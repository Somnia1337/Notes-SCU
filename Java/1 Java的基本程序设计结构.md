## 1. 一个简单的Java程序

```Java
/*Java HelloWorld*/
public class HelloWorld
{
    public static void main(String[] args)
    {
        System.out.println("Hello, world!"); //*
    }
}
```

**关键字class**：表明Java程序的全部内容都包含在**类**(class)中。

**关键字public**：访问修饰符，表明这个类是**公共类**（公共可见）。

**类名**Hello：==必须以字母开头==，后跟字母、数字的任意组合（习惯上使用**骆驼命名法**(camel case)，即==每个单词首字母为大写==）。

**源代码文件**：==文件名必须与公共类的类名相同，并以.java为扩展名==。==一个源文件中只能有1个公共类==，而可以有任意个非公共类。

**方法main**：每个Java程序都必须包含的方法（Java中的“方法”即为C中的“函数”）。

**方法println**：在打印结束后自动添加'\\n'，System.out对象还有一个不自动添加'\\n'的方法print。

**点号(.)**：调用方法，星标语句对System.out调用其方法println。

```Java
/*方法的调用格式*/
*object.*method(*parameters)
```

## 2. 注释

/ /、/\* \*/：用法与C相同，==/\* \*/不能像括号一样嵌套==，第一次出现的\*/将与开头的/\*匹配。

/\*\* \*/：用于自动生成文档。

## 3. 数据类型

8种基本类型：4种整型，2种浮点类型，1种字符类型，1种布尔类型。

### 1) 整型

|类型|存储大小/字节|
|-|-|
|byte|1|
|short|2|
|int|4|
|long|8|

==Java中整型的取值范围与具体平台无关==，无论移植到哪里，一个类型的存储大小都不改变。

长整型数值有后缀L/l（例如4000000000L），十六进制数值有前缀0X/0x（例如0xCAFE），八进制数值有前缀0（例如010，等于十进制下的8），二进制数值有前缀0B/0b（例如0b1001，等于十进制下的9）。

可以为数字字面量加下划线以提高其可读性（例如1_000_000），编译时它将被删除。

不存在无符号形式(unsigned)。

### 2) 浮点类型

|类型|存储大小/字节|
|-|-|
|float|4|
|double|8|

float值有后缀F/f，==没有后缀的小数默认为double类型==，也可以为double值添加后缀D/d。

浮点数值的计算过程中将产生舍入误差（例如2.0-1.1的打印结果为0.8999999999999999），如需绝对精确，应使用**BigDecimal类**。

### 3) char类型

最好不使用char，除非确实需要处理字符。

### 4) boolean类型

2种值：false、true。

==整型值与布尔值不能相互转换==，在C中常错写的`if (x = 0)`（应为`x == 0`）在Java中无法编译。

## 4. 变量与常量

### 1) 声明变量

**标识符**：可由==字母、数字、货币符号以及标点连接符==组成，==首字符不能为数字==。

“字母”可为表示自然语言字母的任何Unicode字符，包括ä、π等，“标点连接符”包括下划线(\_)、波浪线(﹏)等。

### 2) 初始化变量

**关键字var**：对于局部变量，如果可以从变量的初始值推断其类型，就只需使用关键字var，而不再需要声明类型。

```Java
/*var的使用*/
var vacationDays = 12;
var greeting = "Hello";
```

### 3) 常量

**关键字final**：将变量声明为常量。final表示这个变量的值已经是最终值、不能再更改。

```Java
/*final的使用*/
final double CM_PER_INCH = 2.54; //这句话需在类外，即直属于源文件
```

习惯上==常量名全部字母为大写==。

**类常量**（见\[2 对象与类 4. 静态字段与静态方法 2) 静态常量]）：在类内声明的常量，此时除final外，还需**关键字static**。

```Java
/*类常量的声明与使用*/
public class Constants
{
    public static final double d = 2.54;
	
    public static void main(String args[])
    {
        double w = 8.5;
        double h = 11;
        System.out.println("Paper size in centimeters: " + w * d + " by " + h * d);
    }
}
```

==如果一个类常量被标记public，那么其他类也可以访问它==（*Class*.*Field*），此例中其他类可以通过`Constants.d`访问常量`d`。

保留字const：==目前未使用==，仍需用final声明常量。

### 4) 枚举类型

有时一个变量只包含有限种可能值（如披萨的小、中、大、超大），可以使用枚举类型。

```Java
/*一个简单的枚举类型*/
enum Size
{SMALL, MEDIUM, LARGE, EXTRA_LARGE}

Size s = Size.MEDIUM;
```

