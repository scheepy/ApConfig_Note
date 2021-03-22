ApConfigConfig_方法解析

### @Bean public IQueueDocument IQueueDocument()

雖然是一個 跑了30次的迴圈，但是如果再在一次迴圈的時候，就正常建立Connection, 則會直接回傳這個物件, 結束這個生成。

但如果第一次沒有建立成功, iQD == null 成立，執行 logit，打印 APConfigConfig IQueueDocument is null, 進行第二次生成, 直至成功。

```java
//public IQueueDocument getQueue(final IConnectionState listener) throws ConfigurationException, MessageException
// getQueue() :Get a queue instance and set a connection status listener.
//final IConnectionState listener: final 物件代表, 初始化之後不可以再參考其他物件， 
//IConnectionState listener: Invoked when connection status is changed.
IQueueDocument iQD = DCAPI().getQueue((Type type, Status state) -> {
                    logit("IQueueDocument: type=" + type + " status=" + state);
                });

2021-03-22 10:48:22.947 [main] INFO  APConfig - IQueueDocument: type=MSG status=CONNECTION_ACTIVATED
    //DataCoreAPI.service(APConfig) 所以打印 INFO **APConfig**
```

>**問題1. 為什麼需要 Thread.sleep(sleepTime)**;

> Remark
>
> 1. 之所以不會 INFO  APConfig - IQueueDocument: type=MSG status=CONNECTION_ACTIVATED 是因為生成 Bean 就會跳立生成函數。



### public void logit(String s)

ILogger( ) 代表 由 @Bean  public ILogger ILogger() throws Exception，要 Spring() 將  **同一支程式生成的 ILogger物件 直接拿來用**。



### ApConfigConfig 註冊檔生成多少個 Bean

```java

    @Bean
    public APConfig APConfig()
       
    @Bean
    public ACCmd ACCmd() 
   
    @Bean
    public BOCmd BOCmd() 
    
    @Bean
    public ConfigDao ConfigDao() 

    //================================================
    @Bean
    public DataCoreAPI DCAPI() 

    @Bean
    public IQueueDocument IQueueDocument() 

    @Bean
    public IPubSub<String> IPubSubString() 

    @Bean
    public IPubSub<Document> IPubSubDocument() 
        
    @Bean
    public IQueueString IQueueString()

    @Bean
    public ISyncPersistent ISyncPersistent() 

    @Bean
    public ISettings ISettings() 

    @Bean
    public ILogger ILogger() throws Exception 


```

