## 1. 接口

### 1) 接口的概念

**接口**(interface)：描述类应该做什么（而不指定具体如何做），它不是类，而是对符合此接口的类的一组需求。

例如，方法Arrays.sort可以排序对象数组，同时要求对象所属的类实现Comparable接口。

```Java
/*Comparable的定义*/
public interface Comparable
{
	int compareTo(Object other);
}
```

==抽象方法compareTo没有具体实现，它将被实现Comparable接口的类实现==。

接口中的==方法自动为public==，==字段自动为public static final==，都无需标记。

接口中可以有多个方法、常量，但==不能有实例字段==。

要使类实现接口，需要：

1. 将类声明为实现该接口。
2. 对该接口中的所有方法提供定义。

**关键字implements**：声明类实现某个接口。

```Java
/*将Employee声明为实现Comparable*/
class Employee implements Comparable
```

现在，Employee类需要提供compareTo的定义。

```Java
/*实现compareTo，以薪水排序*/
public int compareTo(Object otherObject)
{
	Employee other = (Employee) otherObject;
	return Double.compare(salary, other.salary);
}
```

**方法Double.compare**：*param1*<*param2*返回负值，相等返回0，否则返回正值。

compareTo在接口中没有标记public（因为接口中的方法自动为public），但是==提供定义时必须标记public，否则编译器将默认其访问属性为包可访问==。

```Java
/*Employee实现泛型Comparable*/
class Employee implements Comparable<Employee>
{
	public int compareTo(Employee other)
	{
		return Double.compare(salary, other.salary);
	}
	...
}
```

使用接口的理由：Java是一种强类型语言，==调用方法时编译器要能检查其确实存在==。

```Java
/*sort中可能的语句*/
if (a[i].compareTo(a[j]) > 0)
{
	//调换a[i]与a[j]
	...
}
```

编译器必须确认对`a`一定有可用的方法compareTo。如果`a`是一个Comparable对象的数组，就可以确保其存在（因为任何实现Comparable接口的类都必须实现该方法）。

### 2) 接口的属性

接口不是类，==不能用new实例化一个接口==，不过可以声明接口变量。

```Java
/*接口变量的声明*/
x = new Comparable(...); //ERROR
Comparable x; //OK
```

接口变量只能引用实现了该接口的一个类对象：

```Java
x = new Employee(...);
```

使用==instanceOf检查对象是否实现了某个接口==：

```Java
if (anObject instanceOf Comparable) ...;
```

接口可以扩展。

```Java
/*接口Moveable*/
public interface Moveable
{
	void move(double x, double y);
}
```

```Java
/*接口Powered扩展Moveable*/
public interface Powered extends Moveable
{
	double milesPerGallon();
	double SPEED_LIMIT = 95;
}
```

==每个类只能有一个父类，但可以实现多个接口==，例如，如果一个类实现Java的内置接口Cloneable，方法Object.clone就可以创建该类对象的一个完全副本。

如果类实现多个接口，在声明中用逗号分隔。

```Java
/*声明类实现多个接口*/
class Employee implements Cloneable, Comparable
```

### 3) 接口与抽象类

```Java
/*将Comparable设计为一个抽象类*/
abstract class Comparable
{
	public abstract int compareTo(Object other);
}
```

这样，Employee类只需要继承Comparable，并实现compareTo。

```Java
/*Employee继承Comparable*/
class Employee extends Comparable
{
	public int compareTo(Object other) ...;
}
```

之前提到，每个类只能拥有一个父类，此例中，==Employee已经有父类Person，就不能再扩展Comparable==。

其他一些语言允许一个类拥有多个父类（C++），这个特性称为**多重继承**。它将使语言变得复杂、降低效率。

### 4) 静态和私有方法

> P241

### 5) 默认方法

Java 8之前，接口中的方法只能为抽象方法，而Java 8之后，可以为接口方法提供一个默认实现，用**修饰符default**标记。

```Java
public interface Comparable<T>
{
	default int compareTo(T other) { return 0; }
}
```

Comparable接口的每个实现都将覆盖它，不过有些情况下它也有用处（第7章）。

默认方法可以调用其他方法。

