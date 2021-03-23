##### 來自JobHandler 文件

package : com.icetech.ss2.bean.dispatcher.jobHandlers

| method name                                      | 是否override | return | 執行時機                 | 說明               |
| ------------------------------------------------ | ------------ | ------ | ------------------------ | ------------------ |
| handle(SessionObject session,JSONObject message) | Yes          | void   | AC Job handler檢查權限後 | 處理client command |

``` java
sendJson(JobHandler handler,String sys,
           java.lang.String cmd,
           java.lang.String data,
           **int** code,
           java.lang.String reqId) 送訊息給client,Jobhandler可自行定義code值,來告知client命令執行的結果,其中負值代表錯誤,data請放json格式資料 若data來源為Document,建議使用DocumentHelper.toJson(doc)轉換成json字串,可避免Document.toJson()產生"numberlong"字


```





```java
/*
*	寫在 BOCmd's sub()
	當一個ClientA setConfig or DeleteConfig 時候，能做到即時通知
*/  

// 來自jobhandler轉發
            iQD.queueSubscribe("APConfig", "TransferStation", new IDocumentCallback() {
                @Override
                public void onMessage(String serviceId, String channel, Document doc) {
                    try {
                        log.info("(" + serviceId + " | " + channel + ")" + DocumentHelper.toJson(doc));
                        iPSS.channelSend("APConfig", doc.getString("sc"), doc.getString("msg"));
                    } catch (MessageException e) {
                        log.error("e:" + e);
                    }
                }
            });

```

