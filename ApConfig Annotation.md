# ApConfig Annotation

@Qualifier : 兩個類實現了同一個接口, 標註其類別名子, 讓Spring Autowired知道注入哪一個。







> 關於 Spring Setter方法使用時機

~~我記得我曾跟機歌討論過 Spring 在XML 註冊中,~~ Setter()方法要配合的是空參建構子,  另一種設定方式是帶有參數的建構子。



> /APConfig/BOCmd.java

SpringBoot 或是 Spring 不需要寫 Setter方法 , 只要寫 @Autowired就可以注入。