```Java
/*为Collection接口定义一个常用方法isEmpty*/
public interface Collection
{
	int size();
	default boolean isEmpty() { return size() == 0; }
}
```

这样，实现Collection的程序员就无需自行实现经常需要使用的isEmpty。

默认方法的一个重要用途是**接口演化**，以Collection接口为例，假设从前有人写了一个Bag类，它实现Collection，后来为Collection增加了一个方法stream，如果stream不是默认方法，那么==Bag类将无法编译，因为它没有实现此新方法==。默认方法保证了**源代码兼容**。

### 6) 解决默认方法冲突

> P242-244

### 7) 接口与回调

**回调**(callback)：一种程序设计模式，可以指定某个特定事件发生时采取的动作，例如用户点击按钮时触发的动作。

java.swing包中有一个Timer类，假设程序中有一个时钟，它可以通过Timer每秒更新表盘。

实例化Timer时，需要设置一个时间间隔，并告诉它经过这些时间要做什么。很多语言中可以提供函数名，而Java中可以向Timer传递一个对象，并指定Timer对其调用某个方法。

Timer要求指定一个对象，且其对应的类需要实现java.awt.event包的ActionListener接口。

```Java
/*ActionListener的定义*/
public interface ActionListener
{
	void actionPerformed(ActionEvent event);
}
```

每当经过一定时间，Timer对象将调用方法actionPerformed，例如，将actionPerformed实现为每秒打印一条消息并发出提示音：

```Java
class TimePrinter implements ActionListener
{
	public void actionPerformed(ActionEvent event)
	{
		System.out.println("At the tone, the time is " + Instant.ofEpochMilli(event.getWhen()));
		Toolkit.getDefaultToolkit().beep();
	}
}
```

然后，实例化TimePrinter，将该对象传递给Timer的构造器：

```Java
var listener = new TimePrinter();
var timer = new Timer(1000, listener);
```

Timer构造器的==第一个参数是一个时间间隔==（单位ms），即经过多长时间触发一次，第二个参数是一个监听器对象（实现了ActionListener的类的实例）。

最后，用**方法start**启动计时器：

```Java
timer.start();
```

```Java
/*Timer的使用*/
import java.awt.*;
import java.awt.event.*;
import java.time.*;
import javax.swing.*;

public class TimerTest
{
	public static void main(String[] args)
	{
		var listener = new TimePrinter();
		var timer = new Timer(1000, listener);
		timer.start();
		JOptionPane.showMessageDialog(null, "Quit program?");
		System.exit(0);
	}
}

class TimePrinter implements ActionListener
{
	public void actionPerformed(ActionEvent event)
	{
		System.out.println("At the tone, the time is " + Instant.ofEpochMilli(event.getWhen()));
		Toolkit.getDefaultToolkit().beep();
	}
}
```

### 8) Comparator接口

String类实现了Comparable接口，方法String.compareTo可以按字典顺序比较字符串。现在要按长度顺序对字符串排序，但不可能在String类中用两种不同方式实现compareTo，要处理这种情况，Arrays.sort还有第二个版本，接受一个数组和==一个实现了Comparator的类的实例==作为参数。

```Java
/*泛型Comparator的定义*/
public interface Comparator<T>
{
	int compare(T first, T second);
}
```

编写类LengthComparator，实现Comparator并指定泛型类型为String：

```Java
class LengthComparator implements Comparator<String>
{
	public int compare(String first, String second)
	{
		return first.length() - second.length();
	}
}
```

完成比较时，实例化LengthComparator，将该对象传递给Arrays.sort：

```Java
var comp = new LengthComparator();
Arrays.sort(words, comp);
```

### 9) 对象克隆

> P247-252

## 2. lambda表达式

### 1) 为什么引入lambda表达式

lambda表达式是一个可传递的代码块，可以在以后执行若干次。

观察上文TimerTest和LengthComparator的例子，它们有一个共同点：都将一个代码块传递给某个目标（Timer或sort），这段代码将在未来某个时间调用。

### 2) lambda表达式的语法

在上例LengthComparator中，要计算`first.length() - second.length()`，由于Java是一种强类型语言，要指定`first`和`second`的类型，改写如下：

```Java
(String first, String second) ->
	first.length() - second.length();
```

