## 1. 类型检查与转换

### 1) 操作符is、!is

可以用操作符is及与其相反的!is检查对象是否属于指定类型。

```Kotlin
if (obj is String)
    print(obj.length)

if (obj !is String)
    print("Not a String")
else
    print(obj.length)
```

### 2) 智能类型转换

大多数情况下，使用Kotlin时不必进行显式的类型转换，因为编译器将对val值的is检查与显式的类型转换进行追踪，并在需要时自动插入安全的类型转换。

```Kotlin
fun demo(x: Any)
{
    if (x is String)
        print(x.length) //x已被自动转换为String
}
```

如果!is检查导致了return，此时编译器可以判断出转换处理安全：

```Kotlin
if (x !is String) return

print(x.length)
```

对于操作符&&与||，如果在操作符左侧进行了适当的类型检查，那么操作符右侧也是如此：

```Kotlin
//在||右侧，x被自动转换为String
if (x !is String || x.length == 0) return

//在&&右侧，x被自动转换为String
if (x is String && x.length > 0)
    print(x.length)
```

智能类型转换同样适用于when与while：

```Kotlin
when (x)
{
    is Int -> print(x + 1)
    is String -> print(x.length + 1)
    is IntArray -> print(x.sum())
}
```

智能类型转换需满足的条件：

- 局部val变量：永远有效。
- 局部var变量：如果在类型检查语句与变量使用语句之间，变量没有被改变，而且它没有被lambda捕获并修改，并且它不是一个局部的委托属性，那么智能类型转换是有效的。
- val属性：如果属性标记private或internal，或者类型检查处理与属性定义出现在同一个模块(module)内，那么智能类型转换有效。对于open或存在自定义get方法的属性无效。
- var属性：永远无效。

操作符as：不安全的类型转换操作符，如果转换不成功，类型转换操作符将抛出异常，应使用as。

```Kotlin
val x: String = y as String
```

null类型不是可为null的(nullable)，不能被转换为String，如果`y`为null，这段代码将抛出异常，应在as右侧使用可为null的类型：

```Kotlin
val x: String? = y as String?
```

操作符as?：如果转换失败，将返回null。

```Kotlin
val x: String? = y as? String
```

## 2. lambda编程

### 1) 集合的创建与遍历

传统意义上的集合就是List与Set、Map等，它们在Java中都是接口，主要实现类分别是ArrayList与LinkedList、HashSet、HashMap。

```Kotlin
/*创建ArrayList实例*/
val list = ArrayList<String>()
list.add("Apple")
list.add("Banana")
list.add("Orange")
list.add("Pear")
list.add("Grape")
```

**函数listOf**：简化集合的初始化。

```Kotlin
/*用listOf简化list的初始化*/
val list = listOf("Apple", "Banana", "Orange", "Pear", "Grape")
```

```Kotlin
/*用for-in遍历list*/
for (fruit in list)
	println(fruit)
```

listOf创建的是一个不可变集合（只能读取，不能添加、修改或删除），如需创建可变集合，需要用**函数mutableListOf**。

```Kotlin
/*用mutableListOf创建可变集合*/
val list = mutableListOf("Apple", "Banana", "Orange", "Pear", "Grape")
list.add("Watermelon")
```

Set集合与List类似，函数名为setOf、mutableSetOf，不同在于，==Set中不允许有重复元素==，如果存放了多个相同元素，则只会保留其中一份。

Map集合是一种键值对形式的数据结构，传统写法是先创建一个HashMap实例，然后添加键值对数据。

```Kotlin
/*创建HashMap实例*/
val map = HashMap<String, Int>()
map.put("Apple", 1)
map.put("Banana", 1)
map.put("Orange", 1)
map.put("Pear", 1)
map.put("Grape", 1)

//在Kotlin中，更推荐以下类似于数组的语法

val map = HashMap<String, Int>()
map["Apple"] = 1
map["Banana"] = 1
map["Orange"] = 1
map["Pear"] = 1
map["Grape"] = 1
```

```Kotlin
/*用mapOf简化*/
val map = mapOf("Apple" to 1, "Banana" to 2, "Orange" to 3, "Pear" to 4, "Grape" to 5)
```

这里的to并不是关键字，而是一个infix函数。

```Kotlin
/*用for-in遍历map*/
for ((fruit, number) in map)
	println(fruit + " " + number)
```

### 2) 集合的函数式API

lambda是一小段可以作为参数传递的代码，正常情况下向函数传参时只能传递变量，而借助lambda可以传递一段代码。

```Kotlin
/*lambda的语法结构*/
{ *param1: *Type, *param2: *Type -> /*body*/}
```

