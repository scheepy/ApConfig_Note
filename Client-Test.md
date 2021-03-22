# 進度

​	 目前可以登入帳號，也開一個線程定時執行 Ping-Pong，不會斷線。 Binary Message的接收方法也是寫好了，但還沒測。

### 卡在 search測試命令 回傳 cd: -10000

​	

[登入帳號]

```json
{
    "s":"ac",
 	"c":"login",
    "r":"test123",
    "d": {"COMPANY":"ss2","PRODUCT":"ffm","LOGIN":"historydata","PWD":"70547377","APP":"X3","RT_TRADINGS":true,"RT_PERMS":true,"RT_QRYTKS":1}
}
```

---

WebSocket Server's  CallBack msg

`String msg = {"s":"dispatcher","c":"init","d":"5738ae26"}`

此時先將字串轉換成 Document, 再分別取出資訊。



**這兩段你覺得加在專案的哪裡?**

```groovy
compile('com.icetech.ss2:ac-util:0.14.0')
compile('com.icetech.ss2:jobhandler-api:1.4.4.1')
```





```java
//由Rudy 提供的程式碼出現在 Socket Client那一支
//主要用來把密碼加密
if (c.equals("init")) {
                    String d = doc.getString("d");
                    String k = AccountUtils.encryptPasswordPBKDF2(wsPwd);
                    String pwd = AccountUtils.encryptPasswordSHA256(k, d);
                    wsLogin = wsLogin.replace("##pwd##", pwd);
                    
                    if (bConsole) {
                        System.out.println("[Send Login] ");
                        System.out.println(wsLogin+"\r\n");
                    }
                    
                    session.getRemote().sendString(wsLogin);
                    logit("##send login "+wsLogin);
}
```



## **Next 連線之後.密碼加密把資料回傳給Server**

我們把 帳號密碼放進去 @OnWebSocketMessage, 透過 Server傳來的金鑰把密碼加密之後, 把處理過後的密碼寄給Server。

> Server接受查過帳密回傳的訊息

```java
2021/03/19 17:37:44.632 [HttpClient@bcec361-16] INFO  c.i.s.b.d.j.SecureClientSocket - onMessage() - {"s":"ac","c":"login","d":{"SESSION":"ss2:ffm:ws#572150bf34e84e6cb324952ba46c8fba","IDLE_TIMEOUT":720000,"pd.setting":{},"quote.settings":[],"PERMS":{"apconfig":["getcompany","setcompany","delcompany","getproduct","setproduct","delproduct","getuser","setuser","deluser","sub","delexuser","setexuser","getexuser","delexproduct","setexproduct","getexproduct","delexcompany","setexcompany","getexcompany","search"],"basicdata":["lhsi","lhri","lhdi","lhcqyz"],"bginfo":["sub","unsub","enable","disable","loss","del","badge"],"contractspec":["exchangespec","commodityspec","tradingstatus","holiday"],"historydata":["ss","query","hday","hmon","twahcd","cqyz","sectick","otick","eps","greeks"],"k":["sub","unsub"],"o":["sub","unsub","mkt","query","custom"],"og":["custom","mkt","unsub","sub","snapshot"],"optcloud":["getivrank","getoirank"],"optdata":["sub","unsub","mkt"],"pa":["memo","alter","delete","status","history","request","sub"],"q":["sub","unsub","mkt","omk","tbl"],"symbol":["sub","get","unsub"],"symservice":["get","sub","unsub"]},"TKS":["ss2:ffm:ws#572150bf34e84e6cb324952ba46c8fba@XFhuhh8W"],"PING_TIME":60000},"cd":1,"r":"test123","lt":true}

```



## Next onBinaryMessage()

> @OnWebSocketMessage標籤原始碼
>
> 就能接受 Text Message / Binary Message 兩種訊息

