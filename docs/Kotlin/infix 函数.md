# infix 函数

在 Kotlin 中可以使用 `A to B` 这样的语法结构去构建键值对，这种语法结构的优点是可读性高，相较于调用函数，它用更接近于英语的语法来编写程序。

这是怎么实现的？首先，to 并不是 Kotlin 中的关键字。之所以可以使用 `A to B` 这样的语法结构，完全得益于 Kotlin 提供了一种高级语法糖特征：infix 函数。



## 用法

我们由浅到深来学习一下 infix 函数的用法。

String 中有一个 `startsWith()` 函数，用于判断一个字符串是否以某个指定参数开头：

```kotlin
if("Hello".startsWith("H")){
    ...
}
```

借助于 infix 函数， 我们可以使用一种更具可读性的语法来实现相同的功能：

```kotlin
infix fun String.beginsWith(prefix: String) = startsWith(prefix)
```

如果不加上 `infix` 关键字，那么我们只能使用 `"Hello".beginsWith("H")` 来调用 `beginsWith`，这和 `startsWith()` 并无二异，但是加上了 `infix` 关键字后，除了传统的函数调用方式外，我们还可以用一种特殊的语法糖格式来调用 `beginsWith()`：

```kotlin
"Hello" beginsWith "H"
```



## 限制

不过由于 infix 函数语法糖格式的特殊性，有两个比较严格的限制：

- 不能定义为顶层函数，它必须是某个类的成员函数；
- 必须接收且仅接收一个参数。



## 例子1

有一个集合，想要判断集合中是否包含某个指定元素，一般可以这么写：

```kotlin
val list = listOf("Apple", "Pear", "Banana")
if(list.contains("Banana")){
    ...
}
```

下面借助 infix 函数让代码更具可读性：

```kotlin
infix fun Collection<T>.has(element: T) = contains(element)
```

首先，我们给 Collection 接口添加了一个拓展函数，因为它是 Java 及 Kotlin 中所有集合的总接口，因此如果给 Collection 添加一个 has() 拓展函数，那么所有集合的子类都可以使用这个函数了。另外还使用的泛型的写法使得 has() 可以接收任何具体类型的参数。现在我们可以使用下面的语法来判断集合中是否包含某个元素了：

```kotlin
val list = listOf("Apple", "Pear", "Banana")
if(list has "Banana"){
    ...
}
```



## 例子2

Kotlin 中可以使用 `A to B` 来创建一个 Pair 对象，我们先来看一个 to() 函数的源码：

```kotlin
public infix fun<A, B> A.to(that: B) : Pair<A, B> = Pair(this, that)
```

使用定义泛型函数的方式将 to() 函数定义到 A 类型下，并接收一个 B 类型的参数。因此 A 和 B 可以是两种不同类型的泛型。