这是一个lambda表达式，它是==一个代码块、以及传入该块的所有变量的规范==。

如果代码块的任务无法用一个表达式完成，就要像编写方法一样使用中括号({})，并==显式地包含return语句==：

```Java
(String first, String second) ->
	{
		if (first.length() < second.length()) return -1;
		else if (first.length() > second.length()) return 1;
		else return 0;
	}
```

即使lambda没有参数，==也要像编写无参数方法一样提供括号==：

```Java
() -> { for (int i = 0; i < 100; i++) System.out.println(i); }
```

==如果编译器能够确定某个参数类型，则可以不标注==：

```Java
Comparator<String> comp = 
	(first, second) ->
		first.length() - second.length();
```

在这里，编译器可以推理出`first`和`second`必然是字符串（因为此lambda将返回值赋给字符串比较器`comp`）。

==如果只有一个参数且其类型可以确定，则小括号也可以省略==：

```Java
ActionListener listener = event ->
	System.out.println("At the tone, the time is " + Instant.ofEpochMilli(event.getWhen()));
```

无需指定lambda的返回值类型，因为它总是由上下文推理得出。

存在不返回任何值的分支的lambda不合法：

```Java
(int x) -> { if (x > 0) return 1; }
```

### 3) 函数式接口

**函数式接口**(functional interface)：只有一个抽象方法的接口，==需要其实例时可以重写为提供一个lambda==。

上文的方法Arrays.sort调用：

```Java
var comp = new LengthComparator();
Arrays.sort(words, comp);

class LengthComparator implements Comparator<String>
{
	public int compare(String first, String second)
	{
		return first.length() - second.length();
	}
}
```

`comp`是实现了Comparator的LengthComparator的实例，而Comparator是一个函数式接口，所以可以改写为：

```Java
Arrays.sort(words, (first, second) -> first.length() - second.length());
```

最好==将lambda看作一个函数，而非一个对象==。

lambda可以转换为一个接口，例如上文的例子TimerTest：

```Java
var listener = new TimePrinter();
var timer = new Timer(1000, listener);

class TimePrinter implements ActionListener
{
	public void actionPerformed(ActionEvent event)
	{
		System.out.println("At the tone, the time is " + Instant.ofEpochMilli(event.getWhen()));
		Toolkit.getDefaultToolkit().beep();
	}
}
```

改写为：

```Java
var timer = new Timer(1000, event ->
	{
		System.out.println("At the tone, the time is " + Instant.ofEpochMilli(event.getWhen()));
		Toolkit.getDefaultToolkit().beep();
	});
```

后者更短，而且不需要单独编写TimerPrinter，可读性更好。

java.util.function包中有一个有用的**接口Predicate**。

```Java
/*Predicate的定义*/
public interface Predicate<T>
{
	boolean test(T, t);
}
```

ArrayList中有一个**方法removeIf**，其参数为一个实现了Predicate的类的实例，Predicate专门传递lambda，例如，删除`list`中的所有null值：

```Java
list.removeIf(e -> e == null);
```

另一个有用的函数式接口是**接口Supplier**。

```Java
/*Supplier的定义*/
public interface Supplier<T>
{
	T get();
}
```

supplier没有参数，调用时将生成一个T类型的值，它用以实现**懒计算**(lazy evaluation)：

```Java
LocalDate hireDay = Objects.requireNonNullElse(day, LocalDate.of(1970, 1, 1));
```

由于`day`很少为null，可以只在必要时才构造默认的LocalDate，使用供应者改进：

```Java
LocalDate hireDay = Objects.requireNonNullElseGet(day, () -> LocalDate.of(1970, 1, 1));
```

方法requireNonNullElseGet只在需要值时才调用supplier。

### 4) 方法引用

有时lambda涉及一个方法，例如调用：

```Java
var timer = new Timer(1000, event -> System.out.println(event));
```

如果直接把方法println传递给timer构造器就更好了：

```Java
var timer = new Timer(1000, System.out::println);
```

表达式`System.out::println`是一个**方法引用**，它指示编译器==生成一个函数式接口的实例，覆盖该接口的抽象方法来调用给定的方法==。此例中，编译器将生成一个ActionListener实例，其actionPerformed(ActionEvent e)将调用System.out.println(e)。