函数体的最后一行代码将自动作为其返回值。

```Kotlin
/*找到水果集合内单词最长的水果*/
var maxLengthFruit = ""
for (fruit in list)
	if (fruit.length > maxLengthFruit.length)
		maxLengthFruit = fruit

//使用lambda简化为

val maxLengthFruit = list.maxBy { it.length }
```

函数maxBy接收一个lambda类型的参数，在遍历集合时将每次遍历的值作为参数传递，它将根据传入的条件遍历集合，找到该条件下的最大值。

```Kotlin
/*上文代码的简化前形式*/
val lambda = { fruit: String -> fruit.length }
val maxLengthFruit = list.maxBy(lambda)

//下面对其一步步简化

//首先，不需要专门定义变量lambda

val maxLengthFruit = list.maxBy({ fruit: String -> fruit.length })

//其次，如果函数的最后一个参数为lambda，可将其表达式移至函数括号外

val maxLengthFruit = list.maxBy() { fruit: String -> fruit.length }

//然后，如果lambda还为函数的唯一参数，可省略函数括号

val maxLengthFruit = list.maxBy { fruit: String -> fruit.length }

//再后，lambda中的参数列表大多情况下不必声明类型

val maxLengthFruit = list.maxBy { fruit -> fruit.length }

//最后，如果lambda的参数列表中只有一个参数，可以省略参数名并以关键字it代替

val maxLengthFruit = list.maxBy { it.length }
```

接下来还有几个常用的集合的函数式API：

- map：将集合中的每个元素映射为一个另外的值，映射的规则在lambda中指定，最终生成一个新的集合。

```Kotlin
/*用map将所有水果名改为大写*/
val newList = list.map{ it.toUpperCase() }
```

此外，还可以对水果名取首字母、甚至转换为单词长度的Int集合，只需在lambda中编写规则。

- filter：过滤集合中的数据。

```Kotlin
/*用filter筛选不超过5个字母的水果名*/
val newList = list.filter { it.length <= 5 }
```

- any：判断集合中是否至少存在一个元素满足指定条件，返回一个boolean值。
- all：判断集合中是否所有元素都满足指定条件，返回一个boolean值。

```Kotlin
/*用any/all判断list中的水果名是否存在满足/全部满足长度不超过5个字母*/
val anyResult = list.any { it.length <= 5 }
val allResult = list.all { it.length <= 5 }
```

在Kotlin中调用Java方法也可以使用函数式API，限制条件是调用的方法接收一个Java单抽象方法接口（只有一个待实现方法的接口）参数。

例如，Java原生API中最常见的单抽象方法接口Runnable，只要调用的目标方法接受一个Runnable参数，就可以使用函数式API。

```Java
/*在Java中创建并执行一个子线程*/
new Thread(new Runnable()
	{
		@Override
		public void run()
		{
			System.out.println("Thread is running");
		}
	}).start();
```

此处创建了一个Runnable的匿名类实例，将其传递给Thread类的构造方法，最后调用方法start执行线程。

```Kotlin
/*在Kotlin中创建并执行一个子线程*/
Thread(object : Runnable
	  {
		  override fun run()
		  {
			  println("Thread is running")
		  }
	  }).start()
```

由于Kotlin完全弃用关键字new，创建匿名类实例时改用关键字object。这样看并没有精简很多，不过Thread类的构造方法符合Java函数式API的限制条件，可以用函数式API简化。

```Kotlin
/*用函数式API简化*/
Thread(Runnable {
	println("Thread is running")
}).start()
```

由于Runnable中只有一个待实现方法，所以即使不显式重写run，编译器也明白此处的lambda表达式就是要在run中实现的内容。

如果一个Java方法的参数列表中仅有一个Java单抽象方法接口参数，可以省略接口名。

```Kotlin
/*用省略接口名简化*/
Thread({  
	println("Thread is running")  
}).start()
```

之前提到，如果函数的最后一个参数为lambda，可将其表达式移至函数括号外；如果lambda还为函数的唯一参数，可省略函数括号。

```Kotlin
/*用lambda规则简化*/
Thread {
	println("Thread is running")
}.start()
```

以后要经常打交道的Android SDK还是需要用Java编写，例如，Android中极为常用的点击事件接口OnClickListener：

```Java
public interface OnClickListener
{
	void onClick(View v);
}
```

```Java
/*在Java中注册一个按钮的点击事件*/
button.setOnClickListener(new View.OnClickListener()
	{
		@Override
		public void onClick(View v)
		{
		}
	});
```

```Kotlin
/*在Kotlin中注册一个按钮的点击事件，用函数式API简化*/
button.setOnClickListener
{
}
```

