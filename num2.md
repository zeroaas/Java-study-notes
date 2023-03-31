#继承

---

## 2.1.1 定义子类
可以用如下代码继承Employee类来定义Manager类，这里使用关键字extends表示继承：
```
public class Manager extend Employee{
    //add methods and fields
}
```
关键字extends指示正在构造的新类派生与一个已存在的类。这个已存在的类叫作**超类、基类或父类**；新类称为**子类或派生类**。
在Manager类中，增加了一个用于储存奖金信息的字段，及设置这个字段的新方法：
```
public class Manager extends Empolyee{
    private double bonus;
    ...

    public void setBonus(double bonus) {
        this.bonus = bonus;
    }
}
```
这没有什么特别的，如果有一个Manager对象就可以用setBonus方法。当然，Employee对象不能用，因为Employee类中没有这个方法。
每个Manager对象有四个字段：name,salary,hireD  ay,bonus。其中，name,salary,hireDay是从超类得来的。
>通过扩展超类定义子类的时候，只需要指出子类与超类的不同之处。因此，在设计类的时候，应该将最一般的方法放在超类中，而将更特殊的方法放在子类中，这种做法十分普遍。

---

## 2.1.2 覆盖方法
超类中的方法对子类不一定适用。为此，需要提供一个新方法来覆盖超类中的这个方法：
```
public class Manager extends Employee{
    ...
    public double getSalary(){
        ...
    }
    ...
}
```
应该如何实现这个方法。乍看起来似乎很简单，只要返回salary和bonus字段的总和就可以了。不过，这样是不行的。只有Employee方法能直接访问Employee类的私有字段。这意味着Manager类的getSalary方法不能直接访问salary字段。如果想要访问，那就必须要像所有其他方法一样使用公共接口，在这里就是要使用Employee类中的公共方法getSalary。**并且我们不希望调用Manager类自己的getSalary方法，所以要用特殊的关键字super解决：**
`super.getSalary()`
```
public double getSalary(){
    double baseSalary = super.getSalary();
    return baseSalary + bonus;
}
```
正如前面所看到的那样，子类可以增加字段、增加方法或覆盖超类的方法，不过，继承绝不会删除任何字段或方法。

---

## 2.1.3 子类构造器
我们来提供一个构造器：
```
public Manager(String name,double salary,int year,int month,int day){
    super(name,salary,year,month,day);
    bonus = 0;
}

```
这里的super有不同的含义。语句：
`super(name,salary,year,month,day);`
是“调用超类Employee中带有n、s、year、month和day参数的构造器”的简写形式。
由于Manager类的构造器不能访问Employee类的私有字段，所以必须通过一个构造器来初始化这些私有字段。可以利用特殊的super语法调用这个构造器。使用super调用构造器的语句必须是子类构造器的第一条语句。
如果构造子类对象时没有显示地调用超类的构造器，那么超类必须有一个无参数构造器。这个构造器要在子类之前调用。
一个对象变量可以指示多种实际类型，这一点称为**多态**。在运行时能够自动地选择适当的方法，这称为**动态绑定**

---

##2.1.4 继承层次结构
继承不仅限与一个层次。例如，可以由Manager类派生一个Executive类。*由一个公共超类派生出来的所有类的集合称为继承层次结构*，在继承层次结构中，从某个特定的类到其祖先的路径称为该类的**继承链**。
通常一个超类可以有很多个子类。

---

## 2.1.5 多态
“is-a”规则指出子类的所有对象也是超类的对象。
“is-a”规则的另一种表述是**替换原则**。它指出了程序中需要超类对象的任何地方都可以用子类对象来替换。
对象变量是**多态的**。一个Employee类型的变量既可以引用一个Employee类型的对象，也可以引用任何一个Employee类的子类的对象。
不过，不能将超类的引用赋值予子类变量。原因很简单：不是所有员工都是经理。如果赋值成功那么其运用了Employee的方法，有可能会发生错误。
>子类引用数组可以转换为超类引用数组而不需要强制类型转换：
`Manager[] managers = new Manager[10];`
将它转换为Employee[]是完全合法的：
`Employee[] employees = managers;`

---

## 2.1.6 理解方法调用
下面假设要调用x.f(args)，隐式参数x声明为类C的一个对象。下面是调用的过程：
1. **编译器查看对象的声明类型和方法名。需要注意的是：有可能存在多个名字为f但参数类型不一样的方法。**
  至此，编译器已知所有可能要调用的候选方法。
2. **接下来，编译器要确定方法调用中提供的参数类型。如果在所有名为f的方法中存在一个与所提供参数类型完全匹配的方法，就选择它。这个过程叫*重载解析***。
   至此，编译器已经知道需要调用的方法和参数类型。
