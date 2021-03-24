# 前言

1. Handler裏頭的方法 XXXConfig 是指 XXX方法 ， 譬如說 getConfig 就是指 getUser , getEXUser , etc...

2. SessionObject so.getACSessionSvcInfo(JobHandler this )  其中的 APConfig 下面的 Range 存放的是，
   這個使用者作為管理階層，其管理的對象。

3. Handler 只有在 delConfig, SetConfig, 會透過 ApConfig-Transferstation-Queue 傳給 ApConfig-BOCmd-Sub()

```java
   //cmd 先拚出 (EX,strEX), 在選定執行方法

	switch (cmd) 
            case "getExUser":
            case "setExUser":
            case "delExUser":
          
            case "getExProduct":
            case "setExProduct":
            case "delExProduct":
          
            case "getExCompany":
            case "setExCompany":
            case "delExCompany":
			(EX, "UID":val(Company):val(Product):val(User))
			or remove (EX,~~)

                
  switch (cmd) 
            case "sub":
                doSubAction(so, jso, cmd, r);
            case "search":
                searchConfig(so, jso, cmd, r);
            case "getExCompany":
            case "getCompany":
                jso.put("cy", true);
            case "getExProduct":
            case "getProduct":
                jso.put("pt", true);
            case "getExUser":
            case "getUser":
                getConfig(so, jso, cmd, r);
            case "setExCompany":
            case "setCompany":
                jso.put("cy", true);
            case "setExProduct":
            case "setProduct":
                jso.put("pt", true);
            case "setExUser":
            case "setUser":
                setConfig(so, jso, cmd, r);
            case "delExCompany":
            case "delCompany":
                jso.put("cy", true);
            case "delExProduct":
            case "delProduct":
                jso.put("pt", true);
            case "delExUser":
            case "delUser":
                delConfig(so, jso, cmd, r);

                
```



```java
strEX = "UID:company:product:user::*" // ::* 代表 profile, v
ss = ["UID","company","product","user",*];//這是其中一種可能
or ss = ["UID","","product", "user","3",*], etc...

String[] ss = (strEX + "::*").split(":");
        String company = ss[1];
        String product = ss[2];
        String user = ss[3]; 

alFilter 有三項 company, product, user
for (String filter : alFilter) {
            if (filter.length() > 0) {//filter= "" may be happened
                String[] ss2 = (filter + ":::*").split(":"); // 確保 0, 1, 2 一定代表 company, product, user
                if (company.equals(ss2[0])) {
                    if (ss2[1].length() == 0 || ss2[1].equals(product)) {//alFilter 可能沒有 product
                        if (ss2[2].length() == 0 || ss2[2].equals(user)) {
                            return 0;
                        }
                    }
                }
            }
        }

//acinfoDoc 不只有 alFilter而已
Document acinfoDoc = so.getACSessionSvcInfo(this);
if (acinfoDoc == null || !acinfoDoc.containsKey("range"))
```





```java
 so.setProperties(this, "subCallBack", new IStringCallback() 
 so.getACSessionSvcInfo(JobHandler)//是誰設定alFilter
                  
  List<Document> listData = new LinkedList<Document>(); 
        
        // 如果參數為空的話，就使用$exists，如果是*就不代入以表示通用
           SessionObject\\'s UniqueId 一定有公司項

    
		filter 由 JSONObject jso 產生
		filter 四個欄位都有
            但除了company, profile("default")之外
            user, product 是可能為false
        logit("searchUser filter:" + filter.toJson());

			alFilter 最多
            HashSet<String> hs = new HashSet<>(); // 用來暫存已經比較過的cpu組合，使多個profile可以一次完成
            for (Document dbDoc : iSP.get(query)) {//多筆資料
                StringBuilder sbr = new StringBuilder("UID:");//跟 jso's EX value 一樣開頭
                String c = dbDoc.getString("company");
                if (c != null)
                    sbr.append(c);
                sbr.append(":");//UID::product:user代表沒有公司
                //UniqueID's Company 一定非空, 那為什麼get(query) 公司欄位還有可能空
                String p = dbDoc.getString("product");
                if (p != null)
                    sbr.append(p);
                String u = dbDoc.getString("user");
                if (u != null)
                    sbr.append(":").append(u);
                String uid = sbr.toString();

                if (hs.contains(uid)) {//上一回合加進去了
                    dbDoc.remove("_id");
                    listData.add(dbDoc);
                } else if (ckACRange(uid, alFilter) == 0) { // alFilter 最多是三個分量, 由特定公司/產品/使用者 組成
                    hs.add(uid);
                    dbDoc.remove("_id");
                    listData.add(dbDoc);
                }
            }
```





