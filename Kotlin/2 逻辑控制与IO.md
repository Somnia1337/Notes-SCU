## 1. if

==Kotlin的if语句可以作为表达式使用==。

如果if的分支内有多条语句，其中最后一个表达式的值将作为该分支的返回值。

```Kotlin
/*用if简化largerNumber*/
fun largerNumber(a: Int, b: Int): Int
{
	var value = 0
	if (a > b) value = a else value = b
	return value
}

//可以将if语句返回值赋给value，简化为

fun largerNumber(a: Int, b: Int): Int
{
	var value = if (a > b) a else b
	return value
}

//还可以将其赋给return语句，简化为

fun largerNumber(a: Int, b: Int): Int
{
	return if (a > b) a else b
}

//甚至将其赋给整个函数，简化为

fun largerNumber(a: Int, b: Int): Int = if (a > b) a else b
```

```Kotlin
/*多语句分支*/
val max = if (a > b)
{
    print("Choose a")
    a
} 
else
{
    print("Choose b")
    b
}
```

## 2. when

when语句类似于switch语句，不过更强大。

```Kotlin
/*函数getScore*/
fun getScore(name: String) = 
if (name == "Tom") 86
else if (name == "Jim") 77
else if (name == "Jack") 95
else if (name == "Lily") 100
else 0

//用when语句改写为

fun getScore(name: String) = when (name)
{
	"Tom" -> 86
	"Jim" -> 77
	"Jack" -> 95
	"Lily" -> 100
	else -> 0
}
```

when语句允许传入一个==任意类型的参数==，并在其结构体中定义一系列条件。

```Kotlin
/*分支中的条件*/
//可以在一个分支中指定多个情况
when (x)
{
	0, 1 -> print("valid")
	else -> print("invalid")
}

//可以在分支中使用表达式
when (x)
{
    s.toInt() -> print("s encodes x")
    else -> print("s does not encode x")
}

//可以使用in/!in检查值是否属于范围或集合
when (x)
{
    in 1..10 -> print("x is in the range")
    in validNumbers -> print("x is valid")
    !in 10..20 -> print("x is outside the range")
    else -> print("none of the above")
}

//可以使用is/!is检查值是否为某个类型
when (num)
	{
		is Int -> println("number is Int")
		is Double -> println("number is Double")
		else -> println("number not supported")
	}
```

**关键字is**：相当于Java的关键字instanceOf。

由于Kotlin的智能类型转换，进行过类型判断之后可以直接访问该类型的方法和属性，无需显式的类型检查。

when可以用来替代if-else_if-else串，如果不指定参数，所有的分支条件都应为布尔表达式，当值为true时执行对应的分支：

```Kotlin
fun getScore(name: String) = when
{
	name == "Tom" -> 86
	name == "Jim" -> 77
	name == "Jack" -> 95
	name == "Lily" -> 100
	else -> 0
}
```

Kotlin中==判断字符串或对象相等可以直接使用双等号(\==)==，而无需调用equals。

有些场景下无参when会很有用，例如，当所有名字开头为Tom（Tom、Tommy等）的人都得了86分：

```Kotlin
fun getScore(name: String) = when
{
	name.startsWith("Tom") -> 86
	name == "Jim" -> 77
	name == "Jack" -> 95
	name == "Lily" -> 100
	else -> 0
}
```

可以将when的判断对象保存到一个变量中，该变量仅在when内有效：

```Kotlin
fun Request.getBody() =
    when (val response = executeRequest())
	{
        is Success -> response.body
        is HttpError -> throw HttpException(response.status)
    }
```

when可以用作表达式，也可以用作流程控制语句。如果用作表达式，第一个匹配成功的分支的返回值将成为整个表达式的值；如果用作流程控制语句，各分支的返回值将被忽略。

如果用作表达式，必须有else分支，除非编译器能够证明其他分支的条件已经覆盖了所有可能的情况，例如使用枚举类的常数或封闭类的子类型。

```Kotlin
enum class Bit
{
	ZERO, ONE
}

val numericValue = when (getRandomBit())
{
    Bit.ZERO -> 0
    Bit.ONE -> 1
    //无需else，因为已经覆盖所有情况
}
```

