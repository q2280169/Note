# Spring Boot 安全管理

## Spring Security

**添加依赖**

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

**配置**

```properties
spring.security.user.name=aaa
spring.security.user.password=bbb
spring.security.user.roles=admin
```