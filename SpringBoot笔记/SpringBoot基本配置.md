# SpringBoot 基本配置

## 环境配置

**1.安装JDK**

<img src="C:\Users\GaoWei\Desktop\SpringBoot笔记\图片\安装JDK.png" alt="image-20200901185923866" style="zoom:67%;" />

**2.安装IntelliJ IDEA**

<img src="C:\Users\GaoWei\Desktop\SpringBoot笔记\图片\安装IDEA.png" alt="image-20200901190218280" style="zoom:50%;" />

## 创建项目

1.欢迎界面中选择 **Create New Project**，并选择 **Spring Initializr** 类型的项目。

<img src="C:\Users\GaoWei\Desktop\SpringBoot笔记\图片\创建SpringBoot项目_1.png" alt="image-20200901190406463" style="zoom: 33%;" />

2.单击 **Next** 按钮跳转到项目信息的配置界面，根据项目情况设置项目的元数据。

<img src="C:\Users\GaoWei\Desktop\SpringBoot笔记\图片\创建SpringBoot项目_2.png" alt="image-20200901191001831" style="zoom:33%;" />

3.单击 Next 按钮跳转到选择项目依赖的界面。可以手动选择所需要的依赖；也可以不选择任何依赖，在文件 **pom.xml** 中添加所需要的依赖。

<img src="C:\Users\GaoWei\Desktop\SpringBoot笔记\图片\创建SpringBoot项目_3.png" alt="image-20200901191340601" style="zoom:33%;" />

4. 单击 Next 按钮跳转到设置项目名称和项目位置的界面。

<img src="C:\Users\GaoWei\Desktop\SpringBoot笔记\图片\创建SpringBoot项目_4.png" alt="image-20200901191513195" style="zoom:33%;" />

5. 单击 Finish，进入项目开发界面。

## 项目属性配置

SpringBoot中主要通过 application.properties 文件、 application.yml 文件实现对属性的配置。这两种文件格式不同，内容、作用相同。配置文件的默认执行顺序依次是：

* 项目根目录下 config 子目录
* 项目根目录
* 项目 classpath 子目录下的 config 子目录
* 项目 classpath 子目录。

### 自定义属性设置

可以修改 application.properties 文件来自定义项目属性

```properties
# Tomcat配置
server.port=8888
server.error.path=/error
server.servlet.context-path=/

# 自定义属性
helloWorld = Hello SpringBoot!
mysql.jdbcName = com.mysql.jdbc.Driver
mysql.dbUrl = jdbc:mysql://localhost:3306/mytest
mysql.username = root
mysql.password = root
```

## 定制 banner

在 resource 目录下创建一个 banner.txt 文件，在这个文件中写入的文本将在项目启动时打印出来。有以下几个在线网站可供参考：

* http://www.network-science.de/ascii/
* http://www.kammerl.de/ascii/AsciiSignature.php
* http://patorjk.com/software/taag/

<img src="C:\Users\GaoWei\Desktop\SpringBoot笔记\图片\自定义banner.png" style="zoom:50%;" />

## Profile

在 resources 目录下创建两个配置文件 application-dev.properties 和 application-prod.properties，分别表示开发环境中的配置和生产环境中的配置。然后在 application. properties 中进行配置。

```properties
# application-dev.properties
serve.port=8080

# application-prod.properties
serve.port=80

# application.properties
spring.profiles.active=dev
```

## 项目构建与部署

### JAR

**项目配置**

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

**打包**

配置完成后，在当前项目的根目录执行 Maven 命令进行打包：

```
mvn package
```

或者在 IntelliJ IDEA 中单击 Maven Project，找到 Lifecycle 中的 package 双击打包。

<img src="C:\Users\GaoWei\Desktop\SpringBoot笔记\图片\package.png" style="zoom: 67%;" />

打包成功后，在当前项目的根目录下的 target 目录 中就有刚刚打成的 JAR 包。

<img src="图片\jar.png" style="zoom: 67%;" />

**项目运行**

Windows 中运行，直接进入 target 目录中执行如下命令即可启动项目：

```
java -jar jar-0.0.1-SNAPSHOT.jar
```



### WAR