## 3. 空指针检查

### 1) 可空类型系统

Kotlin解决了NullPointerException的问题，它利用编译时判空机制几乎杜绝了该异常。

```Kotlin
fun doStudy(study: Study)
{
	study.readBooks()
	study.doHomework()
}
```

同样的代码在Java中存在风险（`study`可能为null），但在Kotlin中是安全的，因为Kotlin默认所有的参数与变量都不可为空，也就是说Kotlin将空指针异常的检查提前到了编译时期，如果存在风险将无法编译。

如需某个参数或变量为空，可以使用可为空类型系统，在类名后添加问号(?)，如果这样做，需要手动排除潜在的空指针异常，否则无法编译。

```Kotlin
/*可为空类型系统的使用*/
fun main()
{
	doStudy(null)
}

fun doStudy(study: Study?)
{
	study.readBooks()
	study.doHomework()
}
```

这段代码没有人为处理潜在的空指针异常，无法编译。

```Kotlin
/*手动排除潜在的空指针异常*/
fun main()
{
	doStudy(null)
}

fun doStudy(study: Study?)
{
	if (study != null)
	{
		study.readBooks()
		study.doHomework()
	}
}
```

### 2) 判空辅助工具

为了在编译时期就处理所有的空指针异常，通常需要编写很多额外的检查代码。每次都使用if判断语句会使代码很烦琐，而且无法处理全局变量的判空问题，为此Kotlin提供了一系列判空辅助工具。

**操作符(?.)**：当对象不为空时正常调用方法，否则什么都不做。

```Kotlin
/*用?.改写if语句*/
if (a != null)
	a.doSomething()

//改写为

a?.doSomething()
```

```Kotlin
/*用?.改写doStudy*/
fun doStudy(study: Study?)
{
	study?.readBooks()
	study?.doHomework()
}
```

**操作符(?:)**：左右各接收一个表达式，如果左侧表达式结果非空则返回其结果，否则返回右侧表达式结果。

```Kotlin
/*用?:改写if语句*/
val c = if (a != null) a else b

//改写为

val c = a ?: b
```

```Kotlin
/*返回一段文本长度的函数getTextLength*/
fun getTextLength(text: String?): Int
{
	if (text != null)
		return text.length
	return 0
}

//用?.与?:改写为

fun getTextLength(text: String?) = text?.length ?: 0
```

空指针检查机制并非总是智能，有时可能从逻辑上已经排除了空指针异常，但编译器不知道，仍将编译失败。

```Kotlin
/*一段从逻辑上排除潜在空指针异常的代码*/
var content: String? = "hello"

fun main()
{
	if (content != null)
		printUpperCase()
}

fun printUpperCase()
{
	val upperCase = content.toUpperCase()
	println(upperCase)
}
```

在main中已经对`content`进行判空处理，但printUpperCase并不知道，所以无法编译。

此时需要强行通过编译，可以使用**非空断言(!!)**，将其添加在对象之后。

```Kotlin
/*非空断言!!的使用*/
fun printUpperCase()
{
	val upperCase = content!!.toUpperCase()
	println(upperCase)
}
```

这是一种有风险的写法，意在向编译器保证此对象非空，所以不要进行空指针检查，如果出现问题就直接抛出空指针异常，后果由我自己承担。不过这样写之前，最好想一想是否有更优的实现，因为最自信一个对象非空时，空指针异常很可能发生。

**函数let**：提供函数式API的编程接口，并将原始调用对象作为参数传递给lambda表达式。

```Kotlin
obj.let { obj2 ->
	/*具体逻辑*/
}
```

在对象`obj`上调用let时，lambda将`obj`本身作为参数传递（为了防止重名，将参数名写作`obj2`，实际上它与`obj`是同一个对象）。

```Kotlin
/*用let简化doStudy*/
fun doStudy(study: Study?)
{
	study?.let { stu ->
		stu.readBooks()
		stu.doHomework()
	}
}
```

let将`study`传递，在lambda中对其调用方法。

之前提到，如果lambda的参数列表中只有一个参数，可以省略参数名并以关键字it代替。

```Kotlin
/*用lambda规则简化*/
fun doStudy(study: Study?)
{
	study?.let {
		it.readBooks()
		it.doHomework()
	}
}
```

let可以处理全局变量的判空，而if无法做到。

```Kotlin
/*将doStudy的参数改为全局变量*/
var study: Study? = null

fun doStudy()
{
	if (study != null)
	{
		study.readBooks()
		study.doHomework()
	}
}
```

无法编译，因为全局变量的值随时有可能被其他线程修改，即使判空也无法保证非空。