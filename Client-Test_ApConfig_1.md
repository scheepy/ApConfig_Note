# 前題

---

> 由 Dispatcher 拼湊的 SessionObject so.getUniqueId( ) 回傳的 ("UID", CPU) ，對於使用者來說，**他們的帳號是什麼 ?**

# 測試帳號:: 結果

---

> 這下面的資料怎麼透過 BO 查到 ?

## 登入帳號 

```json
{
  "s": "ac",
  "c": "login",
  "r": "test123",
  "d": {
    "COMPANY": "ss2",
    "PRODUCT": "ffm",
    "LOGIN": "historydata",
    "PWD": "70547377",
    "APP": "X3",
    "RT_TRADINGS": true,
    "RT_PERMS": true,
    "RT_QRYTKS": 1
  }
}
```

## 登入成功獲得訊息

```json
{
  "s": "ac",
  "c": "login",
  "d": {
    "SESSION": "ss2:ffm:ws#25d86c9646f548aa88dd35b3e079c278",
    "IDLE_TIMEOUT": 720000,
    "pd.setting": {},
    "quote.settings": [],
    "PERMS": {
      "apconfig": [
        "getcompany",
        "setcompany",
        "delcompany",
        "getproduct",
        "setproduct",
        "delproduct",
        "getuser",
        "setuser",
        "deluser",
        "sub",
        "delexuser",
        "setexuser",
        "getexuser",
        "delexproduct",
        "setexproduct",
        "getexproduct",
        "delexcompany",
        "setexcompany",
        "getexcompany",
        "search"
      ],
      "basicdata": [
        "lhsi",
        "lhri",
        "lhdi",
        "lhcqyz"
      ],
      "bginfo": [
        "sub",
        "unsub",
        "enable",
        "disable",
        "loss",
        "del",
        "badge"
      ],
      "contractspec": [
        "exchangespec",
        "commodityspec",
        "tradingstatus",
        "holiday"
      ],
      "historydata": [
        "ss",
        "query",
        "hday",
        "hmon",
        "twahcd",
        "cqyz",
        "sectick",
        "otick",
        "eps",
        "greeks"
      ],
      "k": [
        "sub",
        "unsub"
      ],
      "o": [
        "sub",
        "unsub",
        "mkt",
        "query",
        "custom"
      ],
      "og": [
        "custom",
        "mkt",
        "unsub",
        "sub",
        "snapshot"
      ],
      "optcloud": [
        "getivrank",
        "getoirank"
      ],
      "optdata": [
        "sub",
        "unsub",
        "mkt"
      ],
      "pa": [
        "memo",
        "alter",
        "delete",
        "status",
        "history",
        "request",
        "sub"
      ],
      "q": [
        "sub",
        "unsub",
        "mkt",
        "omk",
        "tbl"
      ],
      "symbol": [
        "sub",
        "get",
        "unsub"
      ],
      "symservice": [
        "get",
        "sub",
        "unsub"
      ]
    },
    "TKS": [
      "ss2:ffm:ws#25d86c9646f548aa88dd35b3e079c278@7SWDYGHi"
    ],
    "PING_TIME": 60000
  },
  "cd": 1,
  "r": "test123",
  "lt": true
}
```



## Search

```json
{
  "s": "APConfig",
  "c": "search",
  "d": {
    "cpuf": {
      "company": "ss2",
      "product": "*",
      "user": "historydata",
      "profile": "default"
    },
    "data": [
      {
        "company": "ss2",
        "product": "ffm",
        "profile": "default",
        "user": "historydata"
      },
      {
        "company": "ss2",
        "product": "ffm",
        "profile": "default5",
        "user": "historydata"
      },
      {
        "company": "ss2",
        "product": "ffm",
        "profile": "default6",
        "user": "historydata"
      },
      {
        "company": "ss2",
        "product": "ffm",
        "profile": "default7",
        "user": "historydata"
      },
      {
        "company": "ss2",
        "product": "ffm",
        "profile": "default77",
        "user": "historydata"
      },
      {
        "company": "ss2",
        "product": "ffm",
        "profile": "default79",
        "user": "historydata"
      }
    ]
  },
  "cd": 0,
  "r": ""
}
```



