# 接口、lambda表达式与内部类

---

## 3.1.1 接口的概念
接口不是类，而是对希望符合这个接口的类的一组需求。下面是Comparable接口的代码：
```
public interface Comparable{
    int compareTo(Object other);
}
```
在这个接口中，compareTo方法是**抽象**的，每个实现这个接口的类都需要包含一个compareTo方法，接收一个Object参数，否则，这个类也是抽象的。
>接口中的所有方法都自动public，因此，*在接口中声明方法时，不需要提供关键字public*。
接口可以有多个方法，还可以定义常量，但是绝不会有实例字段。
为了让类实现一个接口，需要完成下面两个步骤：
- 将类声明为实现给定的接口。
- 对接口中的所有方法提供定义。
要声明一个类实现某个接口，需要使用关键字implements；
`class Employee implements Comparable`
使用接口的主要原因在于：Java是一种**强类型**语言。调用方法时，编译器要能检查这个方法确实存在。

---

## 3.1.2 接口的属性
接口不是类，不能用new操作符实例化一个接口。不过，仍然可以声明接口变量：
`Comparable x;`
接口变量必须引用实现了这个接口的一个类对象:
`x = new Employee(...);`
接下来，如同使用 instanceof 检查一个对象是否属于某个特定类一样，也可以使用 instanceof 检查一个对象是否实现了某个特定接口：
`if(anObject instanceof Comparable){...}`
>与建立类的继承结构层次一样，也可以扩展接口，同样运用“extends”。

尽管每个类只能有一个超类，但可以实现多个接口。例如：
`class Employee implements Cloneable , Comparable{...}`\
>记录和枚举类不能扩展其他类（因为它们隐式地扩展了Record和Enum类）。不过，它们可以实现接口。

>接口可以是密封的。与密封类一样，直接子类型（可以是类或者是接口）必须在permits子句中声明，或者放在同一个源文件中。

---

## 3.1.3 静态和私有方法
通常的做法都是将静态方法放在伴随类中。在标准库中，你会看到成对出现的接口和实用工具类，如Collection/Collections或Path/Paths.
可以由一个URI或者字符串序列构造一个文件或目录的路径。在Java11中，Path接口提供了等价的方法：
```
public interface Path{
    public static Path of(URI uri){...}
    public static Path of(String first, String... more){...}
    ...
}
```
这样以来，Paths类就不再是必要的了。

---

## 3.1.4 默认方法
可以为任何接口方法提供一个**默认**实现。必须用default修饰符标记这样一个方法。
```
public interface Comparable<T>{
    default int compareTo(T other){return 0;}
}
```
但是，默认方法只有在一些情况下有用。
>默认方法的一个重要用法是**接口演化**。

---

## 3.1.5 默认方法冲突
如果先在一个接口中将一个方法定义为默认方法，然后又在超类或另一个接口中定义了同样的方法，那么就要遵循Java的规则：
1. 超类优先。
2. 接口冲突。如果一个接口提供了一个默认方法，另一个接口提供了一个同名而且参数类型相同的方法（不论是否是默认方法），必须覆盖这个方法来解决冲突。
```
interface Person{
    default String getName(){return "";}
}

interface Named{
    default String getName(){ return getClass()>getName() +"_" + hashCode();}
}
```
这是就需要选择其中一个方法：
```
class Student implements Person , Name{
    public String getName(){
        return Person.super.getName;
    }
}
```

---

## 3.1.6 Comparator接口
现在假设我们希望按长度递增的顺序对字符串进行排序，而不是按字典顺序进行排序。要处理这种情况，Arrays.sort 方法还有第二个版本，接受一个数组和一个**比较器**，作为参数，比较器是实现了Comparator接口的类的实例。
```
public interface Comparator<T>{
    int compare(T first, T second);
}
```
要按长度比较字符串，可以如下定义一个实现Comparator<String>的类：
```
class LengthComparator implements Comparator<String>{
    public int compare(String first,String second){
        return first.length() - second.length();
    }
}
```
具体完成比较，需要建立一个实例：
```
var comp = new LengthComparator();
if(comp.compare(words[i],words[j])>0)...
```

---
 
## 3.2.1 lambda表达式的语法
```
(String first, String second) ->
    first.length() - second.length()
```
Java中一种简单的lamabda表达式形式：参数，箭头（->）以及一个表达式，如果代码要完成的计算无法放在一个表达式中，就可以像写方法一样，把这些代码放在{}中，并包含显示return语句。例如：
```
(String first, String second) ->{
    if(first.length() < second.length()) return -1;
    else if(first.length() > second.length()) return 1;
    else return 0;
}
```
即使lamabda表达式没有参数，任要提供空括号，就像无参数方法一样：
`() -> {for(int i = 100; i >=0; i--) System.out.println(i);}`
如果可以推导出一个lamabda表达式的参数类型，则可以忽略其类型。例如：
```
Comparator<String> comp = 
    (first,second) //same as (String first, String second) ->
        first.length() - second.length();
```
这里编译器可以推导出first和second必然是字符串，因为这个lamabda表达式将赋给一个字符串比较器。
如果方法只有一个参数，而且这个参数的类型可以推导出，那么甚至还可以省略小括号：
```
ActionListener listener = event ->
    System.out.println("The time is" + Instant.ofEpochMilli(event.getWhen()));
```

