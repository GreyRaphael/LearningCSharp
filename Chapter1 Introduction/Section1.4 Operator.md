# C# Operator

- [C# Operator](#c-operator)
    - [`new` operator](#new-operator)
        - [`new` keyword](#new-keyword)
    - [`checked` & `unchecked` operator](#checked-unchecked-operator)
    - [`delegate`](#delegate)
    - [`sizeof`](#sizeof)
    - [`->` operator](#operator)
    - [一元操作符](#%E4%B8%80%E5%85%83%E6%93%8D%E4%BD%9C%E7%AC%A6)
    - [`=`赋值](#%E8%B5%8B%E5%80%BC)
    - [`await`操作符](#await%E6%93%8D%E4%BD%9C%E7%AC%A6)
    - [类型转换](#%E7%B1%BB%E5%9E%8B%E8%BD%AC%E6%8D%A2)
        - [implicit](#implicit)
            - [小东西到大盒子](#%E5%B0%8F%E4%B8%9C%E8%A5%BF%E5%88%B0%E5%A4%A7%E7%9B%92%E5%AD%90)
            - [子类向父类的隐式转换](#%E5%AD%90%E7%B1%BB%E5%90%91%E7%88%B6%E7%B1%BB%E7%9A%84%E9%9A%90%E5%BC%8F%E8%BD%AC%E6%8D%A2)
            - [boxing](#boxing)
        - [explicit](#explicit)
            - [cast `()`](#cast)
            - [unboxing](#unboxing)
            - [`class Convert`](#class-convert)
            - [`ToString`, `Parse`, `TryParse`](#tostring-parse-tryparse)
        - [自定义类型转换操作符](#%E8%87%AA%E5%AE%9A%E4%B9%89%E7%B1%BB%E5%9E%8B%E8%BD%AC%E6%8D%A2%E6%93%8D%E4%BD%9C%E7%AC%A6)
            - [自定义explicit](#%E8%87%AA%E5%AE%9A%E4%B9%89explicit)
            - [自定义implicit](#%E8%87%AA%E5%AE%9A%E4%B9%89implicit)
    - [二元运算符](#%E4%BA%8C%E5%85%83%E8%BF%90%E7%AE%97%E7%AC%A6)
        - [`is` & `as`](#is-as)
    - [与或非](#%E4%B8%8E%E6%88%96%E9%9D%9E)
    - [null值合并：`??`](#null%E5%80%BC%E5%90%88%E5%B9%B6%EF%BC%9A)

操作符、表达式、语句都是为方法服务的。

[operators](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/statements-expressions-operators/operators)

级别从高到低，同级从左往右(赋值是从右往左)，计算机中的同优先级运算没有**结合律**

级别|Operators
---|---
基本|`x.y f(x) a[x] x++ x-- new typeof default checked unchecked delegate sizeof ->`
一元|`+ - ! ~ ++x --x (T)x await &x *x`
乘除|`* / %`
加减|`+ -`
移位|`>> <<`
关系与类型检测|`< > <= >= is as`
相等|`== !=`
bit And|`&`
bit Xor|`^`
bit Or|`|`
条件And|`&&`
条件Or|`||`
null合并|`??`
条件|`?:`
赋值与Lambda|`= *= /= %= += -= <<= >>= &= ^= |= =>`

operator的本质是对函数（算法）的“简记”，被操纵的对象叫做“操作数”（operand），让算式更短

计算机语言中的操作符不能与其相关联的数据类型脱离(`Console.WriteLine(5/4);`结果为1，其中`/`与`int`关联)

所以，计算机的操作符：与固定数据类型关联的一套基本算法简记法

```csharp
using System;
using System.Collections.Generic;

namespace ConsoleApp7
{
    class Program
    {
        static void Main(string[] args)
        {
            Person p1 = new Person() { Name = "Grey" };
            Person p2 = new Person() { Name = "Jane" };
            List<Person> myList = Person.GetMarry(p1, p2);
            foreach (var item in myList) {
                Console.WriteLine(item.Name);
            }
            Console.WriteLine();
            List<Person> nation = p1 + p2;
            foreach (var item in nation) {
                Console.WriteLine(item.Name);
            }
        }
    }

    class Person {
        public string Name { get; set; }
        public static List<Person> GetMarry(Person p1,Person p2) {
            List<Person> people = new List<Person>();
            people.Add(p1);
            people.Add(p2);
            for (int i = 0; i < 5; i++) {
                Person child = new Person();
                child.Name = p1.Name + "&" + p2.Name + "'s child";
                people.Add(child);
            }
            return people;
        }

        public static List<Person> operator +(Person p1, Person p2) {
            List<Person> people = new List<Person>();
            people.Add(p1);
            people.Add(p2);
            for (int i = 0; i < 5; i++) {
                Person child = new Person();
                child.Name = p1.Name + "&" + p2.Name + "'s child";
                people.Add(child);
            }
            return people;
        }
    }
}
```

```bash
#output
Grey
Jane
Grey&Jane's child
Grey&Jane's child
Grey&Jane's child
Grey&Jane's child
Grey&Jane's child

Grey
Jane
Grey&Jane's child
Grey&Jane's child
Grey&Jane's child
Grey&Jane's child
Grey&Jane's child
```

成员访问操作符`.`:

- 访问外层名称空间的子集名称空间
- 访问名称空间的类型
- 访问类型的static 成员
- 访问对象的成员

```csharp
//
using System;

namespace ConsoleApp8
{
    class Program
    {
        static void Main(string[] args)
        {
            System.IO.File.Create("C:/test.txt");
        }
    }
}
```

C#中只要出现了方法的名字，不一定跟着一个`()`(方法调用操作符)，因为可能涉及delegate

```csharp
//delegate
using System;

namespace ConsoleApp8
{
    class Program
    {
        static void Main(string[] args)
        {
            Student stu = new Student();
            Action myAction = new Action(stu.Report);
            myAction();
        }
    }

    class Student {
        public void Report() {
            Console.WriteLine("Hello");
        }
    }
}
```

```csharp
//[]
using System;
using System.Collections.Generic;

namespace ConsoleApp9
{
    class Program
    {
        static void Main(string[] args)
        {
            Dictionary<string, MyClass> myDict = new Dictionary<string, MyClass>();
            for (int i = 0; i < 10; i++) {
                MyClass my = new MyClass();
                my.Name = "Number" + i.ToString();
                myDict.Add(i.ToString(), my);
            }
            Console.WriteLine(myDict["2"].Name);//Number2
        }
    }

    class MyClass {
        public int ID { get; set; }
        public string Name { get; set; }
    }
}
```

[x++ vs ++x](https://microindie.github.io/2017/01/09/x++and++x/)

p++时先把p放到eax，然后加1放到edx，再把edx存回p，也就是说p++立马把p改了，只是后来使用用的是eax寄存器中的值，就是加1之前的值。这里p++需要三条指令。(在寄存器中+1)

而++p在x86里实际只需要一条对内存中p加1的指令`addl $1, -16(%ebp)`，后面如果要使用++p则将其加载到寄存器中来，而如果只进行加1操作不使用值，只需要一条指令。(内存原地+1)

`typeof`查看类型的内部结构(Metadata)

Metadata包含：

- 类型的名称
- 属于哪个namespace
- 父类是谁
- 包好多少方法、属性、事件....

```csharp
//typeof查看类型的内部结构
using System;

namespace ConsoleApp8
{
    class Program
    {
        static void Main(string[] args)
        {
            Type t = typeof(Student);
            Console.WriteLine(t.Name);
            Console.WriteLine(t.FullName);
            Console.WriteLine(t.Namespace);
            Console.WriteLine(t.BaseType);
            foreach (var item in t.GetMethods()) {
                Console.WriteLine(item);
            }
        }
    }

    class Student {
        public void Report() {
            Console.WriteLine("Hello");
        }
    }
}
```

```bash
#output
Student
ConsoleApp8.Student
ConsoleApp8
System.Object
Void Report()
System.String ToString()
Boolean Equals(System.Object)
Int32 GetHashCode()
System.Type GetType()
```

`default`获取类型默认值

- 结构体类型
- 引用类型
- 枚举类型

```csharp
using System;

namespace ConsoleApp8
{
    class Program
    {
        static void Main(string[] args)
        {
            int x = default(int);
            Console.WriteLine(x);//0
            double y = default(double);
            Console.WriteLine(y);//0
            Student stu = default(Student);
            Console.WriteLine(stu==null);//True
            MyEnum my1 = default(MyEnum);
            Console.WriteLine(my1);//Low,返回index=0的那一个，没有0(限定了1,2,3,偏偏没有0)的话会返回0
        }
    }

    class Student {
        public void Report() {
            Console.WriteLine("Hello");
        }
    }

    enum MyEnum {
        Low,
        Mid,
        High,
    }
}
```

## `new` operator

`new`操作符, 创建一个实例，并调用实例构造器`()`，通过`=`将instance的地址给引用变量；

`new`还可以调用实例初始化器`{}`

```csharp
var x = 100;//x must be initialized, x是隐式类型变量
```

```csharp
new Form().ShowDialog()
new Form(){Text="Hello"}.ShowDialog()
```

并不是所有的class创建实例的时候都需要`new`，比如`string`就不需要(C#的语法糖,只是被隐藏了,当然也是可以`new`的)

```csharp
//grammar sugar
int[] myArr1 = new int[10];//没有()
int[] myArr2 = { 1, 2, 3, 4, 5 };
```

`new`后面如果是class叫做非匿名类型，没有名字叫做匿名类型

```csharp
using System;

namespace ConsoleApp1
{
    class Program
    {
        static void Main(string[] args)
        {
            //implicit, var和new配合使用
            //person为隐式类型变量
            //为匿名类型创建instance, 并且用隐式类型变量来引用这个instance
            var person = new { Name = "Grey", Age = 23 };//匿名类型，并且为匿名类型创建instance
            Console.WriteLine($"{person.Name},{person.Age}");//Grey,23
        }
    }
}
```

`new`操作符会让两个class紧密耦合；大型项目为了避免紧耦合，使用**依赖注入技术**实现相对较松的耦合

### `new` keyword

```csharp
using System;

namespace ConsoleApp2
{
    class Program
    {
        static void Main(string[] args)
        {
            Father p1 = new Father();
            p1.Report();
            Son p2 = new Son();
            p2.Report();
        }
    }

    class Father {
        public void Report() {
            Console.WriteLine("I'm Father");
        }
    }

    class Son:Father {
        //new作为关键字，隐藏父类的方法，不常见
        new public void Report() {
            Console.WriteLine("I'm Son");
        }
    }
}
```

## `checked` & `unchecked` operator

检查一个值在内存中是不是有溢出；

```csharp
Console.WriteLine(uint.MaxValue);//4294967295
Console.WriteLine(Convert.ToString(uint.MaxValue,2));//11111111111111111111111111111111

uint x = uint.MaxValue;
uint y = x + 1;
Console.WriteLine(y);//0
```

```csharp
//checked, result is Overflow
using System;

namespace ConsoleApp3
{
    class Program
    {
        static void Main(string[] args)
        {
            uint x = uint.MaxValue;
            try {
                uint z = checked(x + 1);
                Console.WriteLine(z);
            }
            catch (OverflowException e) {

                Console.WriteLine("Overflow");
            }
        }
    }
}
```

```csahrp
//unchecked, result is 0
using System;

namespace ConsoleApp3
{
    class Program
    {
        static void Main(string[] args)
        {
            uint x = uint.MaxValue;
            try {
                uint z = unchecked(x + 1);
                Console.WriteLine(z);
            }
            catch (OverflowException e) {

                Console.WriteLine("Overflow");
            }
        }
    }
}
````

```csharp
//checked, result is Overflow
using System;

namespace ConsoleApp3
{
    class Program
    {
        static void Main(string[] args)
        {
            uint x = uint.MaxValue;
            checked {
                try {
                    uint z = x + 1;
                    Console.WriteLine(z);
                }
                catch (OverflowException e) {

                    Console.WriteLine("Overflow");
                }
            }
        }
    }
}
```

## `delegate`

`delegate`最常用的是声明一种委托数据类型，用于operator过时(因为Lambda表达式的出现，不需要delegate)

lambda可以减少耦合；

```csharp
//without delegate
public partial class MainWindow : Window {
    public MainWindow() {
        InitializeComponent();

        this.btn.Click += Btn_Click;
    }

    private void Btn_Click(object sender, RoutedEventArgs e) {
        this.myText.Text = "Hello";
    }
}
```

```csharp
//delegate,过时了，用lambda
public partial class MainWindow : Window {
    public MainWindow() {
        InitializeComponent();

        this.btn.Click += (object sender, RoutedEventArgs e) => {
            this.myText.Text = "Hello";
        };
    }
}
```

```csharp
//形参类型都可以不要，因为可以推断
public partial class MainWindow : Window {
    public MainWindow() {
        InitializeComponent();

        this.btn.Click += (sender, e) => {
            this.myText.Text = "Hello";
        };
    }
}
```

## `sizeof`

获取结构体数据类型的对象在内存中所占的字节数；

- 只能用于获取基本类型在内存中的字节数，除了`string`和`object`
- 如果要获取自定义类型的instance字节数，需要`unsafe`，以及其他注意事项

```csharp
//decimal占16Bytes,比double更加精确
Console.WriteLine(sizeof(decimal));//16,金融运算
Console.WriteLine(sizeof(long));//8
```

```csharp
using System;

namespace ConsoleApp4
{
    class Program
    {
        static void Main(string[] args)
        {
            unsafe {
                Console.WriteLine(sizeof(MyStruct));//16=4+4+8
            }
        }
    }

    struct MyStruct {
        //这里还必须是struct,下面还必须是变量，不能是属性
        int ID;
        double Score;
    }
}
```

## `->` operator

必须是unsafe，C#中的指针操作、取地址操作、指针访问成员操作只能是对结构体

```csharp
using System;

namespace ConsoleApp5
{
    class Program
    {
        static void Main(string[] args)
        {
            unsafe {
                MyStruct my1;//on stack
                my1.ID = 1;
                my1.Score = 100.5;
                MyStruct* p = &my1;
                p->ID = 100;
                (*p).Score=99;
                Console.WriteLine($"{p->ID},{p->Score}");//100,99
            }
        }
    }

    struct MyStruct {
        //C#中的struct默认是private
        public int ID;
        public double Score;
    }
}
```

## 一元操作符

`-`:取相反数，所以`--x`与`-(-x)`是不相同的；求相反数算法：按位取反加1；

然而，`-`会造成溢出；`-128~127`

```csharp
using System;

namespace ConsoleApp6
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine(int.MaxValue);//2147483647
            Console.WriteLine(int.MinValue);//-2147483648
            int x = int.MinValue;
            Console.WriteLine(Convert.ToString(x,2).PadRight(32,'0'));
            int y = -x;
            Console.WriteLine(y);//-2147483648

            int x2 = 123;
            Console.WriteLine(Convert.ToString(x2, 2).PadLeft(32,'0'));
        }
    }
}
```

```csharp
//! operator
using System;

namespace ConsoleApp7
{
    class Program
    {
        static void Main(string[] args)
        {
            Student stu = new Student(null);
        }
    }

    class Student {
        public Student(string name) {
            if (!string.IsNullOrEmpty(name)) {
                this.Name = name;
            }
            else {
                throw new ArgumentException("initial name cannot be null or empty");//抛出一个匿名对象
            }
        }
        public string Name { get; set; }
    }
}
```

## `=`赋值

`x++`：拿到寄存器++

`++x`：在内存原地++

单独使用`x++`,`++x`，是没有差异的，只有在出现赋值号`=`的时候，或者左值、右值才会出现差异；

## `await`操作符

异步操作才会用到

## 类型转换

```csharp
using System;

namespace CSharp
{
    class Program
    {
        static void Main(string[] args)
        {
            string str1=Console.ReadLine();
            string str2=Console.ReadLine();
            int x1=Convert.ToInt32(str1);
            int x2=Convert.ToInt32(str2);
            System.Console.WriteLine(x1+x2);
            System.Console.WriteLine(Convert.ToInt32(char.MinValue));//0
            System.Console.WriteLine(Convert.ToInt32(char.MaxValue));//65535
        }
    }
}
```

### implicit

不用明确告诉编译器要进行转换：

- 不丢失精度的转换(小东西到大盒子)
- 子类向父类转换
- 装箱：值类型从stack到heap

#### 小东西到大盒子

```csharp
int x=int.MaxValue;
long l=x;//implicit
```

implict convert Table:<<C# language specificaion>> Chapter6.1.2; 从左往右不丢失精度，从右往左可能丢失精度

- sbyte到`short,int,long,float,double,decimal`
- byte到`short,ushort,int,uint,long,ulong,float,double,decimal`
- short到`int,long,float,double,decimal`
- ushort到`int,uint,long,ulong,float,double,decimal`
- int到`long,float,double,decimal`
- uint到`long,ulong,float,double,decimal`
- long到`float,double,decimal`
- ulong到`float,double,decimal`
- char到`ushort,int,uint,long,ulong,float,double,decimal`
- float到`double`

C#的`char`是2Bytes; 从`int,uint,long,ulong`到float的转换, 以及从long,ulong到double的转换可能导致丢失精度，但绝不会影响数值大小；其他转换不丢失任何信息

#### 子类向父类的隐式转换

所有的面向对象编程语言都支持子类向父类的隐式转换；

多态基于**子类向父类的隐式转换**，也就是cpp中父指针可以指向子类的instance

```csharp
using System;

namespace CSharp
{
    class Program
    {
        static void Main(string[] args)
        {
            Teacher t=new Teacher();
            Human h=t;//子类向父类implicit
            Animal a=h;
            h.Eat();
            h.Think();
            //h看不到Teach()
            a.Eat();//a只能看到Eat()
        }
    }

    class Animal
    {
        public void Eat(){
            System.Console.WriteLine("Eating...");
        }
    }

    class Human:Animal
    {
        public void Think(){
            System.Console.WriteLine("Who am I?");
        }
    }

    class Teacher:Human
    {
        public void Teach(){
            System.Console.WriteLine("I'm Teaching");
        }
    }
}
```

#### boxing

```csharp
static void Main(string[] args)
    {
        int x = 100;
        object obj = x;//boxing,object default is 4Byte
        int y = (int)obj;//unboxing
        Console.WriteLine(y);//100
    }
```

### explicit

- 有可能丢失精度的转换，cast
- 拆箱
- convert类
- ToString方法与各种数据类型的Parse/TryParse

#### cast `()`

cast的词源是铸造的意思；

```csharp
System.Console.WriteLine(ushort.MaxValue);//65535
uint x=65536;
ushort y=(ushort)x;//低16位保留，高16位舍弃
System.Console.WriteLine(y);//0
```

显示类型转换要注意正负号:

Table:<<C# language specificaion>> Chapter6.1.2

#### unboxing

```csharp
static void Main(string[] args)
    {
        int x = 100;
        object obj = x;//boxing,object default is 4Byte
        int y = (int)obj;//unboxing
        Console.WriteLine(y);//100
    }
```

#### `class Convert`

19种重载，够用，包括了下面的ToString,Parse,TryParse

#### `ToString`, `Parse`, `TryParse`

```csharp
using System;

namespace CSharp
{
    class Program
    {
        static void Main(string[] args)
        {
            //ToString()：来自class object
            double d0=100.0;
            string str1=d0.ToString();
            //Parse
            double d1=double.Parse(str1);
            double d2=double.Parse("12.5");
            System.Console.WriteLine(d1+d2);//112.5
            //TryParse
            double d3;
            if (double.TryParse("1e3",out d3))
            {
                System.Console.WriteLine(d3);//1000
            }
        }
    }
}
```

### 自定义类型转换操作符

MSDN规定不能赋值重载`=`

#### 自定义explicit

```csharp
using System;

namespace CSharp
{
    class Program
    {
        static void Main(string[] args)
        {
            //stone to monkey
            Stone stone=new Stone(){Age=5000};
            Monkey wukongSun=(Monkey)stone;//explicit
            System.Console.WriteLine(wukongSun.Age);//10
        }
    }

    class Stone
    {
        public int Age { get; set; }
        public static explicit operator Monkey(Stone stone){
            Monkey m=new Monkey();
            m.Age=stone.Age/500;
            return m;
        }
    }

    class Monkey
    {
        public int Age { get; set; }
    }
}
```

#### 自定义implicit

```csharp
using System;

namespace CSharp
{
    class Program
    {
        static void Main(string[] args)
        {
            //stone to monkey
            Stone stone=new Stone(){Age=5000};
            Monkey wukongSun=stone;//implicit，修改这里
            System.Console.WriteLine(wukongSun.Age);//10
        }
    }

    class Stone
    {
        public int Age { get; set; }
        public static implicit operator Monkey(Stone stone){//修改这里
            Monkey m=new Monkey();
            m.Age=stone.Age/500;
            return m;
        }
    }

    class Monkey
    {
        public int Age { get; set; }
    }
}
```

## 二元运算符

如果两边的数据类型不同，会自动进行数值提升；

```csharp
var x=3.0/4;//4被提升为double

int x1=5;
int x2=0;
// int x3=x1/x2;//异常

double d0=-10;
double d1=10;
double d2=0;
System.Console.WriteLine(d0/d2);//-Infinity
System.Console.WriteLine(d1/d2);//Infinity

double a1=double.PositiveInfinity;
double a2=double.NegativeInfinity;
Console.WriteLine(a1/a2);//NaN

System.Console.WriteLine((double)5/4);//1.25,因为()比/优先级高

int x=5;
double y=4.0;
System.Console.WriteLine(x>y);//True
```

```csharp
//string compare
System.Console.WriteLine((ushort)'b');//98
System.Console.WriteLine((ushort)'B');//66
string str1="abcD";
string str2="aBcd";
System.Console.WriteLine(str1.ToLower()==str2.ToLower());//True
System.Console.WriteLine(string.Compare("B","b"));//1
System.Console.WriteLine(string.Compare(str1,str2));//-1
```

### `is` & `as`

is的返回值是bool;

as用于转换；

```csharp
using System;

namespace CSharp
{
    class Program
    {
        static void Main(string[] args)
        {
            Teacher teacher=new Teacher();
            var res=teacher is Teacher;
            System.Console.WriteLine(res);//True

            Teacher t2=null;
            System.Console.WriteLine(t2 is Teacher);//False

            Teacher t3=new Teacher();
            System.Console.WriteLine(t3 is Human);//True
            System.Console.WriteLine(t3 is Animal);//True
            System.Console.WriteLine(t3 is object);//True,注意

            object o1=new Teacher();
            if (o1 is Teacher)
            {
                Teacher t4=(Teacher)o1;
                t4.Teach();//I'm Teaching
            }

            //as
            object o2=new Teacher();
            Teacher t5=o2 as Teacher;//如果o2像Teacher一样，那么将instance的地址交给t5，否则t5为null
            if (t5!=null)
            {
                t5.Teach();
            }
        }
    }

    class Animal
    {
        public void Eat(){
            System.Console.WriteLine("Eating...");
        }
    }

    class Human:Animal
    {
        public void Think(){
            System.Console.WriteLine("Who am I?");
        }
    }

    class Teacher:Human
    {
        public void Teach(){
            System.Console.WriteLine("I'm Teaching");
        }
    }
}
```

## 与或非

```csharp
int x=3;
int y=4;
int a=10;
//避开这种逻辑，++尽量单独使用
if (x>y && ++a>y)
{
    System.Console.WriteLine(a);
}
System.Console.WriteLine(a);//10
```

## null值合并：`??`

给基本的数据类型(比如`int`)以`null`

```csharp
Nullable<int> x=null;//因为太常用了，用了语法糖int?
System.Console.WriteLine(x.HasValue);//False
x=100;
System.Console.WriteLine(x.HasValue);//True
System.Console.WriteLine(x.Value);//100

int? x1=null;
System.Console.WriteLine(x1.HasValue);//False

int y=x??1;//如果x为null，那么y=1，否则y=x
System.Console.WriteLine(y);//100

int y1=x1??1;//如果x为null，那么y=1，否则y=x
System.Console.WriteLine(y1);//1
```