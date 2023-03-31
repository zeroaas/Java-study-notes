#对象与类
---
##1.1.1 类（class）
类指定了如何构造对象。可以将类想象成一个小甜品模具，对象就是小甜品。
*由一个类**构造**对象的过程称为创建一个类的**实例**。*
####封装
从形式上看，封装就是将数据和行为组合在一个包里。
*对象中的数据被称作**实例字段**，操作数据的过程叫做**方法***

---
##1.1.2对象
- 对象的**行为(behavior)**-可以对这个对象做哪些操作，或者可以对这个对象应用哪些方法。
- 对象的**状态(state)**-调用那些方法时，对象会如何响应。
- 对象的**标识(identity)**-如何区分可能有相同行为和状态的不同对象。
  
---

##1.1.3识别类

- 商品
- 订单
- 发货地址
- 付款
- 账户

这些名词就可以得到类**Item,Order**等接下来找动词。商品要*添加*到订单中，订单可以*发货*，*付款*等。
也就是说**add**应该是Order类的一个方法，它接受一个item对象作参数。

---

##1.1.4类之间的关系

类之间最常见的关系有
- **依赖("uses-a");**
- **聚合("has-a");**
- **继承("is-a");**
  
**依赖**——如果一个类的方法要使用或操作另一个类的对象，我们就说前一个类依赖于后一个类。
应当经量减少互相依赖的类（如果A不知道B的存在，它就不关心B的任何变化——这意味着B的变化不会在A中引入**bug**）用术语来说就是减少类之间的**耦合**。

**聚合**——类A的对象包含类B的对象（例如一个Order对象包含一些Item对象）

**继承**——表示一个更特殊的类与一个更一般类之间的关系。如果类D扩展了类C，类D会继承类C的方法，另外还会有额外的功能。（下节会详细讨论）

---

##1.2.1对象与对象变量

想要使用对象，首先要构造对象，并指定其初始状态，然后对对象使用方法。
使用**构造器**(又叫构造函数)构造新实例，构造器总是与类同名。Java库中包含一个Data类，下面来看一个例子

要想构造一个新Data对象，需要在构造器前加上**new**操作符，如下：
`new Data()`
这个表达式会构造一个新对象。如果需要的话，可以将这个对象传给一个方法。
`System.out.println(new Data());`
或者可以对刚构造的对象使用方法：
`String s = new Data().toString;`
这两个例子中，构造的对象仅使用了一次。通常，你可以保留所构造的对象以供继续使用，为此，需要将对象放入一个变量中：
`Data rightNow = new Data();`
对象与对象变量之间存在一个重要区别
```
Data startTime;//此时对象变量startTime为空
//让startTime（对象变量）引用已有的对象
startTime = rightNow;
//让startTime（对象变更）构造一个新对象
startTime = new Data();
```
要认识到一点：**对象变量并不实际包含一个对象**，它只是*引用*一个对象.**任何对象变量的值都是一个引用，指向储存在另一个地方的某个对象**`new`操作符的返回值也只是一个引用

---

##1.2.2更改器与访问器方法

来看之前的plusDays方法调用：
`LocalDate aThousandDaysLater = newYearsEve.plusDays(1000);`
事实上，plusDays方法会生成一个新的LocalDate对象，然后把这个新对现象1赋值给aThousandDaysLater变量。**原来的变量不做任何改动，我们就说这个方法是一个*访问器*方法。**

与LocalDate.plusDays方法不同,GregorianCalender.add方法是一个**更改器方法（调用之后对象的状态会发生改变）**

---

##1.3.1Employee类

下面看一个简单的Employee 类，编写工资管理系统时可能会遇到:
```
public class Empolyee {
    //instance fields
    private String name;
    private double salary;
    private LocalDate hireDay;

    //constructor
    public Empolyee(String n,double s, int year,int month,int day){
        name=n;
        salary = s;
        hireDay = LocalDate.of(year,month,day);
    }

    //a  method
    public String getName(){
        return name;
    }
    //more methods
    ...
}
```
 这个类的方法被标记为public。关键字public意味着其可以被任何类的任何方法使用。三个实例字段被private标记，关键字private意味着只有Employee类本身的方法能够访问这些实例字段。

 ---

 ## 1.3.2用var声明局部变量

 在Java10中，如果可以从变量的初始值推导出他们的类型，就可以用**var**关键字声明局部变量，而无需指定类型。例如，可以不这样声明：
