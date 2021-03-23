# 前言

編寫順序就是流程順序



# ApConfig 整理流程

### ApConfigApplication

作為程式進入口, 確認兩種環境變數, 是不是ss2-ApConfig

#### 問題1: 

> **為什麼要做確認 ?**
>
> CONFIG_KEY, DOCKER_SERVICE_NAME 若是其他東西, 意思是這台主機不是用作 ApConfig用途，**確認主機用途**，有沒有用對的主機開啟 ApConfig服務 ?

### ApConfigConfig

ApConfig這個服務的 Spring's 註冊檔

#### 問題0 :

> **DataCoreAPI 用途 ?**
>
> 使用這個Api 之後可以讓 Server 做到 即時訊息、**記憶體快取(memory-cache)**、資料庫存取。
>
> **記憶體快取 ( memory-cahce )** : service之間共享**狀態**、服務更新或是重啟時避免遺失**狀態** , 而且 支援 當使用者改變快取資料時**發出通知**。

#### 問題1 @Bean XXX生成:

> @Bean 生成時 Thread.sleep(sleepTime)

### APConfig

因為這個類實作一個SmartLifecycle，而這個接口讓**Spring在加載初始化Beans之後自動執行 SmartLifeCycle's 的實作方法**。

> 問題1: **有兩種stop()方法, **方法執行的時機
>
> 問題2: **stop()** 為什麼跟start( )方法不對稱, 意思是start() 開的, stop() 沒有跟者關。
>
> 問題3: **log.trace(AtomicBoolean bRunning) **?

### ACCmd

在`APConfig.java`中 start() 裡置入了 `accmd.init() , accmd.sub()`。 

如果subCh是唯一的話, 那 accmd.init( ) 會幫使用者訂閱 這個Group， AC會不定時對Group 發出查詢命令。

Sub() 裡面有寫好自動回復。



### BOCmd

Sub( ) 裏頭分兩個部分，一個是 JobHandler的轉發 ， 另一個是 BO的回應。

BO的回應:  **訂閱 Queue_DataEndPoint**

~~JobHandler 處理的 Client Cmd~~

> 問題1.  是誰在哪個時機透過對 Queue_DataEndPoint 送訊息 ?
>
> ~~有人操作BO ，根據某個機制**BO的回應就是往 DataEndPoint去寄送東西**~~

