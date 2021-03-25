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