```Java
/*用方法引用排序字符串数组*/
Arrays.sort(words, String::compareToIgnoreCase);
```

用双冒号(::)分隔方法名与对象或类名，有3种情况：

- *object*::*instanceMethod*，==方法引用等价于一个lambda==，其参数要传递给方法。
- *Class*::*instanceMethod*，==首个参数将成为方法的隐式参数==，例如上例中的`String::compareToIgnoreCase`等价于`(x, y) -> x.compareToIgnoreCase(y)`。
- *Class*::*staticMethod*，==所有参数都将成为方法的显式参数==，例如`Math::pow`等价于`(x, y) -> Math.pow(x, y)`。

> 更多示例见P259表6-1

==只有当lambda只调用一个方法且无其他操作时，才能将其重写为方法引用==。例如，`s -> s.length() == 0`就不能重写为方法引用，因为除length调用外，还进行了比较。

可以在方法引用中使用this参数，例如，`this::equals`等价于`x -> this.equals(x)`。

类似地，也可以使用super。

```Java
/*super在方法引用中的使用*/
class Greeter
{
	public void greet(ActionEvent event)
	{
		System.out.println("Hello, the time is " + Instant.ofEpochMilli(event.getWhen()));
	}
}

class RepeatedGreeter extends Greeter
{
	public void greet(ActionEvent event)
	{
		var timer = new Timer(1000, super::greet);
		timer.start();
	}
}
```

方法RepeatedGreeter.greet开始执行后，构造一个Timer实例`timer`，然后每隔1000ms执行一次父类方法Greeter.greet。

### 5) 构造器引用

构造器引用与方法引用类似，不过==方法名为new==，例如，`Person::new`是对Person构造器的引用，具体构造器的选择取决于上下文。

可以建立数组类型的构造器引用，例如，`int[]::new`，它有一个参数，即数组的长度，等价于`x -> new int[x]`。

### 6) 变量作用域

有时需要在lambda中访问外围方法或类中的变量：

```Java
public static void repeatMessage(String text, int delay)
{
	ActionListener listener = event ->
		{
			System.out.println(text);
			Toolkit.getDefaultToolkit().beep();
		};
	new Timer(delay, listener).start();
}
```

lambda访问外围方法的参数变量`text`，但实际上它可能在方法repeatMessage调用返回很久后才执行，而那时`text`早已不存在，为什么还能够访问？

lambda有3个部分：

- 一个代码块；
- 参数；
- 自由变量（不是参数且不在代码中定义的变量）的值。

上例中，`text`将存储在lambda对应的数据结构中，称为**捕获**(captured)，代码块连同自由变量称为**闭包**(closure)。

为了确保捕获的值是明确定义的，==lambda只能引用值不会改变的变量==。

```Java
/*尝试在lambda中更改变量的值将出错*/
ActionListener listener = event ->
	{
		start--; //ERROR：lambda表达式中使用的变量应为final或有效final
		System.out.println(start);
	};
```

此限制的原因：如果在lambda中更改变量的值，并发执行多个动作时将不安全。

另外，==lambda引用的变量在外部也不能改变==。

```Java
for (int i = 0; i <= count; i++)
{
	ActionListener listener = event ->
		{
			System.out.println(i); //ERROR
		};
}
```

lambda捕获的变量必须是**事实最终变量**(effectively final)，它是指，对变量==初始化之后就不再为其重新赋值==（即使没有标记final）。

lambda的块遵循嵌套块的作用域规则，同样适用==命名冲突与覆盖==。

在lambda中使用this时，指的是==创建此表达式的方法的this参数==。

```Java
/*在lambda中使用this*/
public class Application
{
	public void init()
	{
		ActionListener listener = event ->
			{
				System.out.println(this.toString());
				...
			};
		...
	}
}
```

表达式`this.toString()`将调用Application类型对象的方法toString，而不是ActionListener实例`listener`的方法toString。

### 7) 处理lambda表达式

使用lambda的优点是**延迟执行**(deferred execution)，之所以想要以后再执行，有很多可能原因：