==当枚举内只有枚举时，可以不加分号==，而有其他内容（方法、属性、构造函数等）时必须加分号。

Size类型的变量只能存储它的声明中所列的某个值或null（表示此变量没有设置任何值）。

## 5. 运算符

### 1) 算术运算符

整数除以0将==产生一个异常==，浮点数除以0将得到==无穷大或NaN==(Not a Number)。

### 2) 数学函数与常量

**方法sqrt**：计算平方根。

```Java
/*sqrt的使用*/
double e = Math.sqrt(16);
System.out.println("e = " + e);
```

**静态方法**：不处理对象的方法，sqrt即为静态方法。

**方法floorMod**：计算最小正余数。

```Java
/*floorMod的使用*/
double f = Math.floorMod(a, b);
```

普通的求余运算符(%)可能返回负结果（当被除数为负、除数为正时），floorMod能解决此问题。遗憾的是，floorMod在被除数为正、除数为负时也将返回负结果。

```Java
/*Math类的其他常用方法与常量*/
/*三角函数*/
Math.sin
Math.cos
Math.tan
Math.atan
Math.atan2

/*幂函数，指数函数及其反函数*/
Math.pow
Math.exp
Math.log //自然对数
Math.log10

/*常量*/
Math.PI
Math.E
```

如需多次使用Math类的方法，不必每次都加`Math.`，而可以在源文件的开头导入Math：

```Java
import static java.lang.Math.*;
```

Math的方法在计算时全部使用double，这可能导致==不同平台上的运行结果不同==，为了避免，可以使用StrictMath类（类似于Math，不过运行速度慢）。

如果一个计算结果溢出（如10亿\*3，超过最大的int），算术运算将返回错误结果而不产生异常，此时应使用**方法Math.multiplyExact**，调用`multiplyExact(1000000000, 3)`将抛出一个异常。类似的方法还有**addExact**，**subtractExact**等。

### 3) 类型转换

3种可能损失精度的类型转换：==int → float==，==long → float==，==long → double==。

### 4) 强制类型转换

```Java
/*强制类型转换的使用*/
double x = 9.997;
int nx = (int)x; //9
```

强制转换将截断小数部分。

**方法Math.round**：对浮点数四舍五入。

```Java
double x = 9.997;
int nx = (int)Math.round(x); //10
```

round返回值类型为long，==仍需显式地强制转换==。

如果强制转换时==数值超出目标类型的上限==（例如byte(300)），结果将==截断为一个错误值==。

### 5) 赋值

如果运算结果与左侧操作对象的类型不符，结果将被自动强制转换。

```Java
/*自动强制转换*/
int x = 0;
x += 3.5; //3

//等价于

int x = 0;
x = (int)(x + 3.5); //3
```

### 6) switch表达式

```Java
/*switch表达式的使用*/
String seasonName = switch (seasonCode)
{
    case 0 -> "Spring";
    case 1 -> "Summer";
    case 2 -> "Fall";
    case 3 -> "Winter";
    default -> "???";
};
```

可以为一个case指定多个标签，用逗号分隔：

```Java
String isOdd = switch (number)
{
    case 1, 3, 5 -> "Yes";
    case 2, 4, 6 -> "No";
    default -> "???";
};
```

参数类型为整数或字符串的switch表达式==必须有标签default==。

## 6. 字符串

### 1) 子串

**方法substring**：截取字符串的子串。

```Java
/*substring的格式*/
String subString = *motherString.substring(*start, *end);
```

`start`为开始截取的字符编号，`end`为==刚好不截取==的第一个字符编号。

```Java
/*substring的使用*/
String greeting = "Hello";
String s = greeting.substring(0, 3); //"Hel"
```

### 2) 拼接

**字符串连接符**(+)：连接字符串。

```Java
/*字符串连接符的使用*/
String head = "Clash";
String tail = "Royale";
String combinedWord = head + tail;
```

`combinedWord`值为"ClashRoyale"。

==当一个非字符串的值与字符串连接时，它将转换为字符串==，此特性常用于打印语句。

```Java
/*用字符串连接符将非字符串值转换为字符串*/
int answer = 1;
System.out.println("The answer is " + answer + "."); //The answer is 1.
```

**方法join**：将多个字符串拼接为一个，以指定的界定符(delimiter)分隔。

```Java
/*join的格式*/
String joinedString = String.join(*delimiter, *substring1, *substring2, ...);
```

第一个参数`delimiter`为界定符字符串，它将出现在拼接后的每两个子串之间。

