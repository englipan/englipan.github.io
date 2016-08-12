---
layout: post
title:  OkHttp 拦截器应用
category: android
---

OkHttp的强大不需要再说，目前Android上应用最广泛的网络请求Client，其拦截器Interceptor应用广泛，值得指出的是Okhttp对于拦截器也采用了分层的概念，分为应用拦截器，和网络拦截器，这里不作引申，主要说拦截器的使用，拦截器最广泛的使用应该是输出请求日志输出；用于调试网络请求非常方便，项目中最初使用了`facebook-stetho`结合Chrome监控网络，但是该库目前还存在一些Bug，如同时发起多个请求，后面的请求显示将覆盖前面请求，导致前面请求无法输出；

由于一些原因便想着利用OkHttp的拦截器输出响应，而OkHttp的Wiki中也有范例展示如何利用：

{% highlight java %}

class LoggingInterceptor implements Interceptor {
  @Override public Response intercept(Interceptor.Chain chain) throws IOException {
    Request request = chain.request();

    long t1 = System.nanoTime();
    logger.info(String.format("Sending request %s on %s%n%s",
        request.url(), chain.connection(), request.headers()));

    Response response = chain.proceed(request);

    long t2 = System.nanoTime();
    logger.info(String.format("Received response for %s in %.1fms%n%s",
        response.request().url(), (t2 - t1) / 1e6d, response.headers()));

    return response;
  }
}


{% endhighlight %}

看着很清晰，想着这只有打印了请求与响应url以及耗时，没有响应内容不是没什么用么，于是很简单的自作聪明加了

`response.body().string()`

尝试直接输出响应，紧接着发现Log是输出了，可是最后数据却没了，也就是明明response返回了数据，打印了之后，app中需要接受数据的地方却接受不到数据了，而之前却是可以正常接受数据的，想想只能自己看看源码找找原因了：看了看 body.String()函数的输出，是利用字符流进行输出的，猜想应该是流数据读取之后，二次读取的问题，也就是输出了流输数据导致app中需要数据返回的地方没有JSON数据读取，看看源码：

Retrofit中 GsonConvert 的Conver()过程，果然是 利用了 response.charStream(),流的封装读取，验证了猜想，也就是要利用拦截器输出的过程变成如何二次读取流数据，在首次读取了流数据之后在拦截器中返回一个新的respon对象，在读了`facebook-stetho`拦截器的函数源码之后，自己改造了如下拦截器，实现了需求：



{% highlight java %}

/**
 * Created by lee on 16/8/9.
 * 处理使用okHttpclient拦截器,打印请求与响应日志
 */
public class ConnectStateInterceptor implements Interceptor{

    @Override
    public Response intercept(Chain chain) throws IOException {
        final Request req = chain.request();

        Logger.d(String.format(Locale.CHINA, "Request Url >>>%s\nHeaders:\n%s\n-----End-----End-----", req.url(), req.headers()));

        Response res = chain.proceed(req);
        ByteArrayOutputStream copyOut = cloneReaderInputStream(res);
        if (copyOut != null) {
            res = res.newBuilder()
                    .body(new ForwardingResponseBody(res.body(), new ByteArrayInputStream(copyOut.toByteArray())))
                    .build();
            IOUtil.closeQuietly(copyOut);
        }
        return res;
    }

    private ByteArrayOutputStream cloneReaderInputStream(Response response) {
        final ResponseBody body = response.body();
        try {
            final ByteArrayOutputStream copyOut = new ByteArrayOutputStream();
            IOUtil.copy(body.byteStream(), copyOut);
            final Charset charset = body.contentType() != null ? body.contentType().charset(UTF_8) : UTF_8;
            Logger.d(String.format(Locale.CHINA, "Receive Response on url --> %s\nResponseCode:%s\nHeaders:\n%s\nResponse JSON:\n%s\n\n>>>>>Over>>>>>Over>>>>>",
                    response.request().url(), response.code(), response.headers(),
                    new String(copyOut.toByteArray(),charset)));
            return copyOut;
        } catch (IOException e) {
            e.printStackTrace();
        }
        return null;
    }

    private static class ForwardingResponseBody extends ResponseBody {
        private final ResponseBody mBody;
        private final BufferedSource mInterceptedSource;

        public ForwardingResponseBody(ResponseBody body, InputStream interceptedStream) {
            mBody = body;
            mInterceptedSource = Okio.buffer(Okio.source(interceptedStream));
        }

        @Override
        public MediaType contentType() {
            return mBody.contentType();
        }

        @Override
        public long contentLength() {
            return mBody.contentLength();
        }

        @Override
        public BufferedSource source() {
            // close on the delegating body will actually close this intercepted source, but it
            // was derived from mBody.byteStream() therefore the close will be forwarded all the
            // way to the original.
            return mInterceptedSource;
        }
    }
}

{% endhighlight %}