>前面说过，方法的名字和参数列表称为方法的签名
返回类型不是签名的一部分。不过在覆盖一个方法时，需要保证返回类型的兼容性。例如Employee有以下方法：
`public Employee getBuddy(){...}`
经理不会想找底层员工作为搭档。为了反应这一点，在子类Manager中，可以如下代码覆盖这个方法：
`public Manager getBuddy(){...}`// OK to change return type
我们说这两个方法有**协变**的返回类型。
3. 如果是prrivate方法、static方法、final方法或者构造器，那么编译器可以准确的知道应该调用哪个方法。这称为**静态绑定**。 于此对应的是，如果要调用的方法依赖于隐式参数的实际类型，那么必须在运行时使用**动态绑定**。
4. 程序运行并采用动态绑定调用方法时，虚拟机必须调用与x所引用对象的实际类型对应的那个方法。

每次调用方法都要完成这个搜索，时间非常长。因此，虚拟机预先为每个类计算了一个**方法表**，其中列出了所有方法的签名和要调用的实际方法。
在运行时，调用e.getSalary()的解析过程为：
1. 首先，虚拟机获取e的实际类型的方法表。
2. 接下来，虚拟机查找定义了getSalary()签名的类。
3. 最后，虚拟机调用这个方法。

动态绑定有一个非常重要的特性：无需修改现有代码就可以对程序进行**扩展**。
>在覆盖一个方法时，子方法**不能低于**超类方法的**可见性**。

---

## 2.1.7 阻止继承：final类和方法
有时候，我们可能希望组织人们定义某个类的子类。不允许扩展的类被称为final类。如果在类中使用了final修饰符，就表明这个类是final类。例如：
```
public final class Executive extends Manager{
    ...
}
```
也可以将类中的某个特定方法声明为final。如果这样做，那么所有子类都不能覆盖这个方法（final类中的所有方法自动地成为final方法）。例如：
```
public class Employee{
    ...
    public final String getName(){
        return name;
    }
    ...
}
```
>起那么曾经说过，字段也可以声明为final。对于final字段来说，构造对象之后就不允许改变了。不过，如果将一个类声明为final，只有其中的方法自动的成为final，而不包括字段。

将方法或类声明为final只有一个原因：确保它们不会在子类中改变语句。
如果一个方法没有被覆盖并且很短，编译器就能够对它进行优化处理，则这个过程称为**内联**。例如，内联调用e.getName()将把它替换为访问字段e.name。
>枚举和记录总是final，它们不允许扩展。

---

## 2.1.8 instanceof模式匹配    （?）
下面这串代码：
```
if(staff[i] instanceof Manager){
    Manager boss =(Manager) staff[i];
    boss.setBonus(5000);
}
```
实在有些冗长。我们真的需要反复提到3次Manager吗？
还有种更简便的方法。可以直接在instanceof测试中声明子类变量：
```
if(staff[i] instanceof Manager boss){
    boss.setBonus(5000);
}
```
如果staff[i]是Manager类的一个实例，则变量boss设置为staff[i]，可以将其作为一个Manager。这样可以跳过强制类型转换。
如果staff[i]并非引用一个Manager，那么不会设置boss，instanceof操作符会生成false值，这样就会跳过if语句。
>使用instanceof的大多数情况下，都需要使用一个子类方法。这样就可以使用instanceof的这种“模式匹配”，而不是类型强制转换。

---

## 2.1.9 受保护访问
有些时候，会希望超类中的某个方法只让子类访问，或着让子类的方法可以访问超类中的某个字段。这时，我们可以把**一个类的特性（字段或方法）声明为protected**在JAVA中，受保护的字段只能被同一个包中的类访问。

---

## 2.2.1 Object 类型的变量
可以使用Object类型的变量引用任何类型的对象：
`Object obj = new Employee("Harry Hacker",3500);`
要想对obj其中的内容进行具体的操作，还需要强制类型转换成具体类型：
`Employee e =(Employee) obj;`
只有基本类型不是对象，所有数组类型都扩展成了Object类的类型。

---

## 2.2.2 Object的常用方法

---

## 2.3.1 声明数组列表
可以如下·声明和构造一个保存Employee对象的数组列表：
`ArrayList<Empolyee> staff = new ArrayList<Empolyee>();`
也可以用var代替：
`var staff = new ArrayList<Empolyee>();`
还可以将后面的参数类型省略：
`ArrayList<Empolyee> staff = new ArrayList<>();`
使用add方法可以在列表中加入数据：
`staff.add(new Empolyee("harry",...));`

---
## 2.3.2 访问数组元素
不能使用[]语法格式访问或改变数组列表的元素，而要用get和set方法。
例如，设置第i个元素，可以用
`staff.set(i,harry);`
它等价于对数组a的元素赋值：
`a[i]=harry`
要得到一个数组列表的元素，可以使用
`Employee e =staff.get(i);`
这等价于：
`Employee e = a[i];`
>没有泛型类时，原始的ArrayList类提供的get方法只能返回Object。因此要强制转换为所需类型。
`Employee e =(Employee) staff.get(i);`

---

