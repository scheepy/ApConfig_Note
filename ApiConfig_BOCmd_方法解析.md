# ApiConfig_BOCmd_方法解析

BOCmd 操作資料庫資料



### 0. 接收 svc,channel  的持久層請求(包成Document)

### 1. 用頻道訂閱代替方法回傳值 

### 2. 新增/ 更新才會寄送個人訊息 

### 3.  iPSD.privateSend(serviceID, doc.getString("response-to"), d);

​	CRUD結果可能會寄到同一個 Service 的不同 Channel(Response-to)

#### **init()**

```java

            Document setting = isetting.get("APConfig", null);//Null代表版本
            重點就是拿出的
                設定有Queue SubCh 就拿出來, 沒有就幫忙設定 
            log.info("BOCmd.SubCh=" + subCh);
     
```



#### **sub()**

```java
Queue subCh="DataEndPoint" 
            // 來自BO的回應
            IDocumentCallback cb = getCB();
            iQD.queueSubscribe(subCh, cb);// Queue SubCh 在 ApConfig

            // 來自jobhandler轉發
            iQD.queueSubscribe("APConfig", "TransferStation", new IDocumentCallback() {
                @Override
                public void onMessage(String serviceId, String channel, Document doc) {
              			//紀錄得到的訊息 && 
                    	//把訊息回傳到 Svc ApConfig
                        log.info("(" + serviceId + " | " + channel + ")" + DocumentHelper.toJson(doc));
                        iPSS.channelSend("APConfig", doc.getString("sc"), doc.getString("msg"));
                }
            });
   
```

#### **unsub() **

```java
iQD.queueUnsubscribe(subCh);
```



#### getDoc(String serviceID, String channel, Document doc)

```java
			//payload 稱作乘載, 當成放資料庫資料的購物車
            Document rPayload = (Document) doc.get("payload"); //doc 從哪裡來 ?
        
{
    payload: {
        company,
        product,
        user,
        profile
    }
}			//就在這裡進資料庫來租廖
            Document dataDoc = configDao.getDoc(company, product, user, profile);
			
			//有撈到資料就存資料, 及一個代表 resultisSuccessful() 的欄位 with value 1
   			// 沒撈到就 log info with value -1

d = {
    request-identify: value(request-identify),
    type: value(type),
    payload: value(payload)
}
			svc,ch= val(response-do),Doc => PSD         
            log.info("BOCmd send d=" + DocumentHelper.toJson(d) + " b=" + isSent);
    
            log.error("BOCmd getDoc", e);//log 拋出的例外
        
```

1.  BOCmd.getDoc(svc, ch, Doc) 其中的Doc 從哪裡來 ? 	不知道, 但應該也是 svc, channel 的 Doc
2. 由Doc 就是 key, 從 DB撈出的就是value, 最後再打額外的訊息, 由 送出去。
3. Doc 有key, 也有送件地址(channel)。 

privateSend 在 Console 不會顯示, 但是 Result.isSuccessful()  is  True, 代表操作仍有某種意義的成功。

#### **getList(String serviceID, String channel, Document doc)**  等價於 getDoc(~~)

```java

{
    payload:{
        company,
        product,
        user,
        profile,
        page(Nullable),
        itemSize(Nullable)
    }
}   

			//進資料庫
            List<Document> list = configDao.getList(company, product, user, profile, page, itemSize);	
			// 有撈到就是 > 0, 沒有就是 -1
        		payload.append("d", list);
                payload.append("result", list.size());
           
d = {
    request-identify: val(),
    type: val(),
    payload:val()
}

```

#### **newDoc(String serviceID, String channel, Document doc)**

```java
{
    payload:{
        company,
        product,
        user,
        profile,
        data(Doc)//欲新增資料
    }
}   



//新建Doc時檢查是否已存在相同的key, 重複回報 -10, 成功 1 並回報(sendClient),添加失敗 -1
Document dataDoc = configDao.getDoc(company, product, user, profile); 
if (dataDoc == null) {
    Result r = configDao.setDoc(company, product, user, profile, data, 0);

成功會轉送兩次訊息, 一次是直送本人, 一次是秘密頻道。
秘密頻道, 你接收到消息別人不知道。

   
```

#### **updateDoc(String serviceID, String channel, Document doc) **

```java
{
    payload:{
        company,
        product,
        user,
        profile,
        data(Doc)//欲更新資料
    }
}   		//v:version, 0:新增資料
            long old_version = rPayload.containsKey("v") ? Long.parseLong(rPayload.getString("v")) : 0L;
            Result r = configDao.setDoc(company, product, user, profile, data, old_version);

			
			//成功 1 並直接通知本人, 失敗 -1
                sendClient(r, old_version, company, product, user, profile);

             
d = {
    request-identify: val(),
    type: val(),
    payload:val()
}
```

#### **delDoc(String serviceID, String channel, Document doc)  **

```java
{
    payload:{
        company,
        product,
        user,
        profile
    }
}   		//直接刪除資料
            Result r = configDao.delDoc(company, product, user, profile);
			//刪除成功 1, 不成功 -1
           
d = {
    request-identify: val(),
    type: val(),
    payload:val()
}
```



#### **sendClient(Result r, long old_version, String company, String product, String user, String profile)**

```java
    long version;
		//公司這個欄位是 NON-Nullable
		//有公司的更新再取出
        if (r.containsKey("v") && (version = r.getLong("v")) > old_version && company != null) {
  
                String sendChannel;//company:product:user
                String cpu; //但頻道到哪一層
              
				
                //寄送純文字訊息, 畢竟只是回報
                iPSS.channelSend("APConfig", sendChannel, "{\"" + cpu + "\":{\"" + profile + "\":" + version + "}}");
                log.info("Channel " + sendChannel + " send v=" + version);
        
```

####  IDocumentCallback getCB()

```java
//填寫一個 Functional Interface, 回傳文件用
IDocumentCallback cb = (String serviceID, String channel, Document doc) -> {
            try {
                log.info("BOCmd recv serviceID=" + serviceID + " channel=" + channel + " doc="
                        + DocumentHelper.toJson(doc));
                //延遲
                if (doc.containsKey("timestamp")) {
                    if (System.currentTimeMillis() - doc.getLong("timestamp") > 10000) {
                        log.info("[Delay]" + doc.get("timestamp"));
                        return;
                    }
                }
				
                //若沒延遲就執行BOCmd指令
                //type = cmd  動作類型, 在去調用對應的方法
                switch (doc.getString("type")) {
                case "getList"//執行這個指令, 會將查詢結果 用頻道通知給通一個Service下的另一個Channel(看Doc)
                case "getDoc"  
                case "newDoc" 
                case "updateDoc"  
                case "delDoc"
                    log.info("BOCmd no such bo cmd");
                }
              //=================================CallBack實體=====================================  
                
  

```

