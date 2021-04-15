---
title: Scala Learning
date: 2021-03-29 21:11:22 +0800
categories: [scala, language]
tags: [language]
---
[A SCALA TUTORIAL FOR JAVA PROGRAMMERS](https://docs.scala-lang.org/tutorials/scala-for-java-programmers.html#introduction)
# First example
```scala
object HelloWorld {   // singleton object, that is a class with a single instance.
  def main(args: Array[String]): Unit = {   // Unit: the return type meaning the main method does not return a value.
    println("Hello, world!")
  }
}
```
scala has no static methods or fields. Rather than defining static members, the Scala programmer declares these members in singleton objects.
- compile
> scalac HelloWorld.scala
- run
> scala HelloWorld

# Use java packages
```scala
import java.util.{Date, Locale}
import java.text.DateFormat._  // '_' means import all members of a package or class

object FrenchDate {
  def main(args: Array[String]): Unit = {
    val now = new Date
    val df = getDateInstance(LONG, Locale.FRANCE)  // getDateInstance()--->DateFormat
    println(df format now)  // df.format(now)
  }
}
```

# Everything is an Object
- Numbers are objects
> 1 + 2 * 3 / x    // 1.+(2.*(3)./(x))
- Functions are objects
```scala
object Timer {
  def oncePerSecond(callback: () => Unit): Unit = {  // () => Unit is the type of all functions which take no arguments and return nothing
    while (true) { callback(); Thread sleep 1000 }
  }
  def timeFlies(): Unit = {
    println("time flies like an arrow...")
  }
  def main(args: Array[String]): Unit = {
    oncePerSecond(timeFlies)
  }
}
```
- anonymous functions
```scala
object TimerAnonymous {
  def oncePerSecond(callback: () => Unit): Unit = {
    while (true) { callback(); Thread sleep 1000 }
  }
  def main(args: Array[String]): Unit = {
    oncePerSecond(() =>
      println("time flies like an arrow..."))
  }
}
```

# Classes
```scala
class Complex(real: Double, imaginary: Double) {
  def re = real
  def im = imaginary
}
```

## Inheritance and overriding
All classes in Scala inherit from a super-class. When no super-class is specified, as in the Complex example of previous section, scala.AnyRef is implicitly used.
```scala
class Complex(real: Double, imaginary: Double) {
  def re = real
  def im = imaginary
  override def toString() =
    "" + re + (if (im >= 0) "+" else "") + im + "i"
}
object ComplexNumbers {
  def main(args: Array[String]): Unit = {
    val c = new Complex(1.2, 3.4)
    println("Overridden toString(): " + c.toString)
  }
}
```