```html

/**
 * Annotation for tagging methods to receive Binary or Text Message events.
 * <p>
 * Acceptable method patterns.<br>
 * Note: <code>methodName</code> can be any name you want to use.
 * <p>
 * <u>Text Message Versions</u>
 * <ol>
 * <li><code>public void methodName(String text)</code></li>
 * <li><code>public void methodName({@link Session} session, String text)</code></li>
 * <li><code>public void methodName(Reader reader)</code></li>
 * <li><code>public void methodName({@link Session} session, Reader reader)</code></li>
 * </ol>
 * Note: that the {@link Reader} in this case will always use UTF-8 encoding/charset (this is dictated by the RFC 6455 spec for Text Messages. If you need to
 * use a non-UTF-8 encoding/charset, you are instructed to use the binary messaging techniques.
 * <p>
 * <u>Binary Message Versions</u>
 * <ol>
 * <li><code>public void methodName(byte buf[], int offset, int length)</code></li>
 * <li><code>public void methodName({@link Session} session, byte buf[], int offset, int length)</code></li>
 * <li><code>public void methodName(InputStream stream)</code></li>
 * <li><code>public void methodName({@link Session} session, InputStream stream)</code></li>
 * </ol>
 *
```

###  Binary Message 的範例

> 這些範例都用太多不需要 或是 我看不懂的東西
>
> [~~Binary Message~~ ](https://www.codota.com/code/java/classes/org.eclipse.jetty.websocket.api.annotations.OnWebSocketMessage) 

> Binary Message Version 有兩種參數模式， 哪一種對於 DispatcherUtil.unzip()  比較適合。



```java
//InputStream()  要開多少層才會轉換成byte[] ?
InputStream is; 
byte[] buff = new byte[is.available];
is.read(buff);

// 我記得在哪裡看過 BufferedInputStream 效率更快
BufferedInputStream bis = new BufferedInputStream(is);
bis.read(buff);



```



#### DispatcherUtil.unzip Resource

```java
//這個方法對所有的檔案類型都可以使用 
public static String unzip(byte[] source) {
        byte[] ss2 = source;
        CompressType type = parseCompressType(ss2[0]);

        byte[] b = new byte[ss2.length - 1];
        System.arraycopy(ss2, 1, b, 0, b.length);
        ByteArrayInputStream in = new ByteArrayInputStream(b);
        String msg = null;
        try {
            if (type == CompressType.GZIP) {
                // GZIP
                GZIPInputStream gin = null;
                ByteArrayOutputStream bo = null;
                try {
                    gin = new GZIPInputStream(in);
                    bo = new ByteArrayOutputStream();
                    byte[] tempBytes = new byte[6000];
                    int numBytesRead = 0;
                    while ((numBytesRead = gin.read(tempBytes, 0, tempBytes.length)) != -1) {
                        bo.write(tempBytes, 0, numBytesRead);
                    }
                    msg = new String(bo.toByteArray(), "UTF-8");
                } catch (Exception e) {
                    iLogObj.error("GZIP unzip fail", e);
                } finally {
                    try {
                        gin.close();
                        bo.close();
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                }
            } else if (type == CompressType.MESSAGE_PACK) {
                try {
                    ObjectMapper objectMapper = new ObjectMapper(new MessagePackFactory());
                    TypeReference<JsonNode> typeReference = new TypeReference<JsonNode>() {};
                    msg = objectMapper.readValue(in, typeReference).toString();
                } catch (IOException e) {
                    iLogObj.error("MESSAGE_PACK unzip fail", e);
                }
            } else if (type == CompressType.LZMA2) {
                ByteArrayOutputStream bo = null;
                InputStream inputStream = null;
                try {
                    LZMA2Options LZMA2 = new LZMA2Options();
                    inputStream = LZMA2.getInputStream(in);

                    bo = new ByteArrayOutputStream();
                    byte[] tempBytes = new byte[6000];
                    int numBytesRead = 0;
                    while ((numBytesRead = inputStream.read(tempBytes, 0, tempBytes.length)) != -1) {
                        bo.write(tempBytes, 0, numBytesRead);
                    }
                    msg = new String(bo.toByteArray(), "UTF-8");
                } catch (Exception e) {
                    iLogObj.error("LZMA2 unzip fail", e);
                } finally {
                    try {
                        inputStream.close();
                        bo.close();
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                }
            } else if (type == CompressType.LZMA) {
                ByteArrayOutputStream baos2 = null;
                try {
                    baos2 = new ByteArrayOutputStream();
                    Decoder dec = new Decoder();
                    byte[] props = new byte[5];
                    in.read(props, 0, 5);
                    dec.SetDecoderProperties(props);
                    boolean code = dec.Code(in, baos2, -1);

                    msg = new String(baos2.toByteArray(), "UTF-8");
                } catch (Exception e) {
                    iLogObj.error("LZMA unzip fail", e);
                } finally {
                    try {
                        baos2.close();
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                }
            } else if (type == CompressType.BSON) {
                BasicBSONDecoder decoder = new BasicBSONDecoder();
                BSONObject bson = decoder.readObject(b);
                msg = DocumentHelper.toJson(new Document(bson.toMap()));
            } else {
                msg = new String(source, "UTF-8");
            }
        } catch (Exception e) {
            iLogObj.error("unzip fail", e);
        } finally {
            try {
                in.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

        return msg;
    }
```







## Next Pin-Pone 讓連線持續

1. SpringBoot @Configuraion + @EnableScheduling
2. Open a new Thread

> 我選擇用 `OPen a new Thread`
>
> 這個功能我寫在 **WebSocketClient's  main方法**

```java
			//一定時間未與Dispatcher有訊息傳送,系統會將其斷線(預設10分鐘),建議每5分鐘ping一次
			//Dispatcher cmd範例:{"s":"dispatcher","c":"ping","r":"test123"}
			//定期季送訊息給Server
			new Thread(()->{
				while(true) {
					try {
						Document pingDoc = new Document();
						pingDoc.append("s", "dispatcher").append("c", "ping").append("r", "test123");
						LOG.info("ping to Server {}", DocumentHelper.toJson(pingDoc));
						session.getRemote().sendString(DocumentHelper.toJson(pingDoc));
						Thread.sleep(1000 * 60 * 5);
					} catch (InterruptedException | IOException e) {
						e.printStackTrace();
					}
				}
			}).start();
```

## Next 測試命令

> "ac".equals(s) && "login".equals("c")

```
//成功登入時收到的訊息
{"s":"ac","c":"login","d":{"SESSION":"ss2:ffm:ws#6ce8c135f1cd432f97c9072148c2dd31","IDLE_TIMEOUT":720000,"pd.setting":{},"quote.settings":[],"PERMS":{"apconfig":["getcompany","setcompany","delcompany","getproduct","setproduct","delproduct","getuser","setuser","deluser","sub","delexuser","setexuser","getexuser","delexproduct","setexproduct","getexproduct","delexcompany","setexcompany","getexcompany","search"],"basicdata":["lhsi","lhri","lhdi","lhcqyz"],"bginfo":["sub","unsub","enable","disable","loss","del","badge"],"contractspec":["exchangespec","commodityspec","tradingstatus","holiday"],"historydata":["ss","query","hday","hmon","twahcd","cqyz","sectick","otick","eps","greeks"],"k":["sub","unsub"],"o":["sub","unsub","mkt","query","custom"],"og":["custom","mkt","unsub","sub","snapshot"],"optcloud":["getivrank","getoirank"],"optdata":["sub","unsub","mkt"],"pa":["memo","alter","delete","status","history","request","sub"],"q":["sub","unsub","mkt","omk","tbl"],"symbol":["sub","get","unsub"],"symservice":["get","sub","unsub"]},"TKS":["ss2:ffm:ws#6ce8c135f1cd432f97c9072148c2dd31@KVyKwLdp"],"PING_TIME":60000},"cd":1,"r":"test123","lt":true}
```

### 問題1. 為什麼我的Search指令會出現cd: -10000 ?

```java
//送出的search cmd
{"s": "APConfig", "c": "search", "d": {"company": "ss2", "user": "historydata", "product": "*", "profile": "default"}}

//收到的回覆
2021/03/22 15:47:21.514 [HttpClient@bcec361-15] INFO  c.i.s.b.d.j.SecureClientSocket - onTextMessage() - {"s":"APConfig","c":"search","cd":-10000}
```



