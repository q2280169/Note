# SpringBoot 注解

Spring 容器通过把 Java类注册成 Bean 的方式来管理 Java 类，具体有两种方式：一种是通过 XML 配置，另一种是通过注解。

## Spring 基础注解

**@Component**：放在类的前面，将该类标注成 Spring 的一个普通 Bean。

**@Controller**：标注一个控制器组件类，用来实现自动检测类路径下的组件并将组件自动注册成 Bean

**@Service**：标注一个业务逻辑组件类。

**@Repositoy**：标注一个 DAO 类。

## Spring 常见注解

**@Autowired**：用来实现自动装配，可以标注成员变量、方法、构造函数等。

**@Recource**：标注一个对象的 SET 方法，作用相当于 @Autowired。@Autowired按类型自动注入，而 @Resource 默认按名字自动注入。

**@RequestMapping**：为类或方法指定一个映射路径。

```java
@RequestMapping(value="/getName/{userid}", method=RequestMethod.GET)
public void login(@PathVariable String userid, Model model){
}
```



**@RequestParam**：将请求中带的值赋给被注解的方法参数

```java
public void login(@RequestParam(value="username", required="true") String usernmae){
}
```

**RequestBody**：把请求报文中的正文自动转换成绑定给方法参数的变量字符串。

**ResponseBody**：将内容或 Java 对象转换成响应报文的正文返回。

**@Scope**：定义 Bean 的作用范围。

**@Param**：对参数的解释

**@JoinTable**：表示 Java 类和数据库表的映射关系。

**@Syschronized**：实现 Java 同步机制，相当于加同步锁。

## Spring Boot 的注解

**@Bean**：等价于 XML 配置中的 Bean。

**@Inject**：等价于默认的 @Autowired。

**@RestController**：注解 @Controller 和 @ResponseBody 的合集。

**@SpringBootApplication**：由 @SpringBootConfiguration、@EnableAutoConfiguration、@ComponentScan 组成。

**@Value**：注入Spring Boot配置文件 application.properties 中配置的属性值。