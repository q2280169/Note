# 邮件发送

## 申请开通 POP3/SMTP 或者 IMAP/SMTP 服务

<img src="C:\Users\GaoWei\Desktop\SpringBoot笔记\图片\QQ邮箱1.png" style="zoom:50%;" />

<img src="C:\Users\GaoWei\Desktop\SpringBoot笔记\图片\POP3SMTP.png" style="zoom:50%;" />

## 环境搭建

```xml
<dependency>
 	<groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-mail</artifactId>
</dependency>
```

```properties
spring.mail.host=smtp.qq.com
spring.mail.port=465
spring.mail.username=576605275@qq.com
spring.mail.password=igprkcldlddxiiae
spring.mail.default-encoding=UTF-8
spring.mail.properties.mail.smtp.socketFactory.class=javax.net.ssl.SSLSocketFactory
spring.mail.properties.mail.debug=true
```

## 发送简单邮件

```java
@Component
public class MailService {
    @Autowired
    JavaMailSender javaMailSender;
    
    public void sendSimpleMail(String from, String to, String cc, String subject, String content) {
        SimpleMailMessage simpMsg = new SimpleMailMessage();
        simpMsg.setFrom(from);
        simpMsg.setTo(to);
        simpMsg.setCc(cc);
        simpMsg.setSubject(subject);
        simpMsg.setText(content);
        javaMailSender.send(simpMsg);
    }
}

// 测试类
@RunWith(SpringRunner.class)
@SpringBootTest
public class SendmailApplicationTests {

    @Autowired
    MailService mailService;
    
    @Test
    public void sendSimpleMail() {
        mailService.sendSimpleMail("576605275@qq.com",
                "576605275@qq.com",
                "576605275@qq.com",
                "测试邮件主题",
                "测试邮件内容");
    }
}
```

<img src="C:\Users\GaoWei\Desktop\SpringBoot笔记\图片\简单邮件.png" style="zoom: 67%;" />

## 发送带附件的邮件

```java
@Component
public class MailService {
    @Autowired
    JavaMailSender javaMailSender;
    
    public void sendAttachFileMail(String from, String to,
                       String subject, String content, File file) {
        try {
            MimeMessage message = javaMailSender.createMimeMessage();
            MimeMessageHelper helper = new MimeMessageHelper(message,true);
            helper.setFrom(from);
            helper.setTo(to);
            helper.setSubject(subject);
            helper.setText(content);
            helper.addAttachment(file.getName(), file);
            javaMailSender.send(message);
        } catch (MessagingException e) {
            e.printStackTrace();
        }
    }
}

// 测试类
@RunWith(SpringRunner.class)
@SpringBootTest
public class SendmailApplicationTests {

    @Autowired
    MailService mailService;
    
    @Test
    public void sendAttachFileMail() {
        mailService.sendAttachFileMail("576605275@qq.com",
                "576605275@qq.com",
                "测试邮件主题",
                "测试邮件内容",
                new File("C:\\Users\\GaoWei\\Desktop\\金5.txt"));
    }
}
```

<img src="C:\Users\GaoWei\Desktop\SpringBoot笔记\图片\附件邮箱.png" style="zoom:50%;" />

## 发送带图片资源的邮件

```java
@Component
public class MailService {
    @Autowired
    JavaMailSender javaMailSender;
    
    public void sendMailWithImg(String from, String to,
                                String subject, String content,
                                String[] srcPath,String[] resIds) {
        if (srcPath.length != resIds.length) {
            System.out.println("发送失败");
            return;
        }
        try {
            MimeMessage message = javaMailSender.createMimeMessage();
            MimeMessageHelper helper = new MimeMessageHelper(message, true);
            helper.setFrom(from);
            helper.setTo(to);
            helper.setSubject(subject);
            helper.setText(content,true);
            for (int i = 0; i < srcPath.length; i++) {
                FileSystemResource res =
                        new FileSystemResource(new File(srcPath[i]));
                helper.addInline(resIds[i], res);
            }
            javaMailSender.send(message);
        } catch (MessagingException e) {
            System.out.println("发送失败");
        }
    }
}

// 测试类
@RunWith(SpringRunner.class)
@SpringBootTest
public class SendmailApplicationTests {

    @Autowired
    MailService mailService;
    
    @Test
    public void sendMailWithImg() {
        mailService.sendMailWithImg("576605275@qq.com",
                "576605275@qq.com",
                "测试邮件主题(图片)",
                "<div>hello,这是一封带图片资源的邮件：" +
                        "这是图片1：<div><img src='cid:p01'/></div>" +
                        "这是图片2：<div><img src='cid:p02'/></div>" +
                        "</div>",
                new String[]{"C:\\Users\\GaoWei\\Desktop\\头像.png",
                        "C:\\Users\\GaoWei\\Desktop\\头像.png"},
                new String[]{"p01", "p02"});
    }
}
```

<img src="C:\Users\GaoWei\Desktop\SpringBoot笔记\图片\图片邮件.png" style="zoom:50%;" />

## 使用 Thymeleaf 构建邮件模板

在 resources/templates 目录下创建邮件模板

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>邮件</title>
</head>
<body>
<div>邮箱激活</div>
<div>您的注册信息是：
    <table border="1">
        <tr>
            <td>用户名</td>
            <td th:text="${username}"></td>
        </tr>
        <tr>
            <td>用户性别</td>
            <td th:text="${gender}"></td>
        </tr>
    </table>
</div>
<div>
    <a href="http://www.baidu.com">核对无误请点击本链接激活邮箱</a>
</div>
</body>
</html>
```

```java
@Component
public class MailService {
    @Autowired
    JavaMailSender javaMailSender;
    
    public void sendHtmlMail(String from, String to,
                             String subject, String content){
        try {
            MimeMessage message = javaMailSender.createMimeMessage();
            MimeMessageHelper helper = new MimeMessageHelper(message, true);
            helper.setTo(to);
            helper.setFrom(from);
            helper.setSubject(subject);
            helper.setText(content, true);
            javaMailSender.send(message);
        } catch (MessagingException e) {
            System.out.println("发送失败");
        }
    }
}

// 测试类
@RunWith(SpringRunner.class)
@SpringBootTest
public class SendmailApplicationTests {

    @Autowired
    MailService mailService;
    
    @Autowired
    TemplateEngine templateEngine;
    
    @Test
    public void sendHtmlMailThymeleaf() {
        Context ctx = new Context();
        ctx.setVariable("username", "sang");
        ctx.setVariable("gender", "男");
        String mail = templateEngine.process("mailtemplate.html", ctx);
        mailService.sendHtmlMail("576605275@qq.com",
                "576605275@qq.com",
                "测试邮件主题",
                mail);
    }
}
```

<img src="C:\Users\GaoWei\Desktop\SpringBoot笔记\图片\模板邮件.png" style="zoom:67%;" />