```Java
/*join的使用*/
String all = String.join(" / ", "A", "B", "C"); //"A / B / C"
```

**方法repeat**：将一个字符串重复多次拼接为一个新串。

```Java
/*repeat的格式*/
*string.repeat(*times)。
```

```Java
/*repeat的使用*/
String repeated = "Java".repeat(3); //"JavaJavaJava"
```

### 3) 不可变

String类没有提供方法来直接修改字符串变量的值，可以==提取要保留的子串，再拼接上替换的字符==实现间接修改。

```Java
/*间接修改字符串*/
String word = "Hello";
word = word.substring(0, 3) + "p"; //"Help"
```

C与Java字符串的区别：

- C的字符串是字符数组（char \[]），Java的字符串大致相当于C的指针（char*）。
- ==C的字符串可以修改、不可共享==（因为是独属于某个变量的值），==Java的字符串不可修改、可以共享==（可以认为Java的字符串都存储在一个公共库内，字符串变量只是指向某个位置）。

### 4) 比较

**方法equals**：比较两个字符串，区分大小写。

```Java
/*equals的格式*/
*a.equals(*b);
```

如果`a`、`b`相同返回true，否则返回false。

**方法equalsIgnoreCase**：比较两个字符串，忽略大小写。

==不可以用双等号(\==)比较两个字符串==（除非用于Null串），因为它只能确定两个字符串是否存放在相同位置，而实际上只有字符串字面常量共享。

### 5) 空串与Null串

**空串**：==长度为0==的字符串（""）。

```Java
/*检测字符串是否为空串*/
if (str.length() == 0)
//或者
if (str.equals(""))
```

**Null串**：==值为null==的字符串，null表示目前没有任何对象与之关联。

```Java
/*检查字符串是否为Null串*/
if (str == null)
```

### 6) 构建字符串

可以用**StringBuilder类**高效地拼接很多字符串。

```Java
/*StringBuilder的使用*/
/*首先，构建一个StringBuilder实例*/
StringBuilder builder = new StringBuilder;

/*每次需要添加新的部分时，调用方法append*/
builder.append(ch); //添加单个字符
builder.append(str); //添加字符串

/*构建完成时，调用方法toString*/
String completedString = builder.toString();
```

### 7) 文本块

**文本块**：可以换行的字符串。

```Java
"Hello\nWorld\n"

//此字符串含有多个换行符，不易于读写，它等价于文本块

"""
Hello
World
"""
```

开始三引号(""")后的换行符不属于文本块，而==结束三引号(""")前的换行符属于文本块==。

文本块适合用于包含其他语言编写的代码，如SQL和HTML。

## 7. 输入与输出

### 1) 读取输入

**Scanner类**在java.util包中定义，读取控制台输入前，要先构建一个与“标准输入流”System.in关联的Scanner实例。

```Java
/*Scanner实例的构建*/
import java.util.*;

Scanner in = new Scanner(System.in);
```

**方法nextLine**：读取一行输入。

**方法next**：读取一个单词（以空格作为分隔符）。

**方法nextInt**：读取一个整数。

**方法nextDouble**：读取一个浮点数。

```Java
/*Scanner方法的格式*/
*ScannerName.*method();
```

```Java
/*Scanner方法的使用*/
Scanner nameReader = new Scanner(System.in);
System.out.println("What's your name?");
String name = nameReader.nextLine();
```

### 2) 格式化输出

**方法print**：将参数打印到控制台。

**方法printf**：更精确地控制打印，其用法覆盖C的函数printf，另外还有新增。

> 用于printf的标志见P59

**方法format**：构建一个格式化的字符串（不打印到控制台）。

```Java
/*format的使用*/
String message = String.format("a = %d, b = %d", a, b);
```

以上语句还可以用**方法formatted**（Java 15）简化为：

```Java
/*formatted的使用*/
String message = "a = %d, b = %d".formatted(a, b);
```

### 3) 文件输入与输出

读取文件前，要先构建一个Scanner实例。

```Java
/*读取文件的Scanner实例的构建*/
Scanner in = new Scanner(Path.of("myfile.txt"), StandardCharsets.UTF_8);
```

写入文件前，要先构建一个PrintWriter实例。

```Java
/*写入文件的PrintWriter实例的构建*/
PrintWriter out = new PrintWriter("myfile.txt", StandardCharsets.UTF_8);
```

使用绝对路径时，要==在每个反斜线之前再加一个额外的反斜线转义==，例如`"c:\\files\\myfile.txt"`。

可以像输出到System.out一样==使用print、println、printf等方法输出到文件==。

