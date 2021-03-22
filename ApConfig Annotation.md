# ApConfig Annotation

### @Qualifier 

兩個類實現了同一個接口, 標註其類別名子, 讓Spring Autowired知道注入哪一個。

### @Autowired

在相互作用的程式中, 這個標籤告知Spring 注入物件於此, 讓這支程式可以用到這個物件。

### @Configuration

SpringBoot 雖然有@SpringBootApplication可以當作註冊檔, 但是為了維護及分工, 我們會額外寫把註冊寫在標有這個標籤的檔案。

### @EnableScheduling

告訴Spring 這支程式產生的Bean, 有方法要安排成定時方法。

定時方法上面會用@Scheduled(XXX) 來標註, XXX: 參數設定。







> 關於 Spring Setter方法使用時機

~~我記得我曾跟機歌討論過 Spring 在XML 註冊中,~~ Setter()方法要配合的是空參建構子,  另一種設定方式是帶有參數的建構子。



> /APConfig/BOCmd.java

SpringBoot 或是 Spring 不需要寫 Setter方法 , 只要寫 @Autowired就可以注入。

