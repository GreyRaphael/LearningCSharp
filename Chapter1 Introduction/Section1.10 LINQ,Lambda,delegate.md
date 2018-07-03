# C# delegate, Lambda, LINQ

- [C# delegate, Lambda, LINQ](#c-delegate-lambda-linq)
    - [delegate](#delegate)
        - [generic delegate(解决了class膨胀的问题)](#generic-delegate%E8%A7%A3%E5%86%B3%E4%BA%86class%E8%86%A8%E8%83%80%E7%9A%84%E9%97%AE%E9%A2%98)
    - [Lambda](#lambda)
    - [LINQ(.NET Language Integrated Query)](#linqnet-language-integrated-query)

## delegate

```csharp
//what is delegate
using System;

namespace ConsoleApp1
{
    class Program
    {
        static void Main(string[] args)
        {
            Type type1 = typeof(MyDele);
            Console.WriteLine(type1.IsClass);//True
            //
            MyDele myDele = new MyDele(func1);
            myDele += func1;//multicast
            myDele += (new Student()).SayHello;
            myDele();
            // myDele().Invoke();
        }

        static void func1() {
            Console.WriteLine("Hello");
        }
    }

    delegate void MyDele();

    class Student {
        public void SayHello() {
            Console.WriteLine("I'm a student");
        }
    }
}

```

```bash
#output
True
Hello
Hello
I'm a student
```

### generic delegate(解决了class膨胀的问题)

```csharp
//diy泛型委托
using System;

namespace ConsoleApp2
{
    class Program
    {
        static void Main(string[] args)
        {
            MyDele<int, double> myDele = new MyDele<int, double>(MyAdd);
            double res = myDele.Invoke(100, 200);
            Console.WriteLine($"res={res},type is {res.GetType().Name}");//res=300,type is Double
            //
            MyDele<double, double> myDele2 = new MyDele<double, double>(MyMul);
            Console.WriteLine(myDele2.Invoke(100,200));//
            //
            Console.WriteLine(myDele.GetType().IsClass);//True
        }

        static double MyAdd(int a,int b) {
            return a + b;
        }

        static double MyMul(double a,double b) {
            return a * b;
        }
    }

    delegate T2 MyDele<T1, T2>(T1 a, T1 b);
}
```

```csharp
//没必要自己动手，.net自带了Action,Func<>
using System;

namespace ConsoleApp3
{
    class Program
    {
        static void Main(string[] args)
        {
            Action<string> action1 = new Action<string>(SayHello);
            Func<int, int, double> func1 = new Func<int, int, double>(MyAdd);
            var func2 = new Func<double, double, double>(MyMul);
            //
            action1.Invoke("Grey");
            Console.WriteLine(func1.Invoke(100,200));//300
            Console.WriteLine(func2.Invoke(100,200));//20000
        }

        static double MyAdd(int a, int b) {
            return a + b;
        }

        static double MyMul(double a, double b) {
            return a * b;
        }

        static void SayHello(string Name) {
            Console.WriteLine($"Greeting, {Name}");
        }
    }
}
```

## Lambda

Lambda表达式作用:lambda expression is an inline anonymous method.可以提高运行效率(因为inline)，减少细碎的小函数

- 匿名方法(anonymous)
- Incline方法：调用的时候才去声明

面试：通常考多知识交汇的地方，面试效率高成本低

- Lambda, interface,abstract class,类的关系,多态,操作符

```csharp
using System;

namespace ConsoleApp5
{
    class Program
    {
        static void Main(string[] args)
        {
            Func<double, double, double> func1 = (a, b) => { return a + b; };
            Func<int, int, int> func2 = (a, b) => { return a * b; };
            Console.WriteLine(DoCalc<double>(func1,10,20.5));//30.5
            Console.WriteLine(DoCalc<int>(func2,10,20));//200
            Console.WriteLine(DoCalc<double>((x,y)=> { return x * y; },20.5,30));//615，根据<double>来推
            Console.WriteLine(DoCalc((x,y)=> { return x * y; },20.5,30));//615,根据数据类型推断
        }

        static T DoCalc<T>(Func<T,T,T> func,T a,T b) {
            return func.Invoke(a, b);
        }
    }
}
```

```csharp
using System;

namespace ConsoleApp9
{
    class Program
    {
        static void Main(string[] args)
        {
            Action<string> action1 = p => Console.WriteLine("Hello,"+p);
            action1.Invoke("grey");
            Func<string, bool> func1 = p => p == "grey";
            Console.WriteLine(func1("grey"));//True
        }
    }
}
```

## LINQ(.NET Language Integrated Query)

LINQ只是Lambda的应用而已

- 将.NET代码翻译为SQL代码，把东西query出来，形成一组对象；临时用一用，推荐用SQL
- 还可查询本地的集合(List)，大部分用于与数据库查数据库

LINQ2SQL是一组比较小的类库，集成到了**.NET Framewor**，功能弱;另一个是**Entity Framework**(EF)在Nuget中，功能很强；

EF: 是ORM(Object to Relation database Mapping),把数据库中的一张一张的表变成对象

```csharp
//把Lambda Expression作为实参交给LINQ的函数
var dbContext=new AdventureWorks2014Entities();
var allMatch=dbContext.People.Where(p=>p.FirstName=="Timothy").Select(p=>p.FirstName+" "+p.LastName).ToList();
forearch(var item in allMatch){
    Console.WriteLine(item);
}
```