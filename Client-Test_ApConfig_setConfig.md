# 指令 && 參數

1. 參數和指令都大小寫有別
2. cy , pt 這兩個 boolean 代表，是不是 Company 或是 Product 的指令，如果是則為**true**，反之 **false**。
   而這影響的
3. 指令必要格式 s, c, d.data， d(empty or not).data(empty or not) , e.g. `d: {profile: "profile_value", "data":{}  }` or
   `d:{ data:{} }`
4. profile不一樣就是不一樣的 data
5. Ex 和 一般指令 之間的差異在於，前者比後者多了 product, user 的自由度，但不論是哪一者 Company都是透過 SessionObject so.getUniqueId( ) 來限制的。



# setUser  失敗回傳

````JSON
//setUser 用的和 BOCmd的newDoc(ConfigDao setDoc) 也是DataCore APi 的 ISyncPersisten isp.merget()
//理應該可以新增使用者

//=====================總結========================
//setUser 必須滿足兩點
// 1. 傳送指令必須有識別字串 s, c, d ，其中 d 必須包含 data這個子標籤
// 2. data 描述的 Config 必須在資料庫確實存在 ?
````

# setUser  , setExUser

```json
// setUser's filter
// 在 setUser 下只有 profile 的自由度
{
  "d": {
    "company": "ss2",
    "product": "ffm",
    "user": "historydata",
    "profile": "default"
  }
}
 
// setExUser's filter
// 在 setExUser 下 有 Product, User, Profile 的自由度
{
  "d": {
    "company": "ss2",
    "product": "candyhouse",
    "user": "scheepy",
    "profile": "cpupfile"
  }
}
    
```

# setExCompany

```json
{"s":"APConfig","c":"setExCompany","d":{"msg":"Update Success."},"cd":0,"r":"","lt":true}
{"s":"APConfig","c":"setExCompany","d":{"company":"ss2","product":{"$exists":false},"user":{"$exists":false},"profile":"default"},"cd":0,"r":"查看setConfig's Filter","lt":true}

{"sc":"ss2","msg":"{\"c\":{\"default\":10}}"}
```



# setConfig程式碼留存

