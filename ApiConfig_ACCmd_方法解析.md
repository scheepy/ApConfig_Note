ApiConfig_ACCmd_Note1

```java
1. protected String subCh "ac-query-perms"
2. protected String sendSvc "ac"
3. protected String sendCh  "ac-ch"
    ======= 常量=======
4. init()
5. sub()
6. unsub()
```

#### **init()**:操作必不可缺少的資料 subCh, SendSvc, sendCh, SendPerm

```java

            // 載入設定，如果缺失的情況下寫入預設值
            Document setting = isetting.get("APConfig", null);
   
				{
                     SubCh: ac-query-perms,
                     SendSvc: ac,
                     SendCh: ac-ch, 
                     SendPerm: val()
                }

                acCmdSetting.put("SubCh", "ac-query-perms");
                acCmdSetting.put("SendSvc", "ac";
                acCmdSetting.put("SendCh", "ac-ch");

                // 預設權限列表
                String[] perms = { "sub", "getCompany", "setCompany", "delCompany", "getProduct", "setProduct", "delProduct", "getUser", "setUser", "delUser" };
                                 
                ArrayList<String> al = new ArrayList<>(Arrays.asList(perms));
                Document sendPerm = new Document("APConfig", new Document("name", "APConfig").append("perms", al));
                acCmdSetting.put("SendPerm", sendPerm);
                                 {
                                     APConfig:{
                                         name: ApConfig,
                                         perms, val()//預設權限
                                     }
                                 }

                setting.put("ACCmd", acCmdSetting);
                isetting.set("APConfig", setting);



            log.info("ACCmd.SubCh=" + subCh);
            log.info("ACCmd.SendSvc=" + sendSvc);
            log.info("ACCmd.SendCh=" + sendCh);
     
```

#### **Sub()**:groupSubscribe

```java
            if (subCh != null && cb != null) {
                iPSD.groupSubscribe(subCh, cb); // AC使用Group收signal

			//收到之後，往ac, ac-ch 傳訊息
            IDocumentCallback cb = (String serviceID, String channel, Document doc) -> {
                    Document setting = isetting.get("APConfig", null);
                    Document acCmdSetting = (Document) setting.get("ACCmd");
                    Document sendPerm = (Document) acCmdSetting.get("SendPerm");
                    log.info("ACCmd recv serviceID=" + serviceID + " channel=" + channel + " doc="
                            + DocumentHelper.toJson(doc));

                    Document sendDoc = new Document();
                    sendDoc.put("s", doc.get("s"));
                    sendDoc.put("c", "svc.perms.upd");
                    sendDoc.put("r", "apconfig");
                    sendDoc.put("d", sendPerm);
                    sendDoc.put("ts", System.currentTimeMillis());

                    iPSD.channelSend(sendSvc, sendCh, sendDoc);
                    log.info("ACCmd send svc=" + sendSvc + " ch=" + sendCh + " perm=" + DocumentHelper.toJson(sendDoc));
            
            };

        
```

#### **UnSub()**

```java

            if (subCh != null) {
              iPSD.groupUnsubscribe(subCh); // AC使用Group收signal
            
```