## 2.4 对象包装器与自动装箱
有时，需要将int这样的基本类型转换为一个对象。所有的基本类型都要一个与之对应的类。例如，Integer类对应着基本类型int。通常，这些类被称为**包装器**。
*包装器类是不可变的，一旦构造了包装器，就不允许更改包装器在其中的值。同时，包装器类还是final，因此不能派生它们的子类。*
可以很容易的向`ArrayList<Integer>`添加元素：
`list.add(3);`可以自动转化为`list.add(Integer.valueOf(3));`
这种转换称为**自动装箱**。
相反的，一个Integer对象赋值给int值时，将会自动给拆箱：
`int n = list.get(i);`转换成`int n = list.get(i).intValuue();`
大多数情况下容易有一种假象，认为基本类型与它们的包装器是一样的。但他们有一点很大不同：同一性。==运算符可以应用于包装器对象，不过检测到是对象的内存是否有相同的内存位置，因此，下列代码可能会失败：
```
Integer a =1000;
Integer b =1000;
if(a==b)...
```
由于包装器类引用可以为null，所以自动装箱可能会抛出NullPointException异常:
```
Integer n = null;
System.out.println(2*n);// throws NullPointException
```
另外，如果在一个条件表达式中混合使用Integer和Double类型，则Integer值会拆箱，提升为double再装箱为Double
```
Integer n = 1;
Double x = 2.0;
System.out.println(true ? n : x); //prints 1.0
```

---

##2.5 参数个数可变的方法
有时，这些方法被称为“变参”方法。
有这样一个方法：printf。例如，可以这样调用：
`System.out.print("%d",n);`和`System.out.println("%d %s",n,"widgets");`
两个语句都调用这个方法，只不过参数的个数不一样。
printf方法是这样定义的:
public class PrintStream{
    public PrintStream printf(String fmt,Object... args){
        return format(fmt,args);
    }
}
这里的“...”表明这个方法可以接受任意数量的对象（除fmt参数以外）。
你也可以定义自己的有可变个数参数的方法，可以指定参数为任意类型，甚至可以是基本类型。下面的例子功能可以计算若干个数中的最大值：
```
public static double max(double... values){
    double largest = Double.NEGATIVE_INFINITY;
    for(double v: values){
       if(v > largest) largest = v; 
    } 
    return largest;
}
```
可以像下面这样调用这个函数：
`double m = max(3.14,40.4,-5);`
编译器将new double[]{3.14 , 40.4 , -5}传递给max函数。

---

##2.6 抽象类
对于学生和员工来说，他们都是人。所以，我们可以把getName方法放到继承层次结构中的更高一层。
现在，再增加一个getDescription方法，它可以返回对一个人的简短描述。
在Employee和Student中实现这个类很简单，可在Person中能提供什么信息呢？其返回的只有一个空字符串。不过还有以个更好的方法，如果使用abstract关键字就根本不需要实现这个方法了。
public abstract String getDescription();
为了提高程序的清晰性，包含一个或多个抽象方法的类本身必须声明为抽象的。
```
public abstract class Person{
    ...
    public abstract String getDescription();
}
```
除了抽象方法之外，抽象类还可以包含字段和具体方法。抽象方法相当于子类中实现具体方法的占位符。
**扩展一个抽象类时，可以有两种选择**
- 一种是在子类中保留抽象类中的部分或所有抽象方法仍未定义，这样就必须将子类也标为抽象类；
- 另一种就是定义全部方法，这样一来，子类就不再是抽象类。

即使不含抽象方法，也可以将类声明为abstract。抽象类不能实例化，但可以具体子类的对象。需要注意的是，仍可以创建一个抽象类的对象变量，但是它只能引用非抽象子类的对象：
`Person p =new Student("Vince Vu","Economics");`

---

##2.7 枚举类
`public enum Size{ SMALL, MEDIUM, LARGE, EXTRA_LARGE}`
实际上，这个声明定义的类型是一个类，它刚好有4个实例，不可能构造新的对象。
因此，在比较枚举类的值时，并不需要使用equals，可以直接使用 == 来比较。
如果需要的话，可以为枚举类型增加构造器、方法和字段，构造器只是在构造构造枚举常量的时候使用：
```
public enum Size{
    SMALL("S"),MEDIUM("M"), LARGE("L"), EXTRA_LARGE("XL");
    private String abbreviation;
    Size(String abbreviation){
        this.abbreviation = abbreviation;
    }

    public String getAbbreviation(){ return abbreviation; }
}
```
枚举构造器总是私有的，如果声明一个枚举的构造器为public或protected，就会出现语法错误。
所有枚举类型都是enum类型的子类。

---

## 2.8 继承的设计技巧

**1. 将公共操作和字段放在超类中。
2. 不要使用受保护的字段。
3. 使用继承实现“is-a”关系。
4. 除非所有继承方法都是有意义的，否则不要使用继承。
5. 覆盖方法时，不要改变预期的行为。
6. 使用多态，而不要使用类型信息**
