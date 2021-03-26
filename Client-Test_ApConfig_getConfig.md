profile 有提供預設值，所有方法除去 sub 不需要之外，其他方法都有提供。

general-cmd 直接取 SessionObject so.getUniqueId( ) 取得 cpu，
唯一的自由度 profile，也有預設值。

Ex-cmd 比 general-cmd 提供額外的 pu 自由度，但沒有提供預設值，若不提供pu會寄送錯誤訊息給使用者。

# getConfig 

//====================觀察======================

會將

// 1. cpu + profile 寫進去 d，d.data 吃以外的資料。
// 2. 沒寫 profile 的條件，就會以 profile="default" 去搜尋
// 3. profile 這個字大小寫有別，畢竟"PROFILE"，"profile" 就是兩個 key。
~~// 4. 如果 Cmd 沒有 EX指令，則 CPU 會直接從 SessionObject so.getUniqueId()取得~~

# getUser , getExUser

//1.  getUser 有 profile 的 filter 自由度， getExUser 比 getUser 多了 product, user 兩個自由度。  

