# ApConfig 整理流程

### ApConfigApplication

作為程式進入口, 確認兩種環境變數, 是不是ss2-ApConfig

#### 問題1: 

> **為什麼要做確認 ?**
>
> CONFIG_KEY, DOCKER_SERVICE_NAME 若是其他東西, 意思是這台主機不是用作 ApConfig用途，**確認主機用途**，有沒有用對的主機開啟 ApConfig服務 ?

### ApConfigConfig

ApConfig這個服務的 Spring's 註冊檔

#### 問題0:

> **DataCoreAPI 用途 ?**
>
> 使用這個Api 之後可以讓 Server 做到 即時訊息、**記憶體快取(memory-cache)**、資料庫存取。
>
> **記憶體快取 ( memory-cahce )** : service之間共享**狀態**、服務更新或是重啟時避免遺失**狀態** , 而且 支援 當使用者改變快取資料時**發出通知**。

#### 問題1 @Bean XXX生成:

> @Bean 生成時 Thread.sleep(sleepTime)