- 在一个单独的线程中运行代码。
- 多次运行代码。
- 在算法的适当位置运行代码（如排序算法的比较操作）。
- 发生某种情况时运行代码。
- 只在必要时运行代码。

例如，将一个动作重复n次，可以编写方法repeat，接收重复次数与重复动作，后者用lambda表达。要接受此lambda，需要选择一个函数式接口，此例中使用Runnable：

```Java
public static void repeat(int n, Runnable action)
{
	for (int i = 0; i < n; i++) action.run();
}

repeat(10, () -> System.out.println("Hi!"));
```

> 常用的函数式接口见P263表6-2

现在需要指定在具体的哪一次迭代中执行动作，为此需要选择一个合适的函数式接口，其方法要有一个int参数且返回值类型为void，一般用**IntConsumer接口**处理int值。

```Java
/*IntConsumer的定义*/
public interface IntConsumer
{
	void accept(int value);
}
```

```Java
/*更改repeat*/
public static void repeat(int n, IntConsumer action)
{
	for (int i = 0; i < n; i++) action.accept(i);
}

repeat(10, i -> System.out.println("Countdown: " + (9 - i)));
```

> 基本类型的特殊化接口见P264表6-3

### 8) 再谈Comparator

> P266-267

## 3. 内部类

**内部类**(inner class)是定义在另一个类中的类，使用内部类的可能原因：

- 内部类可以对同一个包中的其他类隐藏。
- 内部类方法可以访问定义这些方法的作用域中的数据，包括原本私有的数据。

### 1) 使用内部类访问对象状态

```Java
/*从上例TimerTest中提取TalkingClock类*/
public class TalkingClock
{
	private int interval;
	private boolean beep;
	public TalkingClock(int interval, boolean beep) { ... }
	public void start() { ... }
	public class TimePrinter implements ActionListener
	{
		public void actionPerformed(ActionEvent event)
		{
			System.out.println("At the tone, the time is " + Instant.ofEpochMilli(event.getWhen()));
			if (beep) Toolkit.getDefaultToolkit().beep();
		}
	}
}
```

可以看到，一个内部类方法可以访问自身的实例字段，也可以访问==创建它的外部类对象的实例字段==（此例中为`beep`）。

因此，内部类的对象总有一个隐式引用，==指向创建它的外部类对象==，该引用在内部类的定义中不可见。

为了说明此概念，将该引用称为`outer`，TimePrinter类改写为：

```Java
public class TimePrinter implements ActionListener
{
	public void actionPerformed(ActionEvent event)
	{
		System.out.println("At the tone, the time is " + Instant.ofEpochMilli(event.getWhen()));
		if (outer.beep) Toolkit.getDefaultToolkit().beep(); //outer.beep
	}
}
```

外部类的引用在构造器中设置，编译器将为内部类的所有构造器添加一个对应外部类引用的参数，此例中TimePrinter类没有定义构造器，编译器为其生成了一个无参数的构造器：

```Java
public TimePrinter(TalkingClock clock)
{
	outer = clock;
}
```

再次强调，==outer并不是Java的关键字，此处只是引入它来说明该引用的概念==。

在方法start中构造一个TimePrinter类型对象后，编译器将当前TalkingClock类型对象的this引用传递给它：

```Java
var listener = new TimePrinter(this);
```

如果TimePrinter类是一个普通的类，它就需要通过TalkingClock类的公共方法访问`beep`标志，而==使用内部类就避免了提供只对另外一个类有用的访问器==。

可以将TimePrinter类标记private，这样一来只有TalkingClock类的方法能构造TimePrinter对象。==只有内部类可以为私有，常规类只能为包可见或公共可见==。

### 2) 内部类的特殊语法规则

上一节引入outer来解释内部类对外部类的引用，实际上，该引用的正规语法是*OuterClass*.this。

于是内部类TimePrinter可以改写为：

```Java
public class TimePrinter implements ActionListener
{
	public void actionPerformed(ActionEvent event)
	{
		...
		if (TalkingClock.this.beep) Toolkit.getDefaultToolkit().beep();
	}
}
```

反过来，可以用以下语法更明确地编写内部类对象的构造器：

```Java
*outerObject.new *InnerClass(*constructionParams)
```

