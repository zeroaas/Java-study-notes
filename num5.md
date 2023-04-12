#泛型程序设计

---

## 5.1 类型参数的好处
在Java中增加泛型类之前，泛型程序设计使用**继承**实现的。ArrayList类只维护一个Object引用的数组：
```
public class ArrayList{
    private Object[] elementData;
    public Object get(int i){...}
    public void add(Object o){...}
}
```
这种方法存在两个问题。获取一个值时必须进行强制类型转换：
```
ArrayList files = new ArrayList();
...
String filename = (String) files.get(0);
```
如果在一些地方，将get的结果强转类型为String类型，就会产生一个错误。
泛型提供了一个更好的解决方案：**类型参数**。ArrayList类现在有一个类型参数用来指示元素的类型：
`var files = new ArrayList<String>();`
这使得代码具有更好的可读性。人们一看就知道这个数组列表中包含的是String对象。
>如果用一个明确的类型而不是var声明一个变量，则可以通过使用“菱形”语法省略构造器中的类型参数：`ArrayList<String> files = new ArrayList<>()`

---

## 5.2 定义简单泛型类
**泛型类**就是有一个或多个类型变量的类。本章使用一个简单的Pair类作为例子。下面是泛型Pair类的代码：
```
public class Pair<T> {
    private T first;
    private T second;

    public Pair() {
        first = null;
        second = null;
    }
    public Pair(T first,T second){
        this.first = first;
        this.second = second;
    }

    public T getFirst() {
        return first;
    }

    public T getSecond() {
        return second;
    }

    public void setFirst(T first) {
        this.first = first;
    }

    public void setSecond(T second) {
        this.second = second;
    }
}

```
Pair类引入了一个类型变量T，用尖括号(<>)括起来，放在类名后面。泛型类可以有多个类型变量。例如，可以定义Pair类，其中第一个字段和第二个字段使用不同的类型：
`public class Pair<T, U>{...}`
可以用具体的类型替换类型变量来**实例化**泛型类型，例如：
`Pair<String>`
可以把结果想象成一个普通类，它有以下构造器：
`Pair<String>()`
`Pair<String>(String,String)`
换句话说，泛型类相当于普通类的工厂。

---

## 5.3 泛型方法
上一节已经介绍了如何定义一个泛型类。还可以定义一个带有类型参数的方法。
```
class ArrayAlg{
    public static <T> T getMiddle(T...a){
        return a[a.length / 2];
    }
}
```
这个方法是在普通类中定义的，而不是在泛型类中。**可以在普通类中定义泛型方法，也可以在泛型类中定义。**
当调用一个泛型方法时，可以把具体类型包围在尖括号中，放在方法名前面：
`String middle = ArrayAlg.<String>getMiddle("John","Q","Public");`
在这种情况下，方法调用中可以省略<String>类型参数。也就是说，可以简单的调用：
`String middle = ArrayAlg.getMiddle("John","Q","Public");`

---

## 5.4 类型变量的限定
有时，类或方法需要对类型变量加以约束。下面是一个经典的例子。我们要计算数组中最小的元素：
```
class ArrayAlg{
    public static <T> T min(T[] a) // almmost correct
    {
        if(a == null || a.length == 0) return null;
        T smallest = a[0];
        for (int i = 1;i<a.length; i++){
            if (smallest.compareTo(a[i])>0) smallest = a[i];
        }
        return smallest;
    }
}
```
这里有一个问题，变量smallest的类型为T，这意味着它可以是任何一个对象。如何知道T所属的类有一个compareTo方法呢？
解决这个问题只能限制T是实现了Comparable接口的类。
可以通过设置一个**限定**来实现这一点：
`public static <T extends Comparable> T min(T a)...`
实际上，Comparable接口本来就是一个泛型类型。
一个类型变量或通配符可以有多个限定，例如：
`T extends Comparable & Serializable`
限定类型用“&”符分隔，而逗号用来分隔类型变量。
>按照Java继承机制，可以根据需要拥有多个接口超类型，但最多有一个限定可以是类。如果有一个类作为限定，它必须是限定列表中的第一个限定。