## sub

```json
{
  "s": "APConfig",
  "c": "sub",
  "d": {
    "c": {
      "default": 9,// profile + version
      "default9": 1
    },
    "p": {
      "default2": 8,
      "default9": 3
    },
    "u": {
      "default": 15,
      "default5": 2,
      "default6": 0,
      "default7": 2,
      "default77": 0,
      "default79": 74
    },
    "s": true
  },
  "cd": 0,
  "r": ""
}

```

## getUser

```json
Document suinner = new Document("data", new Document("say","hello"));
Document setUserQry = new Document("s","APConfig").append("c", "setUser")
	.append("d", new Document("COMPANY", "ss2").append("PRODUCT", "ffm")
	.append("USER", "historydata").append("PROFILE", "0323_01").append("data", suinner)).append("r", "threadID");

{
  "s": "APConfig",
  "c": "getUser",
  "d": {
    "v": 20,
    "profile": "default",
    "data": {
      "data": {
        "say": "hello"
      }
    }
  },
  "cd": 0,
  "r": "threadID",
  "lt": true
}

//================ Observation_1===================================
// 1. getUser 回傳結果中的 s, c, d ， 其中 d 包含 的 v(version), profile, data
// 2. 為什麼我剛剛設定的 profile 沒有複寫上去呢 ? 是因為大小寫有別嗎 ? 我用到的修改指令是 ("PROFILE", "0323_01")


//先更新使用者的訊息
Document suQry = new Document("s","APConfig").append("c", "setUser")
.append("d", new Document("COMPANY", "ss2").append("PRODUCT", "ffm")
.append("USER", "historydata").append("profile", "0323_01")
.append("data", new Document("say","hello"))).append("r", "threadID");

{"s":"APConfig","c":"setUser","d":{"msg":"Update Success."},"cd":0,"r":"threadID","lt":true}
//-------------------

//取得上次更新的那筆資料
Document guQry1 = new Document("s","APConfig").append("c", "getUser")
.append("d", new Document("COMPANY", "ss2").append("PRODUCT", "ffm")
.append("USER", "historydata").append("profile", "0323_01")
.append("data", new Document("say","hello"))).append("r", "threadID");

{"s":"APConfig","c":"getUser","d":{"v":1,"profile":"0323_01","data":{"say":"hello"}},"cd":0,"r":"threadID","lt":true}
//=================== Observation2 ===========================
// 1. 我先更新狀態


```











## setUser  失敗回傳

```json
	Document setUserQry = new Document("s","APConfig").append("c", "setUser")
							.append("d", new Document("profile", "0323_1")).append("r", "threadID");

{
  "s": "APConfig",
  "c": "setUser",
  "d": {
    "msg": "No config data."
  },
  "cd": -18,
  "r": "threadID",
  "lt": true
}

//=================================================
{
  "s": "APConfig",
  "c": "setUser",
  "d": {
    "msg": "Loss token \"d\""
  },
  "cd": -14,
  "r": "threadID",
  "lt": true
}

//=====================總結========================
//結合上述的兩則訊息來看， 要setUser 必須滿足兩點
// 1. 傳送指令必須有識別字串 s, c, d ，其中 d 必須包含 data這個子標籤
// 2. data 描述的 Config 必須在資料庫確實存在

```

## setUser成功指令,回傳

```json
//指令
{
  "s": "APConfig",
  "c": "setUser",
  "d": {
    "COMPANY": "ss2",
    "PRODUCT": "ffm",
    "USER": "historydata",
    "PROFILE": "0323_01",
    "data": {
      "data": {
        "say": "hello"
      }
    }
  },
  "r": "threadID"
}

//回傳
{
  "s": "APConfig",
  "c": "setUser",
  "d": {
    "msg": "Update Success."
  },
  "cd": 0,
  "r": "threadID",
  "lt": true
}

```