`Employee harry = new Employee("Harry Hacker",50000,1989,11,1);`
 只需要写： 
 `var harry = new Employee("Harry Hacker",50000,1989,11,1);`
 这一点可以必变重复写类型名Employee。
 *注意：关键字var只能用于方法中的局部变量。参数和字段类型必须声明。*
  
  ---

  ## 1.3.3隐式参数与显示参数

  方法会操作对象并访问他们的实例字段。
```
  public void raiseSalary(double byPercent){
        double raise = salary*byPercent/100 ;
        salary += raise ;
    }
```
将调用这个方法的对象的salary实例字段设置为一个新值。
`number001.raiseSalary(5);`
其作用是将number001.salary字段增加5%。
raiseSalary方法有两个参数。第一个参数称为**隐式参数**是出现在方法名前的Employee类型的对象。第二个参数是位于方法名后面括号的数值，这是一个**显示参数**。（可以说隐式参数称为方法调用的目标或接收者）

---

## 1.3.4 final实例字段

可以将实例字段定义为final。这样的字段必须在构造对象时初始化，并且以后不能再修改这个值。
```
class Employee{
    public final String name;
    ...
}
```
final修饰符对于类型为基本类型或者**不可变类型**的字段尤为适用。（如果类中所有方法都不会改变其对象，这样的类就为不可变类。例如，String类就是不可变的。）

---

## 1.4.1 静态字段
如果将一个字段定义为static，那么这个字段并不会出现在每个对象里。可以认为静态字段属于类，而不属于单个对象。
```
class Employee{
    private static int nextId =1;
    private int id;
    ...
}
```
假设我们构造了对象harry，harry的id设置为静态字段nextId的当前值，并将静态字段nextId的值加1:
```
harry.id = Employee.nextId;
Employee.nextId++;
```

---
##1.4.2静态常量

静态变量用的非常少，但是静态常量却非常常用。例如，Math类中定义了一个静态常量**PI**：
```
public class Math{
    ...
    public static final double PI = 3.141592653;
    ...
}
```
如果去除static关键字，那么每一个Math对象中都有一个自己的PI副本。(将static与final一起运用就会得到一个不可再变动的静态常量)

---

## 1.4.3 静态方法
静态方法是不操作对象的方法。例如Math类中的pow方法：
 `Math.pow(x,a);`会计算x的a次幂。它并不使用任何Math对象来完成这个任务，换句话说，它没有隐式参数。
 **静态方法不能访问实例字段，但是可以访问静态字段：**
 ```
 //以下是Employee中的一个静态方法
 public static int advanceId(){
    int r = nextId; // obtain next available id
    nextId++;
    return r;
 }
 ```
 要调用这个方法，需要提供类名：
 `int n = Employee.avvanceId();`
 这个方法也可以去除static，但这样一来就需要通过Employee对象引用来调用这个方法。

 **下面两种情况可以使用静态方法：**
- 方法不需要访问对象状态，因为它需要的所有参数都通过显示参数提供（例如Math.pow）。
- 方法只需要访问类的静态字段（例如Employee.advvanceId）。

---

##1.4.4工厂方法

静态方法还有另一种常见的用途。类似LocalDate和NumberFormat的类使用静态*工厂方法*来构造对象--LocalDate.now和LocalDate.of可得到不同样式的格式化对象：
```
NumberFormat currencyFormatter = NumberFormat.getCurrencyInstance();
NumberFormat percentFormatter = NumberFormat.getPercentInstance();
double x =0.1;
System.out.println(currencyFormatter.format(x)); //prints $0.10
System.out.println(percentFormatter.format(x)); //prints 10%
```
为什么LocalDate类不用构造器来创建对象呢？有两个原因：
- 无法为构造器命名。构造器名字总是与类名相同。但是，这里希望有两个不同的名字，分别得到货币实例和百分比实例。
- 使用构造器时，无法改变所构造对象的类型。而工厂方法实际上返回DecimalFormate类型的对象，这是继承NumberFormaate的一个子类。

