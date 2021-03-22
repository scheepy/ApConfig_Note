# 參考



https://portail.capsana.ca/doc/9.4.5.v20170502/jetty-websocket-client-api.html

[測試環境](wss://test.icetech.com.tw/ss2)



# 問題 

> 問題0 Connection closed: 1006 - Disconnected

```script
Connecting to : ws://echo.websocket.org/
Got connect: WebSocketSession[websocket=JettyAnnotatedEventDriver[com.icetech.ss2.bean.dispatcher.jobHandlers.SimpleEchoSocket@4d329453],behavior=CLIENT,connection=WebSocketClientConnection@2dc654c7::SocketChannelEndPoint@11a8ece6{l=/172.28.11.99:52316,r=echo.websocket.org/174.129.224.73:80,OPEN,fill=-,flush=-,to=63/300000}{io=0/0,kio=0,kro=1}->WebSocketClientConnection@2dc654c7[s=ConnectionState@39144252[OPENING],f=org.eclipse.jetty.websocket.common.io.AbstractWebSocketConnection$Flusher@1d97dd74[IDLE][queueSize=0,aggregateSize=-1,terminated=null],g=Generator[CLIENT,validating],p=Parser@2cfddc18[ExtensionStack,s=START,c=0,len=0,f=null]],remote=WebSocketRemoteEndpoint@7ae4dabd[batching=true],incoming=JettyAnnotatedEventDriver[com.icetech.ss2.bean.dispatcher.jobHandlers.SimpleEchoSocket@4d329453],outgoing=ExtensionStack[queueSize=0,extensions=[],incoming=org.eclipse.jetty.websocket.common.WebSocketSession,outgoing=org.eclipse.jetty.websocket.client.io.WebSocketClientConnection]]

session installed before
onConnet Start
onConnet End
Got msg: Hello
Connection closed: 1006 - Disconnected
```

## 問題2

> `fut.get(2,TimeUnit.SECONDS); // wait for send to complete.`

![image-20210319100701306](C:\Users\vans-icetech\AppData\Roaming\Typora\typora-user-images\image-20210319100701306.png)

### 問題2 整理

~~sendStringByFuture( ) - AsyncMethod , 確實會先打印======, 之後才會打印訊息。~~

~~但是 sendString( ) - Blocking Send , 打印訊息之後~~

上面是有問題的, 首先本基只有一條 執行緒再跑, 之所以會有上面的結果是因為, 我們等伺服器端的回傳訊息。

```java
   	System.out.println("==========onConnet Start==================");
           
        	Future<Void> fut;
			//這時候fut 代表 sendStringByFuture的運算本身, get(TimeOut)代表
			//會等10秒,假設時間到還沒執行完是會拋出 TimeoutException
            fut = session.getRemote().sendStringByFuture("Hello");
            fut.get(10,TimeUnit.SECONDS); // wait for send to complete.

            //fut = session.getRemote().sendStringByFuture("Thanks for the conversation.");
            //fut.get(2,TimeUnit.SECONDS); // wait for send to complete.

//            session.close(StatusCode.NORMAL,"I'm done");
            System.out.println("====================onConnet End======================");


```

  * > 以下取自 Future.get 方法的 Api
     >
     > Initiates the asynchronous transmission of a text message. This method may return before the message is
     >
     > transmitted. Developers may use the returned
     >
     > Future object to track progress of the transmission.

     ![image-20210319103335404](C:\Users\vans-icetech\AppData\Roaming\Typora\typora-user-images\image-20210319103335404.png)

     > > 圖片說明: future物件我們馬上取出來 , 並設定 等10秒, 故此時 Main Thread 會等到結果出來, 也就是印出"等寄送 ~~"那一行之後, 才會繼續打印。
     > >
     > > 關於Future的詳細說明可參照
     > >
     > > [Future介紹](https://popcornylu.gitbooks.io/java_multithread/content/async/future.html)

> ! 訊息

```java
Connecting to : ws://echo.websocket.org
Got connect: WebSocketSession[websocket=JettyAnnotatedEventDriver[myjetty.SimpleEchoSocket@2715f439],behavior=CLIENT,connection=WebSocketClientConnection@54538d07::SocketChannelEndPoint@586adf74{l=/172.28.11.99:53764,r=echo.websocket.org/174.129.224.73:80,OPEN,fill=-,flush=-,to=35/300000}{io=0/0,kio=0,kro=1}->WebSocketClientConnection@54538d07[s=ConnectionState@444d025a[OPENING],f=org.eclipse.jetty.websocket.common.io.AbstractWebSocketConnection$Flusher@7218117f[IDLE][queueSize=0,aggregateSize=-1,terminated=null],g=Generator[CLIENT,validating],p=Parser@2845e382[ExtensionStack,s=START,c=0,len=0,f=null]],remote=WebSocketRemoteEndpoint@26da1881[batching=true],incoming=JettyAnnotatedEventDriver[myjetty.SimpleEchoSocket@2715f439],outgoing=ExtensionStack[queueSize=0,extensions=[],incoming=org.eclipse.jetty.websocket.common.WebSocketSession,outgoing=org.eclipse.jetty.websocket.client.io.WebSocketClientConnection]]
Connection closed: 1006 - Failed to open local endpoint

```



@WebSocket() 標註的物件將會是 Api 裏頭提到的 Socket 物件

四個階段的 Annotation , Connect, Message, Close, Error



Remote EndPoint : 遠端

寄送訊息有四種方式

Session會先去存取 org.eclipse.jetty.websocket.api.RemoteEndpoint，用來寄送訊息。

訊息分成 text / binary websocket messages, websocket ping and pong control frames



Blocking Send Message || return until the send has completed (or has thrown an exception).

直到寄送完成 或是 拋出例外，不然不會""回傳""



```java
RemoteEndpoint remote = session.getRemote();

// Blocking Send of a BINARY message to remote endpoint
ByteBuffer buf = ByteBuffer.wrap(new byte[] { 0x11, 0x22, 0x33, 0x44 });
try
{
    remote.sendBytes(buf);
}
catch (IOException e)
{
    e.printStackTrace(System.err);
}
```

`session.remote.sendXXX()` session存取 RemoteEndpoint 來寄送訊息。

Blocking Send Message 就是一種同步方法

---

# what next ?

##### ` session.close(StatusCode.NORMAL,"I'm done");`

```java
      /**
     * Send a websocket Close frame, with status code.
     * <p>
     * This will enqueue a graceful close to the remote endpoint.
	 * @param statusCode the status code
     * @param reason the (optional) reason. (can be null for no reason)
     * @see StatusCode
     * @see #close()
     * @see #close(CloseStatus)
     * @see #disconnect()
     */
    void close(int statusCode, String reason);
```