## 8. 控制流程

### 1) 块

与C不同的是，Java==不允许在嵌套的块中声明同名变量==。

### 2) 条件语句

与C相同。

### 3) 循环

> 见\[10. 数组 3) for each循环]

### 4) switch（语句）

与\[5. 运算符 6) switch表达式]中的switch表达式不同，这里介绍的是switch语句。

switch在C中的经典形式也适用于Java。

> switch的4种形式见P73

case标签可以有多个字符串：

```Java
String input = ... ;
switch (input.toLowerCase())
{
    case "yes", "y" ->
    {
        ... ;
    }
    case "no", "n" ->
    {
        ... ;
    }
    default ->
    {
        ... ;
    }
}
```

**直通行为**：switch中执行某个标签后，在遇到break前，后续的标签也被执行的行为。

如果每个case==以冒号(:)结束，则有直通行为==，==以箭头(->)结束，则没有直通行为==。

==不能在一个switch中混用冒号和箭头==。

**关键字yield**：终止switch执行，并==额外生成一个值作为表达式的值==。

==switch的每个分支必须生成一个值==，这个值一般直接跟在箭头之后，但有时需要不止一条语句，此时应在箭头之后使用==花括号与yield==。

```Java
/*yield的使用*/
case "Spring" ->
{
    System.out.println("spring time!");
    yield 1;
}
```

switch==表达式优于语句==，如果在每个分支内为变量赋值/为方法调用值，则应使用表达式。

```Java
/*表达式*/
num = switch(season)
{
    case "Spring", "Summer", "Winter" -> 6
    case "Fall" -> 4
    default -> 1
}

//要优于

/*语句*/
switch(season)
{
    case "Spring", "Summer", "Winter" ->
      num = 6;
    case "Fall" ->
      num = 4;
    default ->
      num = 1;
}
```

### 5) 中断控制流程的语句

**带标签break**：可以跳出多重嵌套的循环。嵌套很深的循环语句可能产生意外结果，有时需要完全跳出所有嵌套循环。

**标签**：任意取名，放在想要跳出的最外层循环之前，后跟冒号。

```Java
/*带标签break的使用*/
read_data:
while()
{
    for()
    {
        if()
          break read_data;
    }
}
```

标签还可以放在==任何语句块==之前，遇到`break *lable`时将==跳出整个块==。

continue与C相同。

## 9. 大数

**BigInteger类**、**BigDecimal类**：java.math包中的两个类，可以处理包含任意长度数字序列的数值（有时基本的整数和浮点数精度不满足需求）。

```Java
BigInteger.ZERO
BigInteger.ONE
BigInteger.TWO
BigInteger.TEN
```

**方法valueOf**：将普通的数转换为大数。

```Java
/*valueOf的使用*/
BigInteger a = BigInteger.valueOf(100);
```

对于极长的数，需要构建一个实例（BigDecimal类型则总是必须构建实例）。

```Java
/*BigInteger实例的构建*/
BigInteger reallyBig = new BigInteger("2938547390493857652716904682634902717656783095880143523");
```

==大数运算时不能使用常规的运算符，只能使用大数类中的方法add、方法mutiply等==。

```Java
/*add的使用*/
BigInteger c = a.add(b); //c = a + b
```

## 10. 数组

### 1) 声明数组

```Java
/*数组的声明*/
int[] a = new int[100];
//或者
var a = new int[100];
//还可以使用int a[]
```

长度==不要求是常量==，不过创建之后无法改变。

可以提供初始化列表，此时不再需要new。

```Java
/*数组的初始化*/
int[] a = {2, 3, 5, 7, 11};
```

最后一个值后面允许有逗号，如需不断为数组添加新值，这样很方便。

**重新初始化**时需要new。

```Java
/*数组的重新初始化*/
a = new int[]{1, 3, 5, 7, 9};
```

**复制**：对新定义的数组用赋值运算符，将已有数组的内容复制到新数组。

```Java
/*数组的复制*/
int[] b = new int[]{2, 3, 5, 7, 11};
int[] c = b;
```

编写一个返回值类型为数组的方法时，如果结果为空，可以返回一个长度为0的数组。

```Java
/*长度为0的数组*/
new nullArray[0];
//或者
new nullArray[]{};
```

### 2) 访问数组元素

创建一个...

  - 数字数组时，所有元素默认初始化为==0==。
  - boolean数组时，所有元素默认初始化为==false==。
  - 对象（比如字符串）数组时，所有元素默认初始化为==null==（表示没有指向对象）。
  
**方法length**：返回数组中元素的个数。