---

## 1.5方法参数
**传参分为按值传递和按引用传递**
java中对象作为参数传递给一个方法，到底是值传递，还是引用传递？
pdd：所谓java只有**按值传递**：基本类型  值传递；引用类型，地址值传递，所以叫值传递。
当主函数的变量，对象（主本）传递到副函数时，传递的变量、对象（方法中的形参）其实都是**副本**，而副本的改变**不会影响主**本。
**基本类型**：传递的是值的拷贝，也就是说传递后就互不相关了，也就是说，不过副函数的副本如何变化，主本永远不会被影响。
**引用类型**：传递的是引用地址值，有可能会被影响。
**string**：具有不可变。是特殊的引用类型，其作用跟基本类型一样，传递后不相关。
String和int参数传递是按值传递还是引用传递？
一道面试题目，String的传递：
```
public String change(String s){

     s = "222";

     return s; 

}

public static void main(Stirng[] args){

    String s = "111"; 

    change(s);

    sout(s);

}
```

很多人认为对象引用为按引用传递，我们来看个例子：
```
 public static void main(String[] args) {
        var A = new Empolyee("A",1000,2001,12,12);
        /*System.out.println(aa.getName());
         */
        var B = new Empolyee("B",1000,2001,12,12);
        System.out.println("first is "+A.getName()+" second is "+B.getName());
        swap(A,B);
        System.out.println("first is "+A.getName()+" second is "+B.getName());
    }

    public static void swap(Empolyee a,Empolyee b){
        Empolyee temp = a;
        b = a;
        a = temp;
    }
```
swap方法并没有完成预期效果，。swap方法参数a与b初始化为两个对象的副本，这个方法是交换两个副本。
这说明Java程序设计里对**对象采用的不是按引用调用，而是按值传递。**
下面总结方法参数能做什么，不能做什么：
- 方法不能修改基本数据的参数（即数据型或布尔型）。
- 方法可以改变对象参数状态。
- 方法不能让一个对象参数引用一个新对象。

---

## 1.6.1重载
有些类有许多个构造器，如下可以创建一个空的StringBuilder对象：
`var messagea = new StringBulider();`
也可以创建一个有初始化的StringBuilder对象:
`var messagea = new StringBulider("To do none\n");`
这种功能就叫做**重载**，如果有多个同名的方法，它们的方法参数不同。那么编译器就要选择所对应的那个方法来调用。（编译器选择具体方法的过程称为*重载解析*）

>Java允许所有方法的重载，而不只是构造器方法。

---

## 1.6.2默认字段初始化与显示字段初始化
如果在构造器中没有显示的为一个字段设置初始值，就会将它自动设置为默认值：如数值将设置为0，布尔值设置为false，对象引用为null。

>这是字段与局部变量的一个重要区别。方法中的局部变量必须明确的初始化。但是在类中，如果没有初始化类中的字段，将会自动初始化为默认值。

不论调用哪个构造器，每个实例字段都要设置一个有意义的初始值，确保这一点总是一个好主意。
在执行构造器之前完成这个赋值。如果某个类的构造器都需要把某个特定的实例字段设置为同一个值，那么这个语法尤为有用。
**初始值不一定是常量**，例如：
```
class Employee{
    private static int nextId;
    private int id = advanceId();
    ...
    private static int advanceId(){
        int r = nextId;
        nextId++;
        return r;
    }
    ...
}
```

---

## 1.6.3调用另一个构造器
关键词this指示一个方法的隐式参数。不过，这个关键词还有另外一个含义。
如果构造器的第一个语句形如this(...)，这个构造器将调用同一个类的另一个构造器。下面就是一个简单的例子：
```
public Employee(double s){
    //calls Employee(String,double);
    this("Employee#" + nextId,s);
    nextId++;
}
```
**那这个语法有什么用呢？**
**我也不知道**

---

