---
title: Java使用网易邮箱向QQ邮箱发送邮件

date: 2020-05-10 16:30:00
tags:
  - 博客
  - 教程
---

使用 Java 应用程序发送 E-mail 十分简单，但是首先你应该在你的机器上安装 JavaMail API 和 Java Activation Framework (JAF) 。

- 您可以从 Java 网站下载最新版本的 [JavaMail](http://www.oracle.com/technetwork/java/javamail/index.html)，打开网页右侧有个 **Downloads** 链接，点击它下载。
- 您可以从 Java 网站下载最新版本的 [JAF（版本 1.1.1）](http://www.oracle.com/technetwork/articles/java/index-135046.html)。

你也可以使用本站提供的下载链接：

[http://static.runoob.com/download/mail.jar](http://static.runoob.com/download/mail.jar)

[http://static.runoob.com/download/activation.jar](http://static.runoob.com/download/activation.jar)

示例：

```java
Java
import java.security.GeneralSecurityException;
import java.util.Properties;
import javax.mail.Authenticator;
import javax.mail.Message;
import javax.mail.MessagingException;
import javax.mail.PasswordAuthentication;
import javax.mail.Session;
import javax.mail.Transport;
import javax.mail.internet.InternetAddress;
import javax.mail.internet.MimeMessage;
import com.sun.mail.util.MailSSLSocketFactory;

public class SendEmail {
    public static void main(String [] args) throws GeneralSecurityException  {
        // 收件人电子邮箱
        String to = "XXXXX@qq.com";

        // 发件人电子邮箱
        String from = "XXXXXX@qq.com";

        // 指定发送邮件的主机为 smtp.qq.com
        String host = "smtp.qq.com";  //QQ 邮件服务器

        // 获取系统属性
        Properties properties = System.getProperties();

        // 设置邮件服务器
        properties.setProperty("mail.smtp.host", host);

        properties.put("mail.smtp.auth", "true");

        // 关于QQ邮箱，还要设置SSL加密，加上以下代码即可
        MailSSLSocketFactory sf = new MailSSLSocketFactory();
        sf.setTrustAllHosts(true);
        properties.put("mail.smtp.ssl.enable", "true");
        properties.put("mail.smtp.ssl.socketFactory", sf);
        // 获取默认session对象
        Session session = Session.getDefaultInstance(properties,new Authenticator(){
            public PasswordAuthentication getPasswordAuthentication()
            {
                return new PasswordAuthentication("XXXXXX@qq.com", "授权码"); //发件人邮件用户名、授权码
            }
        });

        try{
            // 创建默认的 MimeMessage 对象
            MimeMessage message = new MimeMessage(session);

            // Set From: 头部头字段
            message.setFrom(new InternetAddress(from));

            // Set To: 头部头字段
            message.addRecipient(Message.RecipientType.TO, new InternetAddress(to));

            // Set Subject: 头部头字段
            message.setSubject("您有新的消息!");

            // 设置消息体
            message.setText("测试邮件发送成功");

            // 发送消息
            Transport.send(message);
            System.out.println("邮件发送成功...请注意查收！！！");
        }catch (MessagingException mex) {
            mex.printStackTrace();
        }
    }
}
```

博主以网易邮件服务器为例，你需要在登录网易邮箱后台在”设置”=》账号中开启 POP3/SMTP 服务 ，如下图所示：

[![](https://s1.ax1x.com/2020/08/01/aGZIK0.jpg#align=left&display=inline&height=922&margin=%5Bobject%20Object%5D&originHeight=922&originWidth=1033&status=done&style=none&width=1033)](https://s1.ax1x.com/2020/08/01/aGZIK0.jpg)

源码：

```
Java
package email;


import java.security.GeneralSecurityException;
import java.util.Properties;
import javax.mail.Authenticator;
import javax.mail.Message;
import javax.mail.MessagingException;
import javax.mail.PasswordAuthentication;
import javax.mail.Session;
import javax.mail.Transport;
import javax.mail.internet.InternetAddress;
import javax.mail.internet.MimeMessage;
import com.sun.mail.util.MailSSLSocketFactory;

public class SendEmail
{
    public static void main(String [] args) throws GeneralSecurityException
    {
        // 收件人电子邮箱
        String to = "xxx@qq.com";

        // 发件人电子邮箱
        String from = "xxx@163.com";

        // 指定发送邮件的主机为
        String host = "smtp.163.com";  //网易 邮件服务器

        // 获取系统属性
        Properties properties = System.getProperties();

        // 设置邮件服务器
        properties.setProperty("mail.smtp.host", host);

        properties.put("mail.smtp.auth", "true");
        MailSSLSocketFactory sf = new MailSSLSocketFactory();
        sf.setTrustAllHosts(true);
        properties.put("mail.smtp.ssl.enable", "true");
        properties.put("mail.smtp.ssl.socketFactory", sf);
        // 获取默认session对象
        Session session = Session.getDefaultInstance(properties,new Authenticator(){
            public PasswordAuthentication getPasswordAuthentication()
            {
                return new PasswordAuthentication("xxx@163.com", "你的网易邮箱授权码"); //发件人邮件用户名、密码
            }
        });

        try{
            // 创建默认的 MimeMessage 对象
            MimeMessage message = new MimeMessage(session);

            // Set From: 头部头字段
            message.setFrom(new InternetAddress(from));

            // Set To: 头部头字段
            message.addRecipient(Message.RecipientType.TO, new InternetAddress(to));

            // Set Subject: 头部头字段
            message.setSubject("您有新的消息!");

            // 设置消息体
            message.setText("测试邮件发送成功");

            // 发送消息
            Transport.send(message);
            System.out.println("邮件发送成功...请注意查收！！！");
        }catch (MessagingException mex) {
            mex.printStackTrace();
        }
    }
}
```

效果如下：

代码运行之后，后台显示如下证明邮件发送成功

[![](https://s1.ax1x.com/2020/08/01/aGeXTS.png#align=left&display=inline&height=319&margin=%5Bobject%20Object%5D&originHeight=319&originWidth=886&status=done&style=none&width=886)](https://s1.ax1x.com/2020/08/01/aGeXTS.png)

邮箱显示效果：

[![](https://s1.ax1x.com/2020/08/01/aGnmE8.png#align=left&display=inline&height=549&margin=%5Bobject%20Object%5D&originHeight=549&originWidth=1013&status=done&style=none&width=1013)](https://s1.ax1x.com/2020/08/01/aGnmE8.png)

OK 了效果出来就大功告成了

温馨提示：如果找不到邮件的话，可能并不是发送失败，可能放到了垃圾箱里…