# 問題

##### [  AC   不知道權限] (#AC)

> 每一個Service，有自己的權限，以APConfig 來說，就有getXXX, getEXooo, etc....。
>
> 但這些 AC 都不知道，他會透過**頻道去問**每個Service ，而Service 再去 **同一個頻道回答*。**
>
> 以 APConfig 這一支服務來說，他就有一支 ACCmd  來負責回應 svc AC。

[ Spring Service(APConfig)](#APConfig)

>APConfigJobHandler 和 APConfig 這支程式的運作關聯不太大。
>
>**訊息上來說**， APConfig 只有在 BOCmd 上面 有來自 JobHandler 的 SetConfig , delConfig。
>
>**權限上來說**，svc AC 並不知道 APConfig 這一支服務下有什麼權限 ? 這裡的 APConfig 是指 Spring Service("APConfig") ，也就是 JobHandler 上面的那一支 Spring Annotation ，而 APConfig 的權限是只 **前端使用者** 可以使用的 JobHandler 中的哪些指令。
>
>

##### 2. JSONObject 用途

JSONObject jso 

​	jso 核對資料庫的篩選資料

​	filter就是SQL's where condition

​	cpuf: searchConfig的查詢結果

​	Session.sendJSON(~~): 往頻道裡送訊息

​	cmd: 作為參數傳進來就直接添加在頻道訊息裡, 並沒有做其他用途。

JSONObject message



SessionObject so 代表使用者連線

​	**使用者資訊**

   + UniqLoginId: 由以下組成
        + company 
        + product 
        + user
+ ACInfo: alFilter (名稱上來看, 用於資料庫搜尋)



# 方法

###### **handle(SessionObject so, JSONObject message)**

​	message 有哪些識別字串 r, c, d，分別代表以下意思

​	[Dispatcher代碼](https://sites.google.com/a/icetech.com.tw/singleserver2/home/dispatcher-setting/code)

	- r : reqID
	- c: cmd
	- d: data(jso)

>handle作為 這個 JobHandler的核心。
>
>Cmd:
>
>​	OOOExUser  => strEx
>
>



###### **ckACRange(String strEX, ArrayList<String> alFilter)**

​	strEX 類似什麼規格,像是有哪些公司的表格

​	alFilter: 透過ACInfo取得使用者權限



###### **searchConfig(SessionObject so, JSONObject jso, String cmd, String r)**

​	**ckACRange**(**資料庫撈得資料**, **使用者資料(ACInfo)**)



###### **getConfig(SessionObject so, JSONObject jso, String cmd, String r)**

​	jso核對資料庫篩選條件. 資料庫結果再由 so往頻道送。

###### **delConfig(SessionObject so, JSONObject jso, String cmd, String r)**

​	除了頻道裡寄送訊息, 也往 **Queue寄送( 直接寄給使用者 )**。  

###### **setConfig(SessionObject so, JSONObject jso, String cmd, String r)**

	1. ACCmd v 代表什麼意思?
	2. 方法本身拿來更新資料用
	3. **Queue寄送**

###### **doSubAction(SessionObject so, JSONObject jso, String cmd, String r)**

​	outDoc

- (c, docCY)

- (p, docPT)
- (u, docUR)

 subScribeChannel (svc ApConfig, SessionObject so, channel, )

​	uids[1]

​	uids[1] + ":" + uids[2]

​	uids[1] + ":" + uids[2] + ":" + uids[3]

​	

# 公司文件

```java
sendJson(JobHandler handler,String sys,
       				 java.lang.String cmd,
                     java.lang.String data,
                     int code,
                     java.lang.String reqId)
送訊息給client,Jobhandler可自行定義code值,來告知client命令執行的結果,其中負值代表錯誤,data請放json格式資料
```

### <a id="AC"> AC是什麼?</a>