##1.6.4初始化块
前面已经介绍了两种初始化实例字段的方法：
- 在构造器中设置值；
- 在声明中赋值；
其实还有第三种机制，称为**初始化块**。在一个类的声明中，可以包含任意代码块。构造这个对象时，这些快就会执行。例如：
```
class Employee{
    private static int nextId;
    
    private int id;
    private String name;
    private double salary;

    //object initialization block
    {
        id = nxtId;
        nextId++;
    }
    
    public Employee(String n,double s){
        name = n;
        salary = s;
    }

    public Employee(){
        name = "";
        salary = 0;
    }
    ...
}
```
这个例子中，无论用哪个构造器构造对象，id字段都会在对象初始化块中初始化。首先运行初始化块，然后在运行构造器的主题部分。
*这种方法并不常见，一般把代码块放在构造器中。*
**下面是调用构造器时的具体处理步骤：**
1. 如果构造器的第一行调用了另一个构造器，则基于所提供的参数执行第二个构造器。
2. 否则
a)所有实例字段初始化为其默认值。
b)按照在类声明中出现的顺序，执行所有字段初始化方法和初始化代码块。
3. 执行构造器主题代码。

---

##1.6.5对象析构与finalize方法
某些对象使用了内存之外的其他资源，例如，文件或使用系统资源的另一个对象的句柄。在这种情况下，当资源不再需要时，将其回收再利用就十分重要。
如果一个资源需要用完就立即关闭，那么就需要用close方法来完成清理工作。如果可以等到虚拟机退出，那么就可以使用方法Runtime.addShutdownHook增加一个“关闭钩”。

---

## 1.7.1记录概念（不熟）
记录是一种特殊形式的类，其状态不可变而且公共可读。可以如下将Point定义为一个记录：
`record Point(double x, double y){}`
其结果是有以下实例字段的类：
```
private final double x;
private final double y;
```
在Java语言规范中，一个*记录*的实例字段被称为**组件**。
这个类有一个构造器：
`Point(double x,double y)`
和以下访问器方法：
```
public double x()
public double y()
```
>注意：访问器的方法为x和y，而不是getX和getY。（Java中实例字段可以与方法同名，这是合法的。）

```
var p = new Point(3,4);
System.out.println(p.x() + " " + p.y());
```
除了字段访问器方法，每个记录都有3个自动定义的方法：toString、equals和hashCode。
*可以为一个记录增加你自己的方法，也可以有静态字段和方法。不过，不能为记录增加**实例字段***。
>记录的实例字段自动为final字段。不过，它们可能是可变对象的引用。
`record PointInTime(double x,double y,Date when){}`
这样记录实例将是可变的：
`var pt = new PointInTime(0,0,new Date)`
`pt.when().setTime(0);`
如果希望记录实例是不可变的，那么字段就不能使用可变类型。

---

##1.7.2构造器：标准、自定义和简洁
自动定义的设置所有实例字段的构造器称为**标准构造器**。
还可以定义另外的**自定义构造器**。这中构造器的第一个语句必须调用另一个构造器，所以最终会调用标准构造器。
```
record Point(double x,double y){
    public Point() { this(0,0); }
}
```
这个记录有两个构造器：标准构造器和一个生成原点的无参构造器。
如果标准构造器需要完成额外工作，那么可以提供你自己的实现：
```
record Range(int form,int to){
    public Range(int form,int to){
        if(form <= to){
            this.form = form;
            this.to = to;
        }
        else {
            this.form = to;
            this.to = form;
        }
    }
}
```
不过，实现标准构造器时，建议使用一种**简洁**形式。不用指定参数列表：
```
record Range(int form,int to){
    public Range{
        if(form > to){
            int temp = form;
            form = to;
            to = temp;
        }
    }
}
```
简洁形式的主体是标准构造器的“前奏”。它只是为 **实例字段this.form和this.to赋值之前修改参数变量form和to**。不能在简洁构造器的主体中读取或修改实例字段。

---

##1.8.1包名
使用包的主要原因是确保类名的唯一性。可以使用一个因特网域名以逆序的形式作为包名，然后对于不同的项目使用不同的子包。
>从编译器的角度来看，嵌套的包之间没有任何关系。每一个包都是独立的类集合。

---

