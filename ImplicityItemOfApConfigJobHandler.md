### 閱讀前必知

​	IStringCallBack/IDataCallBack<String>

​	IDataCallBack<T> 是針對任意類型資料,不在式 

​	IDocumentCallBack 單一物種
​	
getConfig
​	使用者在AC中設定的存取範圍 alFilter getACInfo()
​		String[] ss = (strEX + "::*").split(":");
​		String company = ss[1];
​		String product = ss[2];
​		String user = ss[3];

#### JSONObject  jso's keys 

​	delConfig/getConfig **"profile", "Ex", "cy", "pt"**
​	queryLog			**"log"**
​	setConfig			**"data"**
​	

```java
delConfig(iSP.remove(Document query))
	jso.getString("profile") 
	jso.getString("Ex") 
	jso.getBoolean("cy")) cy(company) 
	jso.getBoolean("pt")  pt(product)

Table D 欄位(JSONObject jso)
	profile:Non-nullable
	company:Non-nullable
						product:nullable
						user:nullable 
相對應upc
	where company="XXX", product="XXX", user="XXX" -> usc="u"
	where company="XXX", product="XXX", user=""    -> usc="p"
	where company="XXX", product=""   , user=""    -> usc="c"
		
getConfig(iSP.get(Document query))

queryLog
	jso.has("log")
	
searchConfig
	Parameters query.setFilter(Document filter),
	之後再用 filter.append(Document1),
	最後在用 iSP.get(query)，filter 在使用上更有彈性。
	
setConfig
```



```java
//sendErrMsg 包覆 so.sendJson
 sendErrMsg(SessionObject so, String msg, String c, String r, int code) throws Exception
 so.sendJson(this, c, doc, code, r, true)
 so.sendJson(JobHandler handler, String cmd, Document data, int code, String reqId, Boolean isLast)
```





