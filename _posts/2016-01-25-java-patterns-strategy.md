---
layout: post
title: Java 模式- 策略
category: java
---

### 关于行为抽象

对于系统的变量的分离抽取，封装，不一定是整个系统完成了之后才能自顶向下提取封装，一半系统完成之后的重构往往需要更大的代价，与之相对更好的机制是经过周密的事前分析，预估以及设定通用的模型架构，其实这些通用的优秀实践也就是设计模式的由来；

针对继承编程在Java中会造成什么问题？一是Java无多继承，二是不灵活，继承是更多的应该定义属性的继承问题，而对于行为的设定以及抽象，就是接口的应用，而如何抽象行为一是需要合理分析，二是需要经验；

当然，若是需要为子类除了定义行为，同时还要为子类提供相应共有公共功能时，则依然需要抽象类；

在可扩展性弹性编程中我们应该始终记住，优秀的软件都是不断变化的，针对变化的灵活的编程才是行之有效的编程；

针对接口编程，行为抽象以及封装，的核心是利用多态机制，利用多态机制，不需要关心子类就是什么实例，只需要知道之类会按照接口定义的协议完成实例对象中的相应行为动作；从这里也可以进一步明白，接口与抽象类的区别，抽象类利用继承是抽取不变量做基类，利用属性以及行为继承，精简代码，以便后续更多的不变量增加时更加方便，同时便于增加子类特性；而**接口在设定时，就是对于行为的一种定义，是对实现类的约束，是针对外部应用所需要的功能约定实现类的实现行为的协议**，是一种自顶向下的抽象协议，一旦实现了接口就相当于签署了协议，后续的实现子类都要按照规定行事；

**接口的核心思想：封装隔离，封装是指行为封装，隔离是指外部行为调用与内部行为实现；也就是外部调用不关心内部行为实现，当系统扩展时，实现修改而外部调用并不会收到影响；**

类一般既包含属性状态，也涵盖类行为，当我们抽取了特定行为接口定义之后，实现接口的类就会进一步细化行为实现，让类的行为动作更加丰满，也更有特性；

### 策略模式

封装定义行为或算法簇，增加系统的灵活性，同时无耦合关系的算法间也可灵活互相替换；

通过解耦算法行为以及算法调用客户端，使二者之间独立，同时使**算法独立于使用算法的客户端**；有别于之前在同一客户端类中包含算法，二者密不可分的高耦合情形；

 策略模式中各个算法之间的关系就类似 耦合时 if / else 之间的关系一样，彼此之间都是平等的；

 策略模式是立足于组织，如何调用算法，增加系统的可扩展性，本质上策略模式是针对相同行为类别的不同具体实现。策略模式最大的劣势就是会随着扩展得越多类的数量就增长得越多，每一个算法策略都是一个 if / else 情形的独立；

####策略模式中的对象间关系之策略上下文Context对象



{:.center}
![strategy pattern](/assets/img/20160120/strategy.png)

以 Context 对象为媒介，算法调用端与算法 彻底解耦，利用Context 为  算法构建准备合适的 属性条件，而算法本身独立起来，只需要关注本身算法的单一实现，逻辑结构清晰起来；而如果去掉 媒介将使算法与 调用客户端依旧耦合在一起，扩展时依旧需要同步修改，一旦调用方参数数目等属性修改，方法就需要同步修改，系统扩展性大大降低；

#### 关于处理扩展算法过程中添加属性条件的思路

一种方式是将 特殊属性直接置放于 Strategy 对象中，调用时直接调用，那么弊端是什么呢？是属性与算法会不断融合，同时个别类特例有属性而通用类属性又是由Context提供的，系统逻辑变得很奇怪；

{% highlight java%}

public class ConcreteStrategy implements Strategy {

    String age;
    public void setAge(String age) {
        this.age = age;
    }

    @Override
    public void doAction(Context context) {
        if (context == null) return;
        System.out.println(context.name + this.age);
    }
}

{% endhighlight %}

另一种方式是依旧将全部的 属性参数放置在 Context对象中，这样做的好处就是 算法完全独立同一，结构清晰，弊端当然也存在，很多不需要那些属性参数的 算法 也将获取这些属性，当然可以利用多态继承实现，但是这样又进一步使类数量膨胀了；但是综合起来依旧是第二种方式比第一种方式更加适合；

{% highlight java%}

public class Context {

    public String name;
    Strategy strategy;

    public void setStrategy(Strategy strategy) {
        this.strategy = strategy;
    }

    void doAlgorithm(){    
        strategy.doAction(this);
    }
}

{% endhighlight %}

---

Quote：

《研磨设计模式》

《HeadFirst 设计模式》