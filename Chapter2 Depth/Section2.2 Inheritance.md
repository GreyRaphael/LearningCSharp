# C# class inheritance

- [C# class inheritance](#c-class-inheritance)
    - [what is inheritance](#what-is-inheritance)
    - [inheritance affect 类成员的访问](#inheritance-affect-%E7%B1%BB%E6%88%90%E5%91%98%E7%9A%84%E8%AE%BF%E9%97%AE)
    - [继承与访问控制](#%E7%BB%A7%E6%89%BF%E4%B8%8E%E8%AE%BF%E9%97%AE%E6%8E%A7%E5%88%B6)
    - [面向对象的实现风格](#%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E7%9A%84%E5%AE%9E%E7%8E%B0%E9%A3%8E%E6%A0%BC)

面向对象最显著的三个特征：**封装、继承、多态**;而多态基于继承

## what is inheritance

术语:

- base-class,derived-class;
- parent-class, clild-class;

.NET的继承是单根的，也就是所有的类型默认继承自`:object`

```csharp
//class object

namespace System {
    public class Object {
        public Object();
        ~Object();
//
        public static bool Equals(Object objA, Object objB);
        public static bool ReferenceEquals(Object objA, Object objB);
        public virtual bool Equals(Object obj);
        public virtual int GetHashCode();
        public Type GetType();
        public virtual string ToString();
        protected Object MemberwiseClone();
    }
}
```

```csharp
using System;

namespace ConsoleApp1 {
    class Program {
        static void Main(string[] args) {
            Type t1 = typeof(Car);
            Console.WriteLine(t1.FullName);//ConsoleApp1.Car
            Console.WriteLine(t1.BaseType);//ConsoleApp1.Vehicle
            Console.WriteLine(t1.BaseType.BaseType);//System.Object
            Console.WriteLine(t1.BaseType.BaseType.BaseType == null);//True,所以Object已经是继承链的顶端
        }
    }

    class Vehicle {

    }

    class Car : Vehicle {

    }
}
```

是一个(is a):一个子类的实例，同时也是一个父类的实例(父类引用可以引用子类的实例)

```csharp
using System;

namespace ConsoleApp1
{
    class Program
    {
        static void Main(string[] args)
        {
            //a Car is a Vehicle
            Car car = new Car();
            Vehicle vehicle1 = new Vehicle();
            Vehicle vehicle2 = new Vehicle();
            //
            Console.WriteLine(car is Car);//True
            Console.WriteLine(car is Vehicle);//True
            Console.WriteLine(car is object);//True
            //
            Console.WriteLine(vehicle1 is Car);//False
            Console.WriteLine(vehicle1 is Vehicle);//True
            Console.WriteLine(vehicle1 is object);//True
        }
    }

    class Vehicle {

    }

    class Car:Vehicle {

    }
}
```

Attention:

- `sealed`修饰的`class`不能作为base-class
- C#中一个class最多只能有一个base-class，可以有多个base-interface；C++中可以有多个base-class；
- 子类的访问级别不能超过base-class；默认是`internal`(assembly级别)

## inheritance affect 类成员的访问

继承的本质：派生类在base-class已有的**成员**的基础上，对base-class进行**横向和纵向**的扩展

- 当继承发生的时候，子类对父类是**全盘继承**的
- 派生的过程中，进行的是扩展(member只能越来越多，不能越来越少)。所以类库设计的时候，不要贸然引入新的类成员，因为很可能造成继承链、api的污染
- **横向**：类成员在数量上的扩充
- **纵向**：不增加类成员的个数，对某些类成员进行版本的扩充(`override`)

静态语言不能削减成员个数(静态语言:C++,Java,C#);动态语言可以做到(动态语言:python,JavaScript)

```csharp
//全盘继承
using System;

namespace ConsoleApp1
{
    class Program
    {
        static void Main(string[] args)
        {
            RaceCar raceCar = new RaceCar();
            raceCar.Owner = "Grey";
        }
    }

    class Vehicle {//默认是Vehicle:Object
        public string Owner { get; set; }
    }

    class Car:Vehicle {

    }

    class RaceCar:Car {

    }
}
```

```csharp
//子类创建instance的时候，先掉用的base-class的ctor构造base-class的instance，往下一级一级再调用自己的ctor
using System;

namespace ConsoleApp1
{
    class Program
    {
        static void Main(string[] args)
        {
            RaceCar raceCar = new RaceCar();
            Console.WriteLine(raceCar.Owner);//Moris
            raceCar.ShowOwner();//Owner=Moris,base-class Owner=Moris
        }
    }

    class Vehicle {//默认是Vehicle:Object
        public Vehicle() {
            this.Owner = "Grey";
        }
        public string Owner { get; set; }
    }

    class Car:Vehicle {
        public Car() {
            this.Owner = "James";
        }
    }

    class RaceCar:Car {
        public RaceCar() {
            this.Owner = "Moris";
        }
        //
        public void ShowOwner() {
            Console.WriteLine($"Owner={this.Owner},base-class Owner={base.Owner}");//base关键字只能向上访问一层,这个例子this.Owner==base.Owner
        }
    }
}
```

base-class的ctor不能被derived-class所继承

```csharp
using System;

namespace ConsoleApp3
{
    class Program
    {
        static void Main(string[] args)
        {
            Car car = new Car("Grey");
            Console.WriteLine(car.Owner);
        }
    }

    class Vehicle {
        public string Owner { get; set; }
        public Vehicle(string name) {
            this.Owner = name;
        }
    }

    class Car:Vehicle {
        public Car(string name):base(name) {
            //这里可以空着
        }
    }
}
```

## 继承与访问控制

**类成员的访问级别**是以**类的访问级别**为上限的；(internal, public)

- `public`: 无限制
- `internal`:限制在assembly中(一个assembly可以有多个class;assembly中class默认是internal)
- `private`: 限制在class中(class中的property默认是private)
- `protected`:限制在继承链上

```csharp
//private example
using System;

namespace ConsoleApp5
{
    class Program
    {
        static void Main(string[] args)
        {
            Car car = new Car();
            car.Accelerate();
            car.Accelerate();
            Console.WriteLine(car.Speed);//20,证明即便是private的_rpm也是全盘继承，只是没有访问权限而已
        }
    }

    class Vehicle {
        private int _rpm;
        public void Accelerate() {
            _rpm += 1000;
        }
        public int Speed { get { return _rpm / 100; } }
    }

    class Car:Vehicle {

    }
}
```

```csharp
//protected example
using System;

namespace ConsoleApp5
{
    class Program
    {
        static void Main(string[] args)
        {
            Car car = new Car();
            car.Refuel();
            car.TurboAccelerate();
            Console.WriteLine($"speed is {car.Speed},remaining fuel={car.RemainedFuel}");//speed is 30,remaining fuel=97
        }
    }

    class Vehicle {
        private int _rpm;
        private int _fuel;
        public void Accelerate() {
            Burn();
            _rpm += 1000;
        }
        public void Refuel() {
            _fuel = 100;
        }
        protected void Burn() {
            _fuel--;
        }
        public int Speed { get { return _rpm / 100; } }
        public int RemainedFuel { get { return _fuel; } }
    }

    class Car:Vehicle {
        public void TurboAccelerate() {
            for (int i = 0; i < 3; i++) {
                Accelerate();
            }
        }
    }
}
```

## 面向对象的实现风格

派生和继承的风格：

- class-based(现在是主流):C#,Java,C++
- prototype-based: JavaScript

```java
package com.ccc;

public class Main{
    public static void main(String[] args){
        Car car=new Car();
        car.owner="Grey";
        System.out.printLn(car.owner);
}

class Vehicle{//默认派生自Object。Java不是单根的继承(引用类型继承自Object,基本类型继承自primitive),C#是单根继承Object
    public String owner;
}

class Car extends Vehicle{//Java的继承使用extends,实现接口是implements

}
```

Some Example:

```csharp
//on Linux, start program synchronically
using System;
using System.Diagnostics;

namespace CSharp
{
    class Program
    {
        static void Main(string[] args)
        {
            for (int i = 0; i < 30; i++)
            {
                var myProcess=new Process();
                myProcess.StartInfo.FileName="gedit";
                myProcess.Start();
                myProcess.WaitForExit();
            }
        }
    }
}
```

```csharp
//on Linux, start program asynchronically
using System;
using System.Diagnostics;

namespace CSharp
{
    class Program
    {
        static void Main(string[] args)
        {
            for (int i = 0; i < 30; i++)
            {
                var myProcess=new Process();
                myProcess.StartInfo.FileName="gedit";
                myProcess.Start();
            }
        }
    }
}
```

```csharp
//on Windows, start program synchronically
using System;
using System.Diagnostics;

namespace CSharp
{
    class Program
    {
        static void Main(string[] args)
        {
            for (int i = 0; i < 30; i++)
            {
                var myProcess=new Process();
                myProcess.StartInfo.FileName="notepad";
                myProcess.Start();
                myProcess.WaitForExit();
            }
        }
    }
}
```

```csharp
//on Windows, start program asynchronically
using System;
using System.Diagnostics;

namespace CSharp
{
    class Program
    {
        static void Main(string[] args)
        {
            for (int i = 0; i < 30; i++)
            {
                var myProcess=new Process();
                myProcess.StartInfo.FileName="notepad";
                myProcess.Start();
            }
        }
    }
}
```