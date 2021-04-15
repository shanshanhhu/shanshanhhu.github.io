---
title: SpringBoot
date: 2021-3-3 13:31:30 +0800
categories: [spring, springboot]
tags: [springboot]     # TAG names should always be lowercase
---

# @RestController
@RestController返回JSON 或 XML 形式数据, 只返回对象，对象数据直接以 JSON 或 XML 形式写入 HTTP 响应(Response)中,
而不是想单独使用@Controller时，会返回一个页面。@RestController = @Controller + @ResponseBody。
![](/assets/img/sample/spring-mvc.png)
![](/assets/img/sample/spring-restful.png)
@ResponseBody 注解的作用是将 Controller 的方法返回的对象通过适当的转换器转换为指定的格式之后，写入到HTTP 响应(Response)对象的 body 中，通常用来返回 JSON 或者 XML 数据，返回 JSON 数据的情况比较多。

# Spring IoC & AOP
1. IoC (Inversion of Control)
- DI (Dependency Injection)
3.