```Java
/*length的使用*/
for (int i = 0; i < a.length; i++)
    System.out.println(a[i]);
```

### 3) for each循环

**for-each循环**：依次处理数组中的所有元素。

```Java
/*for-each的格式*/
for (*Type *variable : *collection) //"for each variable in collection"
{
	*statement
}
```

它将给定变量`*variable`依次设置为集合中的所有元素，并执行语句块`*statement`。`*collection`必须是一个数组或者实现Iterable接口的类对象（例如ArrayList）。

上文的代码可以用for-each简化为：

```Java
for (int element : a)
    System.out.println(element);
```

for-each循环的循环变量将==遍历数组中所有元素的值，而不是索引值==。

**方法Arrays.toString**：返回一个二维数组的元素列表字符串，形式如"\[2, 3, 5, 7, 11]"。

```Java
/*Arrays.toString的使用*/
System.out.println(Arrays.toString(a));
```

### 4) 数组拷贝

**数组拷贝**：将一个数组变量拷贝到另一个数组变量，此时两个变量将引用同一个数组。

```Java
int[] a;
int[] b = a;
b[5] = 12; //现在a[5]也为12
```

**方法Arrays.copyOf**：将一个数组所有元素的值拷贝到另一个数组。

```Java
/*Arrays.copyOf的使用*/
int[] copiedNumbers = Array.copyOf(numbers, numbers.length);
```

第二个参数为==新数组的长度==，它可以用于==扩容数组==。

```Java
numbers = Arrays.copyOf(numbers, 2 * numbers.length); //将原数组扩充到2倍
```

当然也可以==压缩数组==，此时将只拷贝指定长度内的一部分值。

### 5) 命令行参数

**参数String arg\[]**：出现在每个main中，==表示main将接收一个字符串数组==，也就是命令行上指定的参数。

在命令行中这样调用`java *ClassName -g a bbb`，`args`将包含`args[0]="g"`，`args[1]="a"`，`args[2]="bbb"`。

### 6) 数组排序

**方法Arrays.sort**：用==优化的快速排序算法==对数值型数组排序。

```Java
/*Arrays.sort的使用*/
int[] a = new int[10000];
...
Arrays.sort(a);
```

##### 实例：抽彩游戏（P84）

```Java
/*抽彩游戏*/
import java.util.*;

public class LotteryDrawing
{
    public static void main(String[] args)
    {
        Scanner in = new Scanner(System.in);
        System.out.println("How many numbers do you need to draw?");
        int k = in.nextInt();
        System.out.println("What is the highest number you can draw?");
        int n = in.nextInt();
        int[] numbers = new int[n];
        for (int i = 0; i < numbers.length; i++)
            numbers[i] = i + 1;
        int[] result = new int[k];
        for (int i = 0; i < result.length; i++)
        {
            int r = (int) (Math.random() * n);
            result[i] = numbers[r];
            numbers[r] = numbers[n - 1];
            n--;
        }
        Arrays.sort(result);
        System.out.println("Bet the following combination. It'll make you rich!");
        for (int r : result)
            System.out.println(r);
    }
}
```

**方法Math.random**：返回一个\[0, 1)的随机浮点数，用整型`n`乘以这个浮点数可以得到\[0, n-1]的随机整数。

### 7) 多维数组

==for-each只能循环处理“行”==，无法自动循环处理二维数组的所有元素，遍历整个二维数组时需要两个嵌套的for each。

```Java
/*嵌套for-each遍历二维数组*/
int[][] a = ...

for (int[] row : a)
    for (int value : row)
        ...
```

**方法Arrays.deepToString**：返回一个二维数组的元素列表字符串，形式如"\[\[1, 2], \[3, 4], \[5, 6]]"。

```Java
/*Arrays.deepToString的使用*/
System.out.println(Arrays.deepToString(a));
```

### 8) 不规则数组

**不规则数组**：各行长度不同的数组。

```TEXT
1
1 1
1 2 1
1 3 3 1
1 4 6 4 1
```

```Java
/*不规则数组的创建*/
/*首先，分配一个数组包含这些行*/
final int NMAX = 10;
int[][] odds = new int[NMAX + 1][];

/*然后，分配这些行*/
for (int n = 0; n < NMAX; n++)
    odds[n] = new int[n + 1];
    
/*分配之后，可以采用通常的方式访问其中的元素*/
for (int n = 0; n < odds.length; n++)
    for (int k = 0; k < odds[n].length; k++)
    {
        // 计算lotteryOdds
        ...
        odds[n][k] = lotteryOdds;
    }
```