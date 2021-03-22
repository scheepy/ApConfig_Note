# APConfig _方法解析

**實現接口SmartLifeCycle**，需要實做什麼方法

```java
//SmartLifeCycle 自己的方法

/*Returns true if this Lifecycle component should get started automatically by the container at the time that the containing ApplicationContext gets refreshed.
A value of false indicates that the component is intended to be started through an explicit Lifecycle.start() call instead, analogous to a plain Lifecycle implementation.
*/
boolean isAutoStartup();

void stop(Runnable callback);

//SmartLifeCycle 繼承另外兩個接口的方法
//LifeCycle
void start();
void stop();

//這個SmartLifeCycle Component 有沒有在運行
boolean isRunning();

//Phased
int getPhase();
```

看起來沒打算實作的方法 `getPhase(), isAutoStartup()`

> 問題1: **有兩種stop()方法, **方法執行的時機
>
> 問題2: **stop()** 為什麼跟start( )方法不對稱, 意思是start() 開的, stop() 沒有跟者關。
>
> 問題3: **log.trace(AtomicBoolean bRunning) **?

