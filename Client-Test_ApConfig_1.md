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

