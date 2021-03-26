# delExUser 遇上 ss2: : abc 【卒】

>登入帳號 ss2:ffm:historydata
>
>刪除Config ss2: :abc
>
>因為 User 不同名子，所以只能用 delExUser 來刪除，且 沒有 Company, Product 指令故， 會將 EX_語句，用分號切割取其索引值 1, 2, 3。
>
>問題是現在 **EX_語句  UID: SS2: {"$exists": false} : abc**，這種情況用 **:** 去切，得出 **SS2**, **{"$exists"**, **false}**，這種有問題的送進 DB 就沒辦法得到 **剔除 product ** 的功能。
>
>

