---
layout: post
title: 关于编程学习的一点思考
category: android
---

最近开始逐渐接触公司的CC产品源码，开始跟着他们一起熟悉业务逻辑，牛人接触多了，总是难免生出一些感触，我们究竟该学习些什么？

虽说从第一天入门开始这个问题就应该要解决，然而编程作为一门实践技能，所有的教科书都告诉我们先动手，边做边学，做了就明白了，最后语言，算法学了一大堆，但是好像到今天自己依旧不知道真正该学习什么，或者说好像抓到点边知道点皮毛，但是从来没有真正理清思路过，不过现在我好像真的知道了些什么……

先来几个最近比较有感触的场景：

场景一：

* P：Jessie,我最近大项目遇到瓶颈了，做到一个核心点没有着手点了？      
* J: 什么瓶颈，做什么？你理清需求了吗？      
* P：我想做一个基于用户偏好的随机推荐算法，就是记录用户的一些偏好，给偏好设定权值，做随机推荐       
* J: 什么叫偏好设定权值做随机推荐？偏好权值不是固定的吗？怎么又基于权值搞随机？怎么随机？你确定你真的知道自己要做什么了吗？你得需求真的准确吗？准确的话，你给我解释下


场景二：

* P: Jessie,最近我在做我们产品里面的图像生成算法的改进……      
* J: 为什么做这个呢？
* P：目前产品遇到了一个问题，上下滑动的时候，用户头像的自动生成图片会每次都需要生成，以前的算法好像不是很好       
* J: 你确定是图片算法影响这个性能问题？这个性能问题跟很多方面都有关系，跟IO，缓存都有关，如果图片算法不是核心影响因素，你改进的效果怎么保证？具体是哪个方面是核心影响因素还是先确定比较好

场景三：

*  P: Brother Hua,IM聊天这一块，现在遇到一个Bug，不是很好调，这块是你做的，帮忙分析一下,Bug的情况是这样的……        
*  H: Bug出现了先要分析，这个Bug有两种可能要么是后台没推送更新过来，要么是前台我们处理的逻辑没有考虑全面，你查一下对应的API接口的Log,先确定是前台还是后台的问题，然后做相应的处理，5分钟就能解决

编程的学习最后着重的还是思维能力以及分析解决问题的能力的提升，我想这也是暗时间作者着重行文强调思维能力培养的原因。其实对于编程，有的同学在不停的猜测验证，在验证中分析，有的同学先分析问题，如抽丝拨茧，有的同学写出来的程序自己其实讲不出所以然。

于我而言，分析能力这样内功的修炼是我所更加佩服的，也是我更加偏好的，多问几个为什么，直指问题的核心，这不是敏捷开发修炼之道中所强烈推荐的5个为什么方法么。记录这点一方面是理清自己的前进方向，更重要的是告诫自己不要流于形式，过度在意外功的使用，大部分时候更多的是万变不离其宗，外部框架的使用是一通百通，要沉下心，慢慢研究自己该研究的东西，找准方向前进这些才是我目前最应该做的，新奇的事物无穷无尽，然而引发的根源却就那么几根，顺藤摸瓜，抽丝拔茧，找到根节点用心专研，我想这就是我该做的。