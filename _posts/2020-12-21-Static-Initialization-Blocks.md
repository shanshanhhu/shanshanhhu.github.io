---
title: Static Initialization Blocks
date: 2020-12-21 14:11:25 +0800
categories: [java, grammar]
tags: [grammar]     # TAG names should always be lowercase
---

# Static Initialization Blocks
```
static {
    // whatever code is needed for initialization goes here
}
```
- A class can have any number of static initialization blocks, and they can appear anywhere in the class body.
- The **static** initializer block is only executed when the class is loaded.
- an alternative to static blocks --- a private static method:
```
class Whatever {
    public static varType myVar = initializeClassVariable();

    private static varType initializeClassVariable() {

        // initialization code goes here
    }
}
```
- **Note:** The advantage of private static methods is that they can be reused later if you need to reinitialize the class variable.
# Non-static Initialization Blocks
 There are two ways to initialize an instance variable in a constructor:
- **initializer blocks**
    ```
    {
        // whatever code is needed for initialization goes here
    }
    ```
    The Java compiler copies initializer blocks into every constructor. Therefore, this approach can be used to share a block of code between multiple constructors.
- **final methods**
    ```java
    class Whatever {
        private varType myVar = initializeInstanceVariable();

        protected final varType initializeInstanceVariable() {

            // initialization code goes here
        }
    }
    ```
    A final method cannot be overridden in a subclass. It is especially useful if subclasses might want to reuse the initialization method.
# difference between static and non-static Initialization Blocks
The non-static block Gets called every time an instance of the class is constructed. The static block only gets called once,
 when the class itself is initialized, no matter how many objects of that type you create.

# References
1.[The Java™ Tutorials](https://docs.oracle.com/javase/tutorial/java/javaOO/initial.html)
2.[Spring: Why is afterPropertiesSet() of InitializingBean needed when there are static and non-static initializers in Java?](https://stackoverflow.com/questions/30726189/spring-why-is-afterpropertiesset-of-initializingbean-needed-when-there-are-st)