##1.8.2 类的导入
一个类可以使用所属包中的所有类，以及其他包中的**公共类**。
 我们可以用两种方式访问另一个包中的公共类。第一种是使用**完全限定名**，也就是包名后面跟着类名。
 `java.time.LocalDate today = java.time.LocalDate.now();`
 这显然恒繁琐。更简单且更常用的方式是使用import语句。import语句的关键是可提供一种简写方式来引用包中各个类。一旦增加了import语句，在使用类时，就不必写出类的全名了。
 `import java.time*;`
 然后，就可以使用：
 `LocalDate today = LocalDate.now();`
 而不需要在前面加上包前缀。还可以导入一个包中的特定类：
 `import java.time.LocalDate;`
 >但是要注意的是，（*）只能导入一个包，而不能使用import java.*或import.java.*.*

 ---

 ## 1.8.3静态导入
 有一种improt语句允许导入静态方法和静态字段，而不只是类。
 例如，如果在源文件的最开头加上：
 `import static java.lang.System.*;`
 就可以使用System类的静态方法和静态字段而不需要类名前缀：
 ```
 out.pritln("hello!");// i.e. , System.out
 exit(0); // i.e. , System.exit
 ```

 ---

 ## 1.8.4在包中增加类
 要想将类放入包中，就必须将包名放在源文件的开头，即放在定义这个包中各个类的代码前面。例如：
 ```
 package com.horstmann.corejava

 public class Employee{
    ...
 }
 ```
 如果没有在源文件中放置package语句，那么这个源文件中的类就属于**无名包**。无名包没有包名
 将源文件放到与完整包名匹配的子目录中。

 ---


 ## 1.9.1 创建JAR（Java归档）文件
 可以使用jar工具创建JAR文件。创建一个新的JAR文件最常用的命令使用以下语法：
 `jar cvf jarFileName file1 file2 ...`
 例如：
 `jar cvf CalculatorClasses.jar*.class icon.gif`
  通常，jar命令的格式如下：
  `jar options file1 file2 ...`
  jar程序的更多相关：[github链接](https://github.com/search?l=Java&q=JAR&type=Repositories);（或Java核心技术卷I十二版第147页）

---

## 1.9.2清单文件
除了类文件、图像和其他资源外，每个JAR还包含一个**清单文件**，用于描述归档文件的特殊特性。
清单文件被称为MANIFEST.MF，它位于JAR文件的一个特殊的META-INF子目录中。合法的最小清单文件及其简单：`Manifest-Version: 1.0`
复杂的清单文件可能包含很多个条目。这些清单条目被分为很多个小节。第一节被称为**主节**。它作用于整个JAR文件。随后的条目可以指定命名实体的属性。它们都必须以“”Name条目开始。节与节之间用空行分开。例如：
```
Mainfest-Version: 1.0
//lines describing

Name: Woozle.class
lines describing this file
Name: com/company/mypkg/
lines describing this package
```
要想编辑清单文件，需要将希望添加到清单文件的行放到文本文件中，然后运行：
`jar cfm jarFileName manifestFileName`
要想更新一个已有的JAR文件，需要将希望增加的部分放置到一个文本文件中，然后执行以下命令：
`jar umf MyArchive.jar manifest-additions.mf`
[更多JAR文件和清单文件格式](https://docs.oracle.com/javase/10/docs/specs/jar/jar.html);

---

## 1.9.3可执行JAR文件
 可以使用jar命令中的e选项指定程序的*入口点*。即通常调用java执行程序时指定的类：
 `jar cvfe MyProgram.jar com.company.mypkg.MainAppClass files to add`
 或者，可以在清单文件中指定程序的主类，包括以下形式语句：
 `Main-Class: co.company.mypkg.MainAppClass`
 不要为主类名加扩展名 “.class”。
 >警告：清单文件的最后一行必须以换行符结束。

 不论哪种方法，用户都可以通过以下的命令来启动程序：
 `java -jar MyProgram.jar`

 ---

 ## 1.10类设计技巧
1. 一定要保证数据私有。
2. 一定要初始化数据。
3. 不要在类中使用过多的基本类型。
4. 不是所有的字段都需要单独的字段访问器和更改器。
5. 分解有过多职责的类。
6. 类名和方法名要体现它们的职责。
7. 优先使用不可变类。