必需else的情况：

- 判断对象为Boolean，枚举类，或封闭类型，或它们的nullable类型。
- 分支没有覆盖所有可能的情况。

## 3. 循环

### 1) 下标循环

任何值，只要能够产生一个迭代器(iterator)，就可以使用for循环进行遍历。

如果需要使用下标遍历数组或List，可以调用`*array.indices`：

```Kotlin
for (i in array.indices)
    println(array[i])
```

或者使用库函数withIndex：

```Kotlin
for ((index, value) in array.withIndex())
    println("the element at $index is $value")
```

### 2) 区间

==Java中的for-i被舍弃==，for-each则变为for-in。

**区间**：概念与数学上的区间相同。

**双点号(..)（函数rangeTo）**：创建==双端闭==区间。

```Kotlin
/*区间的创建与使用*/
val range = 0..10 //[0, 10]

for (i in range)
	println(i)
```

**函数until**：创建==左闭右开==区间。

```Kotlin
val range = 0 until 10 //[0, 10)
```

**函数downTo**：创建==降序==区间。

```Kotlin
val range = 10 downTo 1 //[10, 1]
```

函数reversed：反向遍历区间。

```Kotlin
for (i in (1..4).reversed()) print(i)
```

**关键字step**：为区间==指定步长==。

```Kotlin
val range = 0..10 step 2 //{0, 2, 4, 6, 8}
```

**非运算符(!)**：对区间==取反==。

```Kotlin
if (n !in 0..100)
	println("Number is not valid.")
```

## 4. 返回与跳转

有3个跳出程序流程的表达式，return、break与continue。

```Kotlin
val s = person.name ?: return
```

标签(label)：由标识符后面加at符号(@)组成，例如`abc@`、`fooBar@`。

将标签放在表达式之前可以标记表达式。

```Kotlin
/*标签的使用*/
loop@ for (i in 1..100) { ... }
```

标签标记控制流程表达式，可以指定跳转的对象表达式。

```Kotlin
loop@ for (i in 1..100)
{
    for (j in 1..100)
	{
        if (...) break@loop
    }
}
```

在嵌套的函数中可以通过标签return实现非局部的返回（仅对传递给内联函数的lambda表达式有效）。

```Kotlin
fun foo()
{
    listOf(1, 2, 3, 4, 5).forEach lit@{
        if (it == 3) return@lit
        print(it)
    }
    print("done with explicit label")
}
```

`return@lit`将从lambda返回到其调用者，即forEach循环。

使用隐含标签：

```Kotlin
fun foo()
{
    listOf(1, 2, 3, 4, 5).forEach {
        if (it == 3) return@forEach
        print(it)
    }
    print("done with implicit label")
}
```

可以用一个匿名函数替代lambda，return将从该匿名函数返回：

```Kotlin
fun foo()
{
    listOf(1, 2, 3, 4, 5).forEach(fun(value: Int)
	{
        if (value == 3) return
        print(value)
    })
    print("done with anonymous function")
}
```

以上的效果均与continue类似。

## 5. 输入与输出

**函数readln**：读取整行输入为字符串。

```Kotlin
/*readln的使用*/
val line = readln()
```

**函数toInt**、**toLong**：将输入转换为整数。

```Kotlin
/*toInt的使用*/
println("Enter any number: ")
val number = readln().toInt()
print("You entered the number: ")
print(number)
```

**函数toDouble**：将输入转换为浮点数。

```Kotlin
/*toDouble的使用*/
println("Enter any double type number:")
val number = readln().toDouble()
println("You entered the number: ")
print(number)
```

**函数toBoolean**：将输入转换为布尔值。

```Kotlin
/*toBoolean的使用*/
println("The earth is flat. Type true or false:")
val answer = readln().toBoolean()
print("The earth is flat: ")
print(answer)
```

只有当读取的字符串为"true"的忽略大小写变种时才为true，其他情况均为false。

**函数split**：在一行中读取多个值时指示分隔符。

```Kotlin
/*在一行中读取多个值*/
val (a, b) = readln().split(" ")
```