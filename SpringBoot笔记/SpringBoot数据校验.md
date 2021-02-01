## SpringBoot 数据校验

## 普通校验

**添加依赖**

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

**创建 ValidationMessages.properties 文件**

```properties
user.name.size=用户名长度介于5到10个字符之间
user.address.notnull=用户地址不能为空
user.age.size=年龄输入不正确
user.email.notnull=邮箱不能为空
user.email.pattern=邮箱格式不正确
```

**创建 User 类，配置数据校验**

```java
public class User {
    
    private Integer id;
    @Size(min = 5, max = 10, message = "{user.name.size}")
    private String name;
    @NotEmpty(message = "{user.address.notnull}")
    private String address;
    @DecimalMiin(value = "1", message = "{user.age.size}")
    @DecimalMax(value = "200", message = "{user.age.size}")
    private Integer age;
    @Email(message = "{user.email.pattern}")
    @NotNull(message = "{user.email.notnull}")
    private String email;
    
    //省略getter/setter
}
```

**创建 UserController**

```java
@RestController
public class UserController {
    @PostMapping("/user")
    public List<String> addUser(@Validated User user, BindingResult result) {
        List<String> errors = new ArrayList<>();
        if (result.hasErrors()) {
            List<ObjectError> allErrors = result.getAllErrors();
            for (ObjectError error : allErrors) {
                errors.add(error.getDefaultMessage());
            }
        }
        return errors;
    }
}
```

## 分组校验

**创建分类接口**

```java
public interface ValidationGroup1 {
}
public interface ValidationGroup2 {
}
```

**在实体类中添加分组信息**

```java
public class User {
    
    private Integer id;
    @Size(min = 5, max = 10, message = "{user.name.size}", groups = ValidationGroup1.class)
    private String name;
    @NotEmpty(message = "{user.address.notnull}", groups = ValidationGroup2.class)
    private String address;
    @DecimalMiin(value = "1", message = "{user.age.size}")
    @DecimalMax(value = "200", message = "{user.age.size}")
    private Integer age;
    @Email(message = "{user.email.pattern}")
    @NotNull(message = "{user.email.notnull}", groups = ValidationGroup2.class)
    private String email;
    
    //省略getter/setter
}
```

**创建 UserController**

```
@RestController
public class UserController {
    @PostMapping("/user")
    public List<String> addUser(@Validated(ValidationGroup2.class) User user, BindingResult result) {
        List<String> errors = new ArrayList<>();
        if (result.hasErrors()) {
            List<ObjectError> allErrors = result.getAllErrors();
            for (ObjectError error : allErrors) {
                errors.add(error.getDefaultMessage());
            }
        }
        return errors;
    }
}
```



## 校验注解

| 校验注解    | 注解的元素类型 | 描述                       |
| ----------- | -------------- | -------------------------- |
| AssertFalse | Boolean        | 被注解的元素值必须为 false |
| AssertTrue  | Boolean        | 被注解的元素值必须为 true  |