---

## 3.2.2 函数式接口
对于只有一个抽象的方法的接口，需要这种接口的对象时，就可以提供一个lamabda表达式，这种接口称为**函数式接口**。
下面考虑Arrays.sort方法。它的第二个参数需要一个Comparator实例，Comparator就是只有一个方法的接口，所以可以提供一个lamabda表达式：
```
Arrays.sout(words,
    (first,second) -> first.length() - second.length());
```
lamabda表达式可以转换为接口。下面来看一个例子：
```
var timer = new Timer(1000, event ->{
            System.out.println("At the tone, the time is"
                + Instant.ofEpochMilli(event.getWhen()));
            Toolkit.getDefaultToolkit().beep();
        });
```

---

## 3.2.3 方法引用
`var timer = new Timer(1000,System.out::println);`
表达式 System.out::println 是一个**方法引用**，它指示编译器生成一个函数式接口的实例，覆盖这个接口的抽象方法来调用给定的方法。

方法引用需要用::操作符分割方法名与对象或类名。主要由3种情况:
1. object::instanceMethod
2. Class::instanceMethod
3. Class::staticMethod

可以在方法引用中使用this参数。例如：`this::equals`等同于`x ->this.equals(x)`。
使用super也是合法的：`super::instanceMethod`使用this作为目标，会调用给定方法的超类版本。

---

## 3.2.4 构造器引用
构造器引用与方法引用很类似，只不过方法名为new。例如：
```
ArrayList<String> names =...;
Stream<Person> stream = names.stream().map(Person::new);
List<Person> people = stream.toList();
```

---

## 3.2.5 变量作用域
通常，你可能希望在lamabda表达式中访问外围方法或类中变量。
```
public static void repeatMessage(String text,int delay)
{
    ActionListener listener = event ->
        {
            System.out.println(text);
            Toolkit.getDefaultTookit(). beep();
        };
    new Timer(delay,listener).star();
}
```
我们来巩固一下对lambda表达式的理解：
1. 一个代码块；
2. 参数；
3. 自由变量的值，这是指非参数而且不在代码中定义的变量
   
lambda表达式中，只能引用值不会改变的量，这个限制是有原因的/如果在lambda表达式中改变量，并发执行多个动作时就会不安全。
>这里有一条规则：lambda表达式中捕获的变量必须是事实最终变量。事实最终变量是指，这个变量初始化之后就不会再为它赋新值。

---

## 3.2.6 处理lambda表达式
使用lambda表达式的重点是**延迟执行**。之所以希望以后再执行代码，这有很多原因：
- 在一个单独的线程中运行代码；
- 多次运行代码；
- 在算法的适当位置运行代码（如，排序中的比较操作）；
- 发生某种情况时运行代码；
- 只在必要时运行代码；