---

## 5.5 泛型代码和虚拟机

### 5.5.1 类型擦除
无论何时定义一个泛型类型，都会自动提供一个相应的**原始类型**。这个原始类型的名字就是去掉类型参数后的泛型类型名。类型变量会被**擦除**，并替换为其限定类型（或者，对于无限定的变量则替换为Object）。
例如，Pair<T>的原始类型如下所示：
```
public class Pair<T> {
    private Object first;
    private Object second;

    public Pair() {
        first = null;
        second = null;
    }
    public Pair(Object first,Object second){
        this.first = first;
        this.second = second;
    }

    public Object getFirst() {
        return first;
    }

    public Object getSecond() {
        return second;
    }

    public void setFirst(Object first) {
        this.first = first;
    }

    public void setSecond(Object second) {
        this.second = second;
    }
}
```
因为T是一个无限类的类型变量，所以直接替换为Object。
其结果是一个普通类，就好像Java语言中引入泛型之前实现的类一样。
原始类型用第一个限定来替换类型变量，或者，如果没有给限定，就替换为Object。

---

### 5.5.2 转换泛型表达式
编写一个泛型方法调用时，如果擦除了返回类型，编译器会插入强制类型转换。例如，对于下面这个语句序列：
```
Pair<Employee> buddies = ...;
Employee buddy = buddies.getFirst();
```
gitFirst擦除类型后的返回值类型是Object。编译器自动插入转换到Employee的强制类型转换。也就是说，编译器把这个方法调用转换为两条虚拟机指令：
- 调用原始方法Pair.getFirst。
- 将返回的Object类型强制转换为Employee类型。
>访问一个泛型字段时也会插入强制类型转换。

---

### 5.5.3 转换泛型方法
类型擦除也会出现在泛型方法中。程序员通常认为类似下面的泛型方法
`public static <T extends Comparable> T min(T[] a)`
是整个一组方法，而擦除类型后，只剩一个方法：
`public static Comparable min(Comparable[] a)`
注意，类型参数T已经被擦除了，只留下了它的限定类型Comparable。

总之，对于Java泛型的转换，需要记住以下几点：
- 虚拟机中没有泛型，只有普通的类和方法。
- 所有的类型参数都会替换为它们的限定类型。
- 会合成桥方法来保持多态。
- 为保持类型安全性，必要时会插入强制类型转换。

---

## 5.6限制与局限大多数限制都是由类型擦除引起的

### 5.6.1 不能用基本类型实例化类型参数
不能用基本类型代替类型参数。因此，没有“Pair<<double>double>，只有Pair<<Double>Double>。当然，其原因就在于类型擦除。擦除之后Pair类含有Object类型的字段，而Object不能存储double值。
这确实令人烦恼。但是，这样做与Java语言中基本类型的独立状态相一致。这并不是一个致命的缺陷——只有8种基本类型，而且即使不能接受包装器类型，也可以使用单独的类和方法来处理。

---

### 5.6.2 运行时类型查询只适用于原始类型
虚拟机中的对象总是有一个特定的非泛型类型。因此，所有的类型查询只能生成原始类型。例如：
`if(a instanceof Pair<String>) //ERROR`
实际上仅仅测试a是否是任意类型的一个Pair。下面的测试同样如此：
`if(a instanceof Pair<T>) //ERROR`
或以下强制类型转换也是如此：
`Pair<String> p =(Pair<String>) a //waring--can only test that a is a Pair`
为了提醒这一风险，如果试图查询一个对象是否属于某个泛型类型，你会得到一个编译器错误（使用instanceof时），或者得到一个警告（使用强制类型转换时）。

---

### 5.6.3 不能创建参数化类型的数组
不能实例化参数化类型的数组，例如：
`var table = new Pair<String>[10] //ERROR`
数组会记住它的元素类型，如果试图存储类型不正确的元素，就会抛出一个ArrayStore-Exception异常：
`objarray[0] = "Hello"`
不过对于泛型类型，擦除会使这种机制无效。以下赋值
`objarray[0] = new Pair<Employee>()`
尽管能够通过数组储存的检查，但仍会导致一个类型错误。出于这个原因，不允许创建参数化类型的数组。

