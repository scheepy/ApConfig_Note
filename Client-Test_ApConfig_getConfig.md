# getConfig

---

//====================觀察======================
// 1. cpu + profile 寫進去 d，d.data 吃以外的資料。
// 2. 沒寫 profile 的條件，就會以 profile="default" 去搜尋
// 3. profile 這個字大小寫有別，畢竟"PROFILE"，"profile" 就是兩個 key。
~~// 4. 如果 Cmd 沒有 EX指令，則 CPU 會直接從 SessionObject so.getUniqueId()取得~~

# getUser , getExUser

//1.  getUser 有 profile 的 filter 自由度， getExUser 比 getUser 多了 product, user 兩個自由度。  

