# 前題

---

> 由 Dispatcher 拼湊的 SessionObject so.getUniqueId( ) 回傳的 ("pdpf", CPU) ，對於使用者來說，**他們的帳號是什麼 ?**
> **帳號就是CPU**

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

## 額外登入訊息

```json
"scheepyWant": {
      "alFilter": [],
      "so.getUniqueId": "pdpf:ss2:ffm:historydata",
      "so.getACSessionSvcInfo(APConfigJobHandler)": null,
      "so.getPermissions": null,
      "so.getLoginUserId()": "historydata"
    }
```



## Search

```json
1. 搜尋條件大小寫有別
2. 
```



## sub

```json
//sub 的 Query不能缺少d，但裏頭的資料的資料填什麼都無所謂。
//因為sub這東西的Filter就是使用 SessionObject so.getUniqueId()中的 CPU，然後一層一層的疊上去，如下列 Step1 ~ 3。
Step 1. d:{"company":"ss2","product":{"$exists":false},"user":{"$exists":false}},"cd":0,"r":"查詢公司"}
Step 2. d:{"company":"ss2","product":"ffm","user":{"$exists":false}},"cd":0,"r":"查詢產品"}
Step 3. d:{"company":"ss2","product":"ffm","user":"historydata"},"cd":0,"r":"查詢使用者"}

//======== sub 結果
// c:company, p:product, u:user
{
  "s": "APConfig",
  "c": "sub",
  "d": {
    "c": {
      "default": 9,
      "default9": 1
    },
    "p": {
      "default2": 8,
      "default9": 3
    },
    "u": {
      "0323_01": 1,
      "0324_HelloWorld": 15,
      "default": 23,
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



## 

```json

```

## 

```json


```

## BOCmd 接收到 APConfigJobHandler 轉發的訊息

```JSON
//我在Client端setConfig， APConfigJobHandler確實把東西透過 svc_APConfig:Queue_TransferStation 傳給 BOCmd

2021-03-24 12:13:27.856 [Thread-8] INFO  APConfig - (dispatcher@ff4eb9fcea1149458083dab093b168ee | TransferStation){"sc":"ss2:ffm:historydata","msg":"{\"u\":{\"0324_HelloWorld\":1}}"}

```

