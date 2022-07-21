# Lambda 编程基础

Lambda 表达式完整结构语法：

```kotlin
{参数名1: 参数类型1, 参数名2: 参数类型2 -> 函数体}
```

## 集合的函数式 API

思考一个需求，在一个水果集合里面找到单词最长的那个水果，使用函数式 API 可以这么写：

```kotlin
val list = listOf("Apple", "Banana", "Orange", "Pear", "WaterMelon")
val maxLengthFruit = list.maxBy{ it.length }
println("Max length fruit is ${maxLengthFruit}")
```

这是 Lambda 表达式的简化写法，那么这些简化版写法是怎么来的呢？我们来一步步探索。

maxBy 函数其实接收的是一个 Lambda 类型参数，并且在遍历集合时将每次遍历的值作为参数传递给 Lambda 表达式。maxBy 工作原理是根据我们传入的条件来遍历集合，从而找到该条件下的最大值。（这里要找到单词最长的水果，那么条件自然就是单词的长度）

```kotlin
val list = listOf("Apple", "Banana", "Orange", "Pear", "WaterMelon")
val lambda = { fruit: String -> fruit.length }
val maxLengthFruit = list.maxBy(lambda)
```

这是最完整的写法，但是这很啰嗦，我们来逐步简化它。

首先，我们无需专门定义一个 lambda 变量，可以直接将 lambda 表达式传入 maxBy 函数中：

```kotlin
val maxLengthFruit = list.maxBy({ fruit: String -> fruit.length })
```

Kotlin 规定，当 lambda 参数是函数的最后一个参数时，可以将 Lambda 表达式移到参数括号的外面：

```kotlin
val maxLengthFruit = list.maxBy(){ fruit: String -> fruit.length }
```

接下来，如果 Lambda 参数是函数的唯一参数，可以将参数括号省略：

```kotlin
val maxLengthFruit = list.maxBy{ fruit: String -> fruit.length }
```

由于 Kotlin 的类型推导机制，Lambda 表达式中的参数在大多数情况下都无需声明参数类型：

```kotlin
val maxLengthFruit = list.maxBy{ fruit -> fruit.length }
```

最后，当 Lambda 表达式的参数列表中只有一个参数时，可以不声明参数名而使用 it 默认关键字代替。

```kotlin
val maxLengthFruit = list.maxBy{ it.length }
```



## Java 函数式 API 的使用

在 **Kotlin 中调用 Java 方法**也可以使用函数式 API，但有一定的条件限制：**该方法接收一个 Java 单抽象方法接口参数**。（Java 单抽象方法接口指的是接口中只有一个待实现方法）



以 Runnable 接口为例，这个接口中只有一个待实现的 `run()` 方法：

```java
public interface Runnable {
	void run();
}
```

Thread 类的构造方法中接收一个 Runnable 参数，依照传统写法，创建并执行一个线程可以这么写：

```kotlin
Thread(object: Runnable{
	override fun run(){
		println("Thraed is running")
	}
}).start()
```

使用函数式 API 的写法：

```
Thread(Runnable {
	println("Thread is running")
}).start()
```

这段代码明显简化了不少，实现了相同的功能的同时又不会造成歧义，因为 Runnable 类中只有一个待实现方法，即使这里并没有显式地重写 `run()` 方法，Kotlin 也能明白 Runnable 后面的 Lambda 表达式就是要在 `run()` 方法中实现的内容。

另外，如果 Java 方法的参数列表中有且仅有一个 Java 单抽象方法接口参数，还可以将接口名省略：

```kotlin
Thread({
	println("Thread is running")
}).start()
```

和之前的 Kotlin 中函数式 API 用法类似，当 Lambda 表达式是方法最后一个参数时，可以将 Lambda 表达式移到参数括号外面；同时，如果  Lambda 表达式是方法的唯一参数，可以将参数括号省略：

```kotlin
Thread {
	println("Thread is running")
}.start()
```

