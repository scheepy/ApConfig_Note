> 問題1: handle(SessionObject so, JSONObject message) 的參數 JSONObject message從哪裡來 ?
>
> 回答: Dispatcher 從 Client 納編得來。

client -> Dispatcher :不可缺少欄位 s(Service), c(command), d + data

Dispatcher -> ApConfigJobHandler : 中間產出的 JSONObject_message 比上面至少多新增一個 JSONObject d 一個屬性



>每一筆**CPU + profile** 代表的就是 Config ，Company不得為空，profile 有預設 "default"



handle 裏頭也有 UID，出現在 插入 JSONObject jso.put("EX", strEX )。 strEX 一開始是空的字串，**怎麼拼起來的**?

> handle 第一個switch區間決定 jso 的 EX，但二個區間調用處理函數。
>
> > 1. 假設遇到 getEXUser ，就檢查 jso 的 user 屬性存在與否(jso.has("user"))，若不存在就**送出錯誤訊息**
> > 2. jso.has("user") == true，strEX = jso.getString("user")
> > 3. 依序把 CPU建立 ，最後在前面疊上 "UID"





**Handle**(JobHandler 的入口)

JSONObjec_message_識別字串 s r,c,d 填好之後會生成 影響其他方法的jso

handle中有兩處 switch(cmd)，其中 cmd 是從 JSONObjec.getString("c")，這兩處會同時觸發嗎? ~~我的意思是 假設 cmd ="getExUsersub"~~ 

~~可以觸發兩個 switch區塊嗎?  或是這兩個swich區塊根本就是互斥，進一步的意思是 cmd= "xxEXxxx" 就不會有 cmd=""~~

> > 兩個區塊的變數本身是**有交集**。





**訂閱** 

根據SessionObject.getUniqueId 切割出來的 String[] uids  索引值 1 , 2, 3 去依序查詢 ( 每次更新 Filter 都會 setFilter)。

查詢結果顯示欄位, profile , v(version)。

把這三次查詢結果寄給使用者( sendJSON)。

文字訂閱 ch's name**公司:產品:使用者**的三個頻道。

~~會用使用者自身的訊息往回資料庫裏頭找，把找到的資料**寄給使用者**，並訂閱量身訂做的頻道。~~

~~ch's name**公司:產品:使用者**~~

>1. 回傳 cpu 的 profile, version
>2. 訂閱 cpu



**查詢**

~~根據 測試帳號遮掉 Company 會出錯(cd: -200)和 ApConfigJobHandler 中 alFilter.size() == 0  得出兩者結構一樣但內容物不一樣的東西 。~~

SessionObject.getUniqueId( ) 裏頭有 CPU (Company:Product:User的結構，從索引值 1開始)。

alFilter 是 ArrayList<String> ，每一條的 String 廣義來說都是 CPU，狹義上來說 除去 Company可能不能為空，所以不是CPU。



alFilter 是查詢結果的再一次篩選。 

cpuf(cpu + filter) 寄給使用者 ，讓他知道這次他用了什麼條件搜尋。





searchConfig 中 每一筆 UID + CPU  代表 搜尋結果的資料。

---



如果有公司就加入公司, 依序添加查詢條件, 查出來的結果在按照alFIlter中的特定公司/產品/使用者 做第二次的篩選。

譬如有某公司A, 訂閱就會把包含A公司的資料都算進去寄給使用者。 至於查詢就不會添加額外資料。



**取得**，如果沒有公司這個PK, 就回傳null。



----

**設置**

> setConfig 裏頭 和 getConfig 裏頭 看到 jso.cy and jso.pt 的意思是 排除 company 及 product ?

假設排除 Company，upc="p"，sendChannel = uids[1] : uids[2]





