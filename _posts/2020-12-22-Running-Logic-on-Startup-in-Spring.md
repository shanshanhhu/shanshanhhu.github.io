---
title: Running Logic on Startup in Spring
date: 2020-12-22 09:55:30 +0800
categories: [spring, springboot]
tags: [springboot]     # TAG names should always be lowercase
---

# Running Logic on Startup
Firstly, Let's see the problematic code below.
```java
@Component
public class InvalidInitExampleBean {

    @Autowired
    private Environment env;

    public InvalidInitExampleBean() {
        env.getActiveProfiles();
    }
}
```
In this snippet, we're trying to access an autowired field in the constructor, but the Spring bean is not yet fully initialized.
This is problematic because **calling not yet initialized fields will of course result in NullPointerExceptions.**


We have several ways of managing this situation.
## 1.The @PostConstruct Annotation

## 2.The InitializingBean Iterface

## 3. An ApplicationListener

## 4.The @Bean Initmethod Attribute(XML)

## 5.Constructor Injection

## 6.Spring Boot CommandLineRunner

## 7.Spring Boot ApplicationRunner


# References
[Guide To Running Logic on Startup in Spring](https://www.baeldung.com/running-setup-logic-on-startup-in-spring)
