---
title: Kotlin POJO 101(2)
date: 2021-06-20 13:19:49
author: Dragon
toc: true
hide: false
mathjax: false
categories: Tech Improvement
tags:
    - Kotlin
    - POJO
    - 单例模式
    - NPE/?.
---

## object / companion object

object是Kotlin中的一个重要的关键字。

java 全局静态类 `static class GlobalUse`
kotlin没有`static`关键字，但有`object`关键字。

### object声明对象

使用object可以在kotlin中实现单例模式。也可以理解为全局静态类。

```kotlin
object OutObject {
    var outParameter = "out"
    fun outFunction(parameter:String) {
        println("Out object function result: $parameter.")
    }
}

fun changeParam(parameter: String){
    OutObject.outParameter = parameter
}

fun main() {
    print(OutObject.outParameter)
    OutObject.outFunction("Test object");
    print("==========================\n")
    changeParam("change param")
    print(OutObject.outParameter)
}
```

object declaration的类最终被编译成：一个类拥有**一个静态成员来持有对自己的引用，并且这个静态成员的名称为INSTANCE**。

注：和普通类的声明一样，可以包含属性(props)、方法(methods)、初始化代码块(init)以及可以继承其他类或者实现某个接口(:)，但**不能包含构造器(constructor)**。

object 也可以用于一个内部类的声明。与外部声明无区别，也属于内部类的单例模式。当然，内部类还有其他一些玩法，我们之后再进行了解（inner）

```kotlin
class ObjectOuter {
    object Inner{
        fun method(){
            println("I'm in inner class")
        }
    }
}

fun main(args: Array<String>) {
    ObjectOuter.Inner.method()
}
```

### companion object 伴生对象

因为在kotlin中没有static关键字的，就没有静态方法和静态成员。
因此在kotlin中使用包级别函数(package-level function)和伴生对象。

简单说一下包级别函数：

```kotlin
package com.demo

fun sayHello(name: String) {
    println("Hello $name!")
}

// import method
import com.demo.sayHello
fun main(args: Array<String>) {
    sayHello("world")
}
```

如果采用伴生对象实现静态方法，就需要使用`companion object`

示例如下：

```kotlin
class ObjectTest {
   // 定义时如果省略了伴生对象名，那么编译器会为其提供默认的名字Companion
  	companion object {
    //companion object MyObjec{
        val a = 20
        fun method() {
            println("I'm in companion object")
        }
    }
}

fun main(args: Array<String>) {
   // 如此调用，则与调用静态方法一致。
    ObjectTest.method()
    println(ObjectTest.a)
}
```

静态方法调用成员变量，要求成员变量必须是`静态的`

实际上，object可以用于对象表达式来声明匿名类。但需要注意：

- 匿名对象只有**定义成局部变量和private成员变量**时，才能体现它的真实类型(才能被访问)。
- 如果你是将匿名对象作为public函数的返回值或者是public属性时，你只能将它看做是它的父类，当然你不指定任何类型时就当做Any看待。这时，你在匿名对象中添加的属性和方法是**不能够被访问的**。

未曾使用过，不做具体分析，若有需求，可自行查看。

## ?

> Kotlin's type system is aimed at eliminating the danger of null references from code, also known as the [The Billion Dollar Mistake](https://en.wikipedia.org/wiki/Tony_Hoare#Apologies_and_retractions).

**目的就在于淘汰空指针（NPE）情况。**

### ?含义

因此，在声明类型时，自动类型推导可以区分可空（null）和不可空（non-null）。

在变量声明后加 ? 可允许变量为null。

比如：

```kotlin
var strA: String = "abc"
strA = null

// error : Null can not be a value of a non-null type String
```

若是声明可为null的变量，可使用 **?** 

```kotlin
var strB: String? = "ab"
strB = null

// correct.
```

但是，因为使用了? 会引发一些问题。比如无法安全调用对象的方法与属性。即当对象为null时，其不可能去调用属性方法。

```kotlin
strA.length // correct

strB.length // incorrent. error: variable 'b' can be null
```



`as` 作为类型转换关键字，`?`与`as`可结合使用。
`as?`表示若可以转换为指定类型则转换，若不行，返回null。

```kotlin
val str = "adf"
val math = str as Int // ClassCastException

val math = str as? Int // return null
```

### ?.

因此，当你声明了可空变量，就需要采取一些方法来规避上述问题。
也许第一念头法是使用条件判断去处理null和非null的情况。但是，若用Conditions会使程序更冗长。

*写到这里，突然发现，Java的Optional与其有异曲同工之妙。*

因此，kotlin提供了一种safe calls的运算法 **?.** 用于对可空变量的调用。

```kotlin
strB?.length
```

其实， **?.** 在链式调用中优势最明显，若其中有null存在，结果为null。不需要多层判断。（Js也有?. 用于避免相同情况）

```kotlin
var name: String? = company?.unit?.manager?.firstName
```

*补充一点关于 Scope functions的知识。具体了解可参考[此文](https://juejin.cn/post/6892176970965057550)。*

将`?.`与`let`结合使用，将为null的对象全部过滤并执行`let`内的代码。类似的还有`run`,`apply`等。

```kotlin
val listWithNulls: List<Int?> = listOf(1,2,3,null)
for (item in listWithNulls) {
    item?.let{
         val count = it + 1 // it 关键字，替代对象
         print(count)
    }
}
// 如此便将null值过滤出let执行块内。
```

### ?:

当使用可空声明`?`之后，如何对null情况进行抉择呢？如果使用`if else`这类条件判断会显得代码过于冗长。kotlin提供`?:`运算符解决此类问题。
若`?:`左边表达为null，则会执行其右边表达式，否则，返回左边表达式的值。

```kotlin
val strC: String? = "ccc"
val stcD: String? = null
val lenC = strC?.length ?: -1 // return 3
val lenD = strD?.length ?: -1 // return -1

val lenD = if (strD!=null) strD.length else -1 
```

注意：右边也可以是throw语句。

### !!

有时候如果需要将nullable变量转换为non-nullable变量，则需要用上`!!`强制类型转换符。
同时也要注意，一旦变量被声明为non-nullable类型，若出现为null情况，就会出现NPE。
因此，不要随意使用`!!`



这部分内容先到此为止，若有后续需求，会继续更新。江湖路远，不见不散。