---

### 5.6.4 Varargs警告
这一节我们来讨论一个问题，向参数个数可变的方法传递一个泛型类型的实例.
考虑下面这个简单的方法，它的参数个数是可变的：
```
public static <T> void addAll(Collection<T> coll, T...ts){
    for(T t : ts) coll.add(t);
}
```
实际上参数ts是一个数组，包含提供的所有实参。
现在考虑以下调用：
```
Collection<Pair<String>> table = ...;
Pair<String> pair1 =...;
Pair<String> pair2 =...;
addAll(table,pair1,pair2);
```
为了调用这个方法，Java虚拟机必须建立一个Pair<String<String>>数组，这就违反了规则。不过，对于这种情况，规则有所放松，你只会得到一个警告，而不是错误。

可以采用两种方法来抑制这个警告。
- 一种方法是为包含addAll调用的方法增加注解@SuppressWarnings("unchecked")。
- 或者Java7中，还可以用@SafeVarargs直接注解addAll方法。
现在就可以提供泛型类型来调用这个方法了。对于任何只需要读取参数数组元素的方法，都可以使用这个注解。

---

### 5.6.5 不能实例化类型变量
不能在类似new T(...)的表达式中使用类型变量。例如，下面的Pair<T<T>构造器就是非法的：
`public Pair() { first = new T(); second = new T(); } //ERROR`
类型擦除将T变成了Object，而你肯定不希望调用new Object().
在Java8之后，最好的解决办法是让调用者提供一个构造器表达式。例如：
`Pair<String> p = Pair.makePair(String::new);`
makePair方法接受一个Supplier<T<T>>，这是一个函数式接口，表示一个无参数而且返回类型为T的函数：
```
public static <T> Pair<T> makePair(Supplier<T> constr){
    return new Pair<>(constr.get(), constr.get());
}
```
比较传统的解决方法是通过反射调用Constructor.newInstance方法来构造泛型对象。
遗憾的是，细节有点复杂，不能如下调用：
`first = T.class.getConstructor().newInstance(); //ERROR`
表达式T.class是不合法的，因为它会擦除为Object.class。必须适当地设计API以便得到一个Class对象，如下所示：
```
public static <T> Pair<T> makePair(Class<T> cl){
    try{
        return new Pair<>(cl.getConstructor().newInstance(),cl.getConstructor().newInstance());
    }
    catch(Exception){ return null; }
}
```
这个方法可以如下调用：
`Pair<String> p = Pair.makePair(String.class);`
注意，Class本身是泛型的。

---

## 5.7 泛型类型的继承规则
**一般来讲，不管S和T有什么关系，Pair< S >和Pair< T >都没有任何关系。**
总是可以将参数化类型转换为一个原始类型。在与遗留代码交互时，这个转换非常必要。
最后，泛型类可以扩展或实现其他的泛型类。就这一点而言，它们与普通的类没有什么区别。例如，ArrayList< T >类实现了List< T >接口。这意味着，一个ArrayList< Manager >可以转换为一个List< Manager >。但是，如前面所见，ArrayList< Manager >不是一个ArrayList< Employee >或List< Employee >。

---

## 5.8 通配符类型

### 5.8.1 通配符概念
在通配符类型中，允许类型参数变化。例如，通配符类型
`Pair<? extends Employee>`
表示任何泛型Pair类型，它的类型参数是Employee的子类，如Pair< Manager >，但不能是Pair< String >。
假设要写一个打印员工对的方法，如下所示：
```
public static void printBuddies(Pair<Employee> p){
    Employee first = getFirst();
    Employee second = getSencond();
    System.out.println(first.getName()+"and"+second.getName());
}
```
不能将Pair< Employee >传递给这个方法。不过解决的方法很简单，可以使用一个通配符类型：
`public static void printBuddies(Pair<? extend Employee> p)`