```Java
/*内部类对象的构造器*/
ActionListener listener = this.new TimePrinter();
```

构造的TimePrinter对象的外部类引用被设置为构造它的方法的this引用，所以限定符this是多余的，不过也有可能通过显式地命名将外部类引用设置为其他对象：

```Java
var jabberer = new TalkingClock(1000, true);
TalkingClock.TimePrinter listener = jabberer.new TimePrinter();
```

在外部类的作用域之外，可以用以下语法引用内部类：

*OuterClass*.*InnerClass*

### 3) 内部类是否有用、必要和安全

内部类将转换为常规的类文件，用美元符号($)分隔外部类名与内部类名，例如上例中的TalkingClock类内部的TimePrinter类将转换为类文件TalkingClock\$TimePrinter.class。

==内部类可以访问外部类的私有数据，比常规类功能更强==。

> 编译器处理类的嵌套关系、其原理及历史见P272-273

### 4) 局部内部类

在上例中，类名TimePrinter只在TalkingClock类的定义中出现了一次（即创建其对象时）。

在类似的情况下，可以在一个方法中局部地定义此内部类：

```Java
public class TalkingClock
{
	private int interval;
	private boolean beep;
	public TalkingClock(int interval, boolean beep) { ... }
	public void start()
	{
		class TimePrinter implements ActionListener
		{
			public void actionPerformed(ActionEvent event)
			{
				System.out.println("At the tone, the time is " + Instant.ofEpochMilli(event.getWhen()));
				if (beep) Toolkit.getDefaultToolkit().beep();
			}
		}
	}
}
```

==声明局部内部类时不能标记其访问属性==，==其作用域为声明它的块==。

局部内部类的优点：

- ==对外部完全隐藏==，此例中，除方法start以外，没有任何方法知道TimePrinter类的存在。
- ==不仅能访问外部类的字段，还能访问局部变量==（不过只能是事实最终变量）。

### 5) 由外部方法访问变量

> P274-275

### 6) 匿名内部类

有时只想创建类的一个对象，甚至不需要为类命名，这样的类称为**匿名内部类**(anonymous inner class)。

```Java
public void start(int interval, boolean beep)
{
	var listener = new ActionListener()
		{
			public void actionPerformed(ActionEvent event)
			{
				System.out.println("At the tone, the time is " + Instant.ofEpochMilli(event.getWhen()));
				if (beep) Toolkit.getDefaultToolkit().beep();
			}
		};
	var timer = new Timer(interval, listener);
	timer.start();
}
```

它的含义是：创建一个类的对象，该类实现了ActionListener接口，需要实现的方法actionPerformed在花括号中定义。

格式：

new *SuperType*(*construction params*)
{
	*inner class methods and data*
}

*SuperType*可以是接口（如果是，内部类需要实现该接口），也可以是类（如果是，内部类需要扩展该类）。

构造器的名称必须与类名相同，而==匿名内部类没有类名，所以它不能有构造器==，其构造参数传递给父类构造器；==如果它实现一个接口，就不能有构造参数，不过仍需提供小括号(())==。

尽管没有构造器，匿名内部类可以提供一个对象初始化块：

```Java
var count = new Person("Dracula")
	{
		{ //initialization }
		...
	}
```

==如果构造参数列表的右括号())后跟左花括号({)，就是在定义匿名内部类==。

通常使用匿名内部类的解决方案比较简短；对于事件监听器和其他回调的实现，最好还是使用lambda表达式。

如果将一个匿名内部类实例存储在声明为var的变量中：

```Java
var bob = new Object() { String name = "Bob"; }
System.out.println(bob.name);
```

`new Object() { String name = "Bob"; }`构造的对象类型为“有一个String name字段的Object”，它是一个**不可指示的**(nondenotable)类型，即无法用Java语法表示，不过编译器可以理解并将`bob`设置为此类型。==但如果将==`bob`==声明为Object类型，==`bob.name`==将编译失败==。

对于方法equals，一般如下实现：

```Java
if (getClass() != other.getClass()) return false;
```

但对于匿名内部子类，这个测试不可用。

### 7) 静态内部类

> P278-281

## 4. 服务加载器

> P281-283

## 5. 代理

> P283-289