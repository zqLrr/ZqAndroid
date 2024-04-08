# 22 Kotlin 语法入门

## 环境配置

### IDEA

需要在IDEA中下载Kotlin插件

1.使用Gradle 管理Kotlin版本

```groovy
//Build.gradle
plugins {
    id 'org.jetbrains.kotlin.jvm' version '1.8.20'
}

repositories {
    mavenCentral()
    jcenter()
}

dependencies {
    implementation "org.jetbrains.kotlin:kotlin-stdlib"
}
```

### Android Studio

 Gradle管理Kotlin版本

```java
classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:1.8.20"
```

```kotlin
fun main() {
    val kotlinVersion = KotlinVersion.CURRENT
    println("Kotlin compiler version: ${kotlinVersion.major}.${kotlinVersion.minor}.${kotlinVersion.patch}")
}
```



## 语法入门

### 函数

> 函数定义使用关键字 fun，函数格式：fun 函数名(函数参数): 返回类型；参数格式为：参数 : 类型。

```kotlin
//标准格式
fun sum(a: Int, b: Int): Int {   
    return a + b
}

// 表达式作为函数体，返回类型自动推断
fun sum(a: Int, b: Int) = a + b
public fun sum(a: Int, b: Int): Int = a + b   // public 方法则必须明确写出返回类型
```

### NULL检查机制

> 字段后+`?`:可不做处理返回值为null或使用`?:`，字段后+`!!`:抛出空指针异常。

```kotlin
//类型后面加?表示可为空
var age: String? = "23" 
//抛出空指针异常
val ages = age!!.toInt()
//不做处理返回 null
val ages1 = age?.toInt()
//age为空返回-1
val ages2 = age?.toInt() ?: -1
  // 如果 str 的内容不是数字返回 null
fun parseInt(str: String): Int? {
    // ……
}
```

### 类型检测与自动类型转换

> `is` 运算符检测一个表达式是否某类型的一个实例.

```kotlin
fun getStringLength(obj: Any): Int? {
    if (obj is String) {
        // `obj` 在该条件分支内自动转换成 `String`
        return obj.length
    }

    // 在离开类型检测分支后，`obj` 仍然是 `Any` 类型
    return null
}
fun getStringLength(obj: Any): Int? {
    if (obj !is String) return null

    // `obj` 在这一分支自动转换为 `String`
    return obj.length
}
```

### When表达式

```java
fun describe(obj: Any): String =
    when (obj) {
        1          -> "One"
        "Hello"    -> "Greeting"
        is Long    -> "Long"
        !is String -> "Not a string"
        else       -> "Unknown"
    }
```

### 集合

```java
val fruits = listOf("banana", "avocado", "apple", "kiwifruit")
  fruits
  .filter { it.startsWith("a") } //集合可以过滤
.sortedBy { it }
.map { it.toUpperCase() }//可以转换
.forEach { println(it) }
```

## Contract的概念

Contract 是一种向编译器通知函数行为的方法，有以下特点：

1. 只能在 top-level 函数体内使用 Contract，即我们不能在成员和类函数上使用它们。
2. Contract 所调用的声明必须是函数体内第一条语句
3. Kotlin 编译器并不会验证 Contract，因此必须编写正确合理的 Contract
4. 内联化的函数（也需要是 top-level 层级的函数）支持使用 Contract

```kotlin
@ExperimentalContracts
fun String?.isNull(): Boolean {
    //下面是添加的内容
    contract {
        returns(false) implies (this@isNull != null)
    }
    //上面是添加的内容
    return this == null
}

@ExperimentalContracts
fun test() {
    var str: String? = null

    if (str.isNull()) {
        str = "kotlin contract"
    }

    println(str.length)
}
```

### Contract 的分类

#### Returns Contracts

表示当 return 的返回值是某个值（例如true、false、null）时，implies 后面的条件成立，有以下几种形式：

| 形式                              | 说明                                           |
| --------------------------------- | ---------------------------------------------- |
| returns(value: Any?) implies 条件 | 如果函数返回值为 value，条件成立               |
| returns() implies 条件            | 如果函数能够正常返回，且没有抛出异常，条件成立 |
| returnsNotNull implies 条件       | 如果函数返回非 null 值，条件成立               |

### CallsInPlace Contracts

CallsInPlace Contracts 允许开发者对调用的 lambda 表达式进行频率上的约束，只能在 inline 函数中调用

前面的高阶函数 let 就是一个 CallsInPlace Contracts

```kotlin
@kotlin.internal.InlineOnly
public inline fun <T, R> T.let(block: (T) -> R): R {
    contract {
        callsInPlace(block, InvocationKind.EXACTLY_ONCE)
    }
    return block(this)
}
```

contract() 中的 callsInPlace 会告诉编译器，lambda 表达式 block 在 let 函数内只会执行一次

callsInPlace() 中的 InvocationKind 是一个枚举类，包含如下的枚举值

| 枚举值        | 说明                    |
| ------------- | ----------------------- |
| AT_MOST_ONCE  | 函数参数调用次数 <= 1   |
| EXACTLY_ONCE  | 函数参数调用次数 == 1   |
| AT_LEAST_ONCE | 函数参数调用次数 >= 1   |
| UNKNOWN       | 函数参数调用次数 不限制 |

let

```kotlin
@kotlin.internal.InlineOnly
public inline fun <T, R> T.let(block: (T) -> R): R {
    contract {
        callsInPlace(block, InvocationKind.EXACTLY_ONCE)
    }
    return block(this)
}
```

### 类与对象

构造函数：分为主构造函数和次构造函数。

主构造函数指直接在类名后进行初始化，用`()`实现

主构造函数中可以直接定义属性

主构造函数中不能有任何代码，可以使用init 定义的初始化块中来实现

主构造函数定义的属性可以在init 中使用，也可以在声明属性的初始化器中使用

初始化块是主构造函数的一部分，哪怕没有实现，也会隐式发生。

次构造函数，在类中使用constructor(){} 来实现





