---
layout: post
title: Dalvik、ART虚拟机小结
category: android
---

####关于Dalvik虚拟机

Dalvik虚拟机是google针对移动设备特征而对于JVM的改进型，使其更加试用于移动设备应用程序解释运行。

####JVM与Dalvik虚拟机对比

* 最主要区别Dalvik 基于寄存器，而 JVM 基于栈。基于寄存器的虚拟机虽然降低兼容性但是对于更大的程序来说，虚拟器中解释执行程序的时候，其核心指令分发时间大大缩短，花费的时间更短。故而执行时效率更高。               
* 由于delvik虚拟机指令都大多数都包含寄存器的地址，所以它的指令通常比java虚拟机的指令更长。一般而言基于栈的机器需要更多指令，而基于寄存器的机器指令更长。


* Dalvik更好的安全机制，SandBox机制，每一个App运行在独立的虚拟机实例中，其代码在虚拟机的解释下得以执行。 另外可以让虚拟机更多的依赖操作系统的线程调度机制和管理机制。

* .dev格式文件为利用dx工具合并链接.class文件生成，.class文件实际属于编译过程中间件，整合过程中去除冗余信息，进而减小文件尺寸.提高IO速度，而odex是为了在运行过程中进一步提高性能，对dex文件的进一步优化。 

* 合并常量池，将各个类中常量用一个常量池管理。

* 特殊的虚拟机进程zygote，虚拟机实例的孵化器。如果系统需要一个新的虚拟机实例，它会迅速复制自身，以最快的数据提供给系统。同时对于一些只读的系统库，所有虚拟机实例都和Zygote共享一块内存区域。（copy on write share）共享区间提升跨进程数据共享。

* 基于Trace，针对项目中Hot Method所占程序全部代码比例较低的情况进行优化，只讲那些最常使用的Mehod加载进虚拟机中。


 
####Dalvik GC

GingerBread之前，Dalvik虚拟使用的垃圾收集机制有以下特点：

* Stop-the-word，也就是垃圾收集线程在执行的时候，其它的线程都停止；              
* Full heap collection，也就是一次收集完全部的垃圾；                           
* 一次垃圾收集造成的程序中止时间通常都大于100ms。

在GingerBread以及更高的版本中，Dalvik虚拟使用的垃圾收集机制得到了改进，如下所示：

* Cocurrent，也就是大多数情况下，垃圾收集线程与其它线程是并发执行的；     
* Partial collection，也就是一次可能只收集一部分垃圾；也就是会新开一个线程执行GC操作，但是依旧会很占用内存，过多的GC灰造成卡顿。                 
* 一次垃圾收集造成的程序中止时间通常都小于5ms。






####附带提一下：

ART虚拟机，其采用更加彻底的方式，通过在安装时就将App预编译为机器码，从而提升其加载速度，而省去了多余的加载转换的过程。其副作用在于相较于Dalvik虚拟机中，会导致App占用内存空间变大，但能实现更加流畅的用户体验。


---

[Dalvik虚拟机的启动过程分析](http://blog.csdn.net/luoshengyang/article/details/8885792)