```java
/**
* setUser 和 setExUser 都可以新增使用者，後者比前者多了 Product, User 的自由度
* pu:candyhouse:scheepy 
* 
* @cmd {"s": 服務名稱, "c": 指令, "d":{profile or cpu , "data":{其他資料(可空) }}}
* Notice that s, c, d.data 的結構不可少
*/

//只有profile | "msg":"No config data." 
//可從 APConfigJobHandler的 Method setConfig 裏頭看到 d.data 為必要訊息。
Document onlypfile = new Document("s","APConfig").append("c", "setExProduct")
	.append("d", new Document("profile", "只有profile1").append("product", "spy's product 2").append("data", new Document())).append("r", "只有profile");
sess.getRemote().sendString(DocumentHelper.toJson(onlypfile));

//沒有EX的情況下，只有profile的自由度
Document onlydata = new Document("s","APConfig").append("c", "setUser")
	.append("d", new Document("product","candyhouse").append("user", "scheepy").append("data", new Document("descrip1","onlydata1"))).append("r", "onlydata");
sess.getRemote().sendString(DocumentHelper.toJson(onlydata));

//all | "d":{"msg":"No config data."},"cd":-18 | 錯誤原因是因為 d 裏頭不包含 data 子標籤
Document cpupfile = new Document("s","APConfig").append("c", "setUser")
 	.append("d", new Document("profile", "cpupfile").append("company", "sheepOne")
  	.append("product", "candyHouse").append("user","scheepy")).append("r", "cpupfile");
sess.getRemote().sendString(DocumentHelper.toJson(cpupfile));

//setUser + d.data
Document cpupfile = new Document("s","APConfig").append("c", "setUser")
    .append("d", new Document("profile", "setUser + d.data").append("company", "sheepone")
    .append("product", "candyhouse").append("user", "scheepy")
    .append("data", new Document())	).append("r", "setUser + d.data");
sess.getRemote().sendString(DocumentHelper.toJson(cpupfile));

//setUser + d(empty).data
Document cpupfile = new Document("s","APConfig").append("c", "setUser")
    .append("d", new Document().append("data", new Document("description","d裏頭沒有profile也沒有cpu")))
    .append("r", "setUser + d(empty).data");
sess.getRemote().sendString(DocumentHelper.toJson(cpupfile));

//setUser + d(empty).data(empty)
Document cpupfile = new Document("s","APConfig").append("c", "setUser")
    .append("d", new Document().append("data", new Document("","")) ).append("r", "setUser + d(empty).data(empty)");
sess.getRemote().sendString(DocumentHelper.toJson(cpupfile));

//setExUser	
Document cpupfile = new Document("s","APConfig").append("c", "setExUser")
    .append("d", new Document("profile", "cpupfile").append("company", "sheepone")
    .append("product", "candyhouse").append("user","scheepy").append("data", new Document())).append("r", "cpupfile");
sess.getRemote().sendString(DocumentHelper.toJson(cpupfile));



/****************************************
 * 造出 getConfig , search, etc... 需要的資料
 * setExUser's "product":"myproduct", "user":"free_user"
 * **************************************/
					
Document sugQry = new Document("s","APConfig").append("c", "setUser")
    .append("d", new Document("profile","setUser 造出的第2筆").append("data", new Document()));
sess.getRemote().sendString(DocumentHelper.toJson(sugQry));
				
Document sugQry = new Document("s","APConfig").append("c", "setExUser")
.append("d", new Document("profile","setExUser 造出的第2筆").append("product", "myproduct")
.append("user", "free_user").append("data", new Document()));
sess.getRemote().sendString(DocumentHelper.toJson(sugQry));


//==================setExCompany========================
/***************************
* 查看 setExCompany的filter
***************************/
				
Document secy = new Document("s","APConfig").append("c", "setExCompany")
    .append("d", new Document("company","ss3").append("product", "myproduct").append("user", "free_user").append("data", new Document()));
sess.getRemote().sendString(DocumentHelper.toJson(secy));
				
Document decy = new Document("s","APConfig").append("c", "delCompany")
    .append("d", new Document("company","ss2").append("profile", "default9").append("data", new Document()) );
sess.getRemote().sendString(DocumentHelper.toJson(decy));
				
//刪除產品
Document depdct = new Document("s","APConfig").append("c", "delExProduct")
    .append("d", new Document("company","ss2").append("product", "ffm").append("profile", "沒有輸入使用者，我要搭配測試getExProduct").append("data", new Document()));
sess.getRemote().sendString(DocumentHelper.toJson(depdct));
				
//刪除使用者
Document temp = new Document("product", "").append("user", "abc");
Document data = new Document().append("d", temp);
Document depdct = new Document("s","APConfig").append("c", "delExUser").append("d", temp);
System.out.println(DocumentHelper.toJson(depdct));
sess.getRemote().sendString(DocumentHelper.toJson(depdct));
				
Document gepdct = new Document("s","APConfig").append("c", "delUser")
    .append("d", new Document("company","ss2").append("product", "myproduct").append("user", "free_user").append("profile", "setUser 造出的第2筆").append("data", new Document()));
sess.getRemote().sendString(DocumentHelper.toJson(gepdct));
			
//++++++++++++++++++++++++
//按照 EX展開key的指令
Document delAbc = new Document("s", "APConfig").append("c", "delExUser")
    .append("d", new Document("company","ss2").append("product", new Document("$exists", false)).append("user", "abc") );
System.out.println(DocumentHelper.toJson(delAbc));
				
//EX指令展開
Document showdelExC = new Document("s", "APConfig").append("c", "delExProduct")
    .append("d", new Document());
sess.getRemote().sendString(DocumentHelper.toJson(showdelExC));
				
//把d的部分分開來
Document stepDel = new Document("s","APConfig").append("c", "delExUser");
stepDel.append("d", new Document().append("company", "ss2").append("product", new Document("$exists",false)).append("user", "abc")
               .append("data", new Document()));
System.out.println(DocumentHelper.toJson(stepDel));
System.out.println("=====================");
sess.getRemote().sendString(DocumentHelper.toJson(stepDel));
sess.getRemote().sendString(stepDel.toJson());
				
		
```

