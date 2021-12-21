---
title: Kotlin POJO 101(1)
date: 2021-05-25 10:21:00
author: Dragon
toc: true
hide: false
mathjax: false
categories: Tech Improvement
tags:
    - Kotlin
    - Java
    - POJO
---

# Kotlin POJO (1)

## class

**var/val**

- var: variable. 声明一个变量。在初始赋值会后仍然可以再被重新赋值，变量类型可自动推导
- val: value. 声明一个不可变的变量。在初始赋值之后就再也不能重新赋值，类似于Java中的final变量；

永远优先使用val来声明一个变量，而当val没有办法满足你的需求时再使用var。（这是一种没有变量的思想。）

注意： 变量类型推导在赋予初始值完成，若要采用延迟赋值，Kotlin则无法推导其类型，仍需要显式声明类型。

```kotlin
var name 
name = "DDD"
// This variable must either have a type annotation or be initialized

var name = "DDD"
```

Kotlin没有基本类型，全部使用对象类型
`Int, String, Long, Double Boolean ......`
类似于Java中包装基本类型的，包装类型。

**const**

若是声明常量：使用const关键字，const不能与var配合使用。

```kotlin
const val ITS_NAME = "Name IT" 
```

**final**

请注意，kotlin中默认的类和方法都是final的。因此不能继承、不能override。
若要实现可继承的基类需要使用**open关键字**。
继承父类与实现接口都只使用`:`即可。

```kotlin
open class Parent {
    open fun getName(){
        //...
    }
}

class Child : Parent() {
    override fun getName(){
        //...
    }
}
```



**主构造函数/次构造函数**

在 Kotlin 中的一个类可以有一个**主构造函数**以及一个或多个**次构造函数**。主构造函数是类头的一部分：它跟在类名（与可选的类型参数）后。

```kotlin
class Person constructor(firstName: String) { /*……*/ }
```

如果主构造函数没有任何注解或者可见性修饰符，可以省略这个 *constructor* 关键字。

```kotlin
class Person(firstName: String) { /*……*/ }
```

- 如果子类有主构造函数， 则基类必须在子类主构造函数中立即初始化。
- 如果子类没有主构造函数，则必须在每一个二级构造函数中用 super 关键字初始化基类，或者在代理另一个构造函数。初始化基类时，可以调用基类的不同构造方法。
- 通过在一个类中使用主和次级构造函数，次级构造函数需要授权给主构造函数,也就是**次级构造函数会直接或者间接调用主构造函数**。

示例对比Kotlin/Java 继承及构造器

// kotlin

```kotlin
fun main() {
    var parent = Parent("DAD")
    parent.printInfo()

    var child = Child("SON", 5)
    child.printInfo()

    var secondChild = SecondChild("SEC",10)
    secondChild.printInfo()
}

open class Parent(val name: String) {
    open fun printInfo() {
        print("I am Parent. My name is:$name\n")
    }
}

class Child(name: String, val age: Int) : Parent(name) {
    override fun printInfo() {
        print("I am Child. My name is:$name I am$age\n")
    }
}

class SecondChild : Parent {
    private var age: Int = 0

    constructor(name: String, age: Int) : super(name) {
        this.age = age
    }
}
```

// java

```java
package javaDemo;

public class main {
    public static void main(String[] args) {
        Parent parent = new Parent("DAD");
        parent.printInfo();

        Child child = new Child("SON",5);
        child.printInfo();


    }
}

class Parent {
    protected String name;

    public Parent(String name) {
        this.name = name;
    }

    public void printInfo() {
        System.out.println("I am Parent. My name is:" + name + "\n");
    }
}

class Child extends Parent {

    private int age;

    public Child(String name, int age) {
        super(name);
        this.age = age;
    }

    @Override
    public void printInfo() {
        System.out.println("I am Child. My name is:" + super.name + " I am" + age + ".\n");
    }
}
```

**无参构造函数**

kotlin中 不管是class 还是data class 都不会自动帮你生成 无参的构造函数。

- 把定义的属性 **手动给他指定一个默认值**

- 声明**指定一个无参的构造函数**

- **注解**实现无参构造器

Demo: 使用@NoArg

```kotlin
// build.gradle.kts
noArg {
    annotation("statuspage.backend.annotation.NoArg")
    invokeInitializers = true
}

// annoation
package noarg.demo.annotation

annotation class NoArg

// how to use
@NoArg
data class OrgInfo(
    val orgName: String
) : Serializable

// how to use no-arg create object
val orgInfo = OrgInfo::class.java.newInstance()
```



## data class

**只保存数据**的类

在Kotlin中，不能从数据类继承，因为编译器没有明智的方法来生成为数据类自动提供的所有方法。

data 数据类底层实现了哪些基本方法

- getter/setter
- 全参构造器
- equals()/hashCode()
- toString
- copy() : 浅拷贝
- componentN() : 按声明顺序对应于所有属性

数据类必须满足以下要求：

- 主构造函数需要至少有一个参数；
- 主构造函数的所有参数需要标记为 `val` 或 `var`；
- 数据类不能是抽象、开放、密封或者内部的；

```kotlin
@NoArg
data class Info(
    val id: Int,
    val panelId: Int,
    val name: String,
    val state: String,
    val newStateDate: Date,
    val url: String
) : Serializable
```

注： data class依然没有无参构造器，实现无参构造与上述方法相似。

