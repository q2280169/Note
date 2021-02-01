# Thymeleaf技术

Thymeleaf 是一个模板引擎，以显示有应用程序生成的数据或文本。Thymeleaf 命名空间被声明为 th:* 属性，`<html xmlns:th="http://www/thymeleaf.org">` 

## thymeleaf 的标准表达式

**1.简单表达式**

**2.字面量**

**3.文本操作**

**4.算术运算**

**5.布尔运算**

**6.比较和等价**

**7.条件运算操作**

**8.特殊标记**

## thymeleaf 的表达式对象

* #ctx 表示上下文对象
* #vars 表示上下文变量
* #local 表示上下文语言环境
* #request 表示 HttpServletRequest 对象
* #reponse 表示 HttpServletResponse 对象
* #servletContext 表示 ServletContext 对象

## thymeleaf  设置属性

## thymeleaf 模板

* th:insert 用指定片段替换主标签

* th:include 用片段的实际内容替换主标签

* th:replace 用实际片段替换主标签

  