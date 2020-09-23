# Spring Lifecycle

## Lifecycle
```java
public interface Lifecycle {
    void start();
    void stop();
    /**
      *  检查此组件是否正在运行。
      *  1. 只有该方法返回false时，start方法才会被执行。
      *  2. 只有该方法返回true时，stop(Runnable callback)或stop()方法才会被执行。
      */
    boolean isRunning();
}
```


* Lifecycle 接口定义Spring容器对象的生命周期，任何spring管理对象都可以实现该接口。
* 当 ApplicationContext 本身接收启动和停止信号时，spring容器将在容器上下文中找出所有实现了 Lifecycle 及其子类接口的类，并一一调用它们实现的类。
* 常规的LifeCycle接口只是在容器上下文显式的调用start()/stop()方法时，才会去回调LifeCycle的实现类的start stop方法逻辑。并不意味着在上下文刷新时自动启动。
```java
public class Application {
    public static void main(String[] args) {
        ClassPathXmlApplicationContext applicationContext = new ClassPathXmlApplicationContext("classpath*:beans.xml");
        applicationContext.start();
        applicationContext.close();
    }
}
```


## SmartLifecycle
```java
public interface SmartLifecycle extends Lifecycle, Phased {
    /**
      * 如果该 Lifecycle 类所在的上下文在调用`refresh 时,希望能够自己自动进行回调，则返回`true
      * false的值表明组件打算通过显式的start()调用来启动，类似于普通的Lifecycle实现。
      */
    boolean isAutoStartup();
    /**
      * 在实现的关闭过程完成之后，任何实现都必须调用callback的run()方法。这允许在必要时进行异步关闭
      */
    void stop(Runnable callback);
}
```
* SmartLifecycle 用于细粒度控制特定bean的自动启动
* 启动和关闭调用的顺序是很重要的。如果两个对象之间存在依赖关系，依赖类要在其依赖类后启动，依赖类也要在其依赖类前停止。
    然而有时候其之间的依赖关系不是那么直接。你可能仅仅知道某种类型的对象应该在另一类型对象前启动。
    在那些情况下，SmartLifecycle接口定义了另一个选项，在其父类接口Phased中定义命名为getPhase()方法。
    当启动时，有最低phase的对象首先启动，并且停止时，按照相反的顺序结束。

## LifecycleProcessor
* LifecycleProcessor 本身就是 LifeCycle 接口的扩展。它还添加了另外两个方法来响应 spring容器上下文的刷新(onRefresh)和关闭(close)
* Spring 的默认实现是 DefaultLifecycleProcessor



## Method invoke
Spring 容器初始化结束时 finishRefresh()，会调用 getLifecycleProcessor().onRefresh() 方法，
最终调用 DefaultLifecycleProcessor.startBeans(true)
```
if (!autoStartupOnly || (bean instanceof SmartLifecycle && ((SmartLifecycle) bean).isAutoStartup()))
```
该方法会处理: 实现了 SmartLifecycle 接口，并且 isAutoStartup 为true 的Bean