---
title: Kotlin 基础语法走一波
date: 2020-1-1 21:38:14
tags: [Kotlin]
categories: Android

---

# 程序入口写法

```kotlin
fun main(){
    println("Hello world!")
}
```

# 基本数据类型 Basic Types

```
Byte
Short
Int
Long
Float
Double

Char
Booleans
Arrays
String
```

比较(不同类型直接 == 比较 为 false)

```
val a: Int? = 1
val b: Long? = a
print(b == a) // false
```

基本数据类型之间类型转换:

```
toByte(): Byte
toShort()
toInt()
toLong()
...
```



# 函数 Functions

```kotlin
fun sum(a: Int, b: Int): Int{
    return a + b
}
```

函数缩写(函数内只有一条语句)

```kotlin
fun sum(a: Int, b: Int) = a + b
```

函数无返回值的时候:

```kotlin
fun printSum(a: Int, b: Int): Unit{
    println("sum of $a and $b is ${a + b}")
}

fun printSum(a: Int, b: Int){
    println("sum of $a and $b is ${a + b}")
}
```

# 变量 Variables

不可修改变量使用 `val` 修饰
可以修改的变量使用 `var` 修饰

# Nullable, null checks

变量是否可以是 null, 需要声明: `Int?` 表示可能是 null.

## ? 相关的写法

```
val files = File("Test").listFiles()
println(files?.size)
```

```
val files = File("Test").listFiles()
println(files?.size ?: "empty")
```

```
val emails = ... // might be empty
val mainEmail = emails.firstOrNull() ?: ""
```

如果 not null 就执行代码:

```
val value = ...
value?.let {
    ... // execute this block if not null
}
```



# 类型判断和自动转换 Type checks and automatic casts

```kotlin
if(obj is String){
    obj 会自动转化成 String
}

if(obj !is String) return null
obj 会自动转化成 String
```

# For, While, When

```
for(item in items){
    //...
}

for(index in items.indices){
    //....
}

when(obj){
    1 -> "one"
    "Hello" -> "Two"
    is Long -> "Long"
    !is String -> "Not a String"
    else -> "UnKnow"
}
```

## Break, continue, return



# Arrays, Collections, Map

## Arrays
基本数据类型数组
```
val x: IntArray = intArrayOf(1, 2, 3)

// Array of int of size 5 with values [0, 0, 0, 0, 0]
val arr = IntArray(5)
```

## Collections

判断对象是否在集合中

```
when{
    "orange" in items -> println("Link")
}
```

```
if("item" in items){
    //...
}
```

## Map

```
for((k, v) in map){
    //...
}
```


# Class

## Data classes

```kotlin
data class User(val name: String, val age: Int)
```

JVM 中，如果需要无参数构造函数，需要设置默认值:

```kotlin
data class User(val name: String = "", val age: Int = 0)
```

可以在 Class Body 中定义成员变量:

```kotlin
data class Person(val name: String) {
    var age: Int = 0
}

val person1 = Person("John")
```

Data 解构:

```kotlin
val jane = User("Jane", 35)
val (name, age) = jane
```

## Constructors 构造函数

Kotlin 中有主构造函数和副构造函数，主构造函数在 class 头里面声明，副构造函数

```
class Person constructor(firtname: String){}
```

主构造函数缩写:

```
class Person (firtname: String){}
```

主构造函数中不能写代码，初始化函数要写在 `init` 语句快中, 可以有多个 `init` 语句块，按照顺序执行

```
class InitOrderDemo(name: String) {
    val firstProperty = "First property: $name".also(::println)
    
    init {
        println("First initializer block that prints ${name}")
    }
    
    val secondProperty = "Second property: ${name.length}".also(::println)
    
    init {
        println("Second initializer block that prints ${name.length}")
    }
}
```

### 副构造函数

```
class Person {
    var children: MutableList<Person> = mutableListOf<Person>();
    constructor(parent: Person) {
        parent.children.add(this)
    }
}
```

### Getters and Setters

```
var <propertyName>[: <PropertyType>] [= <property_initializer>]
    [<getter>]
    [<setter>]
```




# 设计模式

## Singleton

```
object Resource{
    val name = "Instance"
}
```