[常用函数式接口](https://www.runoob.com/java/java8-functional-interfaces.html)

---

## 3.3.1 使用内部类访问对象状态
我们重构TimerTest 
```
public class TalkingClock{
    private int interval;
    private boolean beep;
    public TalkingClock(int interval, boolean beep){...}
    public void star(){...}

    public class TimePrinter implements ActionListener{
        ...
    }
}
```
需要注意，这里的 TimePrinter 类位于 TalkingClock 类内部。**这并不意味着每个 TalkingClock 都有一个 TimePrinter 实例字段**。TimePrinter 是由 TalkingClock 方法构造的。
下面是 TimePrinter 类的详细内容:
```
public class TimePrinter implements ActionListener{
    public void actionPerformed(ActionEvent event){
        System.out.println("At the tone, the time is "+ Instant.ofEpochMilli(event.getWhen()));
        if(beep) Toolkit.getDefaultTookit().beep();
    }
}
```
可以看到，一个内部类方法可以访问自身的实例字段，也可以访问创建它的外部类对象的实例字段。
>通常，内部类的对象总有一个隐式引用，指向创建它的外部类对象。

这个引用在内部类的定义中是不可见的。不过，我们将外部类对象的引用称为outer。于是actionPerformed方法将等价于以下代码：
```
public void actionPerformed(ActionEvent event){
    System.out.println("At the tone, the time is "+ Instant.ofEpochMilli(event.getWhen()));
    if(outer.beep) Toolkit.getDefaultTookit().beep();

}
```
>外部类的引用在构造器中设置。

---

## 3.3.2 内部类的特殊语法规则
表达式`OuterClass.this`表示外部类引用。例如，可以像下面这样编写 TimePrinter 内部类的 actionPerformed 方法：
```
public void actionPerformed(ActionEvent event){
    ...
    if(TalkingClock.this.beep) Toolkit.getDefaultTookit().beep();

}
```
反过来，可以采用以下语法更加明确地编写内部类对象的构造器：
`outerObject.new InnerClass(construction parameters)`
例如：
`ActionListener = this.new TimePrinter();`
在外部类的作用域之外，可以这样引用内部类：
`OuterClass.InnerClass`

---

## 3.3.3 局部内部类
可以在一个方法中局部地定义这个类：
```
public void start(){
    class TimePrinter implements ActionListener{
        public void actionPerformed(ActionEvent event){
            System.out.println("At the tone, the time is "+ Instant.ofEpochMilli(event.getWhen()));
            if(outer.beep) Toolkit.getDefaultTookit().beep();
        }
    }
    var listener = new TimePrinter();
    var timer = new Timer(interval, listener);
    timer.start();
}
```
声明局部类时不能有访问说明符（即public或private）。局部类的作用域总是限定在声明这个局部类的块中。
>局部类有一个很大的优势，即对外部世界完全隐藏。

---

## 3.3.4 由外部方法访问变量
与其他内部类相比，局部类还有另外一个优点。它们不仅能够访问外部类的字段，还可以访问局部变量。不过，那些局部变量必须是事实最终变量。
这里，将TalkingClock构造器的参数interval和beep移至star方法：
```
public void start(int interval,boolean beep){
    class TimePrinter implements ActionListener{
        public void actionPerformed(ActionEvent event){
            System.out.println("At the tone, the time is "+ Instant.ofEpochMilli(event.getWhen()));
            if(beep) Toolkit.getDefaultTookit().beep();
        }
    }
    var listener = new TimePrinter();
    var timer = new Timer(interval, listener);
    timer.start();
}
```

---

## 3.3.5 匿名内部类
使用局部类内部类时，通常还可以再进一步。假如只想创建这个类的一个对象，甚至不需要为类指定名字。这样一个类被称为**匿名内部类**。
```
public void start(int interval,boolean beep){
    var listener = new ActionListener{
        public void actionPerformed(ActionEvent event){
            System.out.println("At the tone, the time is "+ Instant.ofEpochMilli(event.getWhen()));
            if(beep) Toolkit.getDefaultTookit().beep();
        }
    }
    var listener = new TimePrinter();
    var timer = new Timer(interval, listener);
    timer.start();
}
```
这个语法的含义是：创建一个类的新对象，这个类实现了 ActionListener 接口，需要实现的方法 actionPerformed 是大括号{}中定义的方法。
一般的，语法如下：
```
new SuperType(construction parameters){
    inner class methods and data
}
```
在这里，SuperType 可以是接口，如ActionListener，如果是这样，内部类就要实现这个接口。 SuperType 也可以是一个类，如果是这样，捏不累就要扩展这个类。
由于构造器的名字要与类名相同，而匿名内部类没有类名，所以，匿名内部类不能有构造器。实际上，构造参数要传递给**超类构造器**。具体的，只要内部类实现一个接口，就不能有任何构造参数。不过，仍然要提供一组小括号：
```
new InterfaceType(){
    methods and data
}
```
如果构造参数列表结束后的小括号后面跟一个开始大括号，就是在定义匿名内部类：
`var queen = new Person("Mary");`和
`var count = new Person("Darcula"){...}`
文明习惯的做法是用匿名内部类实现事件监听器和其他回调。如今最好还是使用lambda表达式。

---

## 3.3.6 静态内部类
有时候，使用内部类只是为了把一个类隐藏在另一个类的内部，并不需要内部类有外部类对象的一个引用。为此，可以将内部类声明为static，这样就不会生成那个引用。
下面是一个静态内部类的典型例子。考虑这样一个任务：计算数组中的最小值和最大值。
```
double min = Double.POSITIVE_INFINITY;
double max = Double.NEGATIVE_INFINITY;
for(double v : values){
    if(min > v) min = v;
    if(max < v) max = v;
}
```
不过，这个方法必须返回两个数，为此，可以定义一个包含两个值的类Pair:
```
class Pair{
    private double first;
    private double second;
    public Pair(double f, double s){
        first = f;
        second = s;
    }
    public double getFirst(){ return first; }
    public double getSecond(){ return second; }
}
```
minmax方法可以返回一个Pair类型的对象。
```
class ArrayAlg{
    public static Pair minmax(double[] values){
        ...
        return new Pair(min,max)
    }
}
```