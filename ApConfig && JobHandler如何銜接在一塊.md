```java
/*
*	寫在 BOCmd's sub()
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

