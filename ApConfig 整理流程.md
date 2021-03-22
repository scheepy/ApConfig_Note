# ApConfig 整理流程

### ApConfigApplication

作為程式進入口, 確認兩種環境變數, 是不是ss2-ApConfig

#### 問題1: 

> 為什麼要做確認 ?
>
> CONFIG_KEY, DOCKER_SERVICE_NAME 若是其他東西, 意思是這台主機不是用作 ApConfig用途，**確認主機用途**，有沒有用對的主機開啟 ApConfig服務 ?

### ApConfigConfig

ApConfig這個服務的 Spring's 註冊檔, 