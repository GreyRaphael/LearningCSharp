# Override & Polymorphism

- [Override & Polymorphism](#override-polymorphism)
    - [Rider Tutorial](#rider-tutorial)
    - [content](#content)
        - [`function members`](#function-members)
        - [visible(可见)](#visible)
        - [签名一致](#)
        - [代差](#)

Java用的框架是Spring, Hibenate(JPA); 分别对应ASP.NET Core, Entity Framework core;

[Timothy Examples](https://www.edx.org/course?search_query=timothy+liu)

[C# 语言规范](https://www.ecma-international.org/publications/standards/Ecma-334.htm)

[javascript 语言规范](https://www.ecma-international.org/publications/standards/Ecma-262.htm)

## Rider Tutorial

`Alt+Insert`: Create a constructor

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Security.Cryptography;

namespace HelloRider
{
    class Program
    {
        static void Main(string[] args)
        {
            var values = Enumerable.Range(0, 10).ToArray();
            var bst = GetTree(values, 0, values.Length - 1);
            DFS(bst);
            Console.WriteLine("================");
            BFS(bst);
        }

        static Node GetTree(int[] values, int lower_indx, int higher_indx)
        {
            if (lower_indx > higher_indx) return null;
            var middle_indx = lower_indx + (higher_indx - lower_indx) / 2;
            var node=new Node(values[middle_indx]);
            node.Left = GetTree(values, lower_indx, middle_indx - 1);
            node.Right = GetTree(values, middle_indx + 1, higher_indx);
            return node;
        }

        static void DFS(Node node)
        {
            if(node==null) return;
            DFS(node.Left);
            Console.WriteLine(node.Value);
            DFS(node.Right);
        }

        static void BFS(Node root)
        {
            var q = new Queue<Node>();
            q.Enqueue(root);
            while (q.Count>0)
            {
                var node = q.Dequeue();
                Console.WriteLine(node.Value);
                if (node.Left!=null) q.Enqueue(node.Left);
                if (node.Right!=null) q.Enqueue(node.Right);
            }
        }
    }

    class Node
    {
        public int Value { get; set; }
        public Node Left { get; set; }
        public Node Right { get; set; }

        public Node(int value)
        {
            Value = value;
        }
    }
}
```

## content

类的继承:

- 类成员的**横向扩展**(成员越来越多)
- 类成员的**纵向扩展**(行为改变，版本增高)
- 类成员的隐藏(不常用)
- 重写与隐藏发生的条件: 函数成员(function memebers)，可见，签名一致

多态(polymorphism):

- 基于override机制(virtual→override)
- 函数成员的具体行为(版本)由对象决定
- C#中可以用父类类型变量引用子类类型instance

父类没有`virtual`, 子类没有`override`；子类对于父类隐藏

```csharp
// 未构成重写
using System;

namespace ConsoleApp4 {
    class Program {
        static void Main(string[] args) {
            var v = new Vehicle();
            var car = new Car();
            v.Run();
            car.Run();
        }
    }

    class Vehicle {
        public void Run() {
            Console.WriteLine("I'm running!");
        }
    }

    class Car:Vehicle {
        public int speed { get; set; }
        public void Run() {
            Console.WriteLine("Car is running");
        }
    }
}
```

加入`virtual`, `override`可以实现polymorphism

```csharp
// 未构成重写
// 加入virtual override
using System;

namespace ConsoleApp4 {
    class Program {
        static void Main(string[] args) {
            var v = new Vehicle();
            var car = new Car();
            v.Run();
            car.Run();
        }
    }

    class Vehicle {
        public virtual void Run() {
            Console.WriteLine("I'm running!");
        }
    }

    class Car:Vehicle {
        public int speed { get; set; }
        public override void Run() {
            Console.WriteLine("Car is running");
        }
    }
}
```

如果没有`virtual`, `override`那么结果是`I'm running`;

- 相当于没有`virtual`, `override`的时候，一个Car里面有两个版本的`Run()`;
- 加入`virtual`, `override`后，一个Car里面只有一个版本的`Run()`;

然而Java天然就是自动override, 所以注意一点；

```csharp
// 实现polymorphism
using System;

namespace ConsoleApp4 {
    class Program {
        static void Main(string[] args) {
            // 父类类型变量引用子类类型instance;类似cpp父类型指针指向子类型数据

            Vehicle v=new Car();
            v.Run();//Car is running
        }
    }

    class Vehicle {
        public virtual void Run() {
            Console.WriteLine("I'm running!");
        }
    }

    class Car:Vehicle {
        public int speed { get; set; }
        public override void Run() {
            Console.WriteLine("Car is running");
        }
    }
}
```

```java
// Java版本
public class Main {

    public static void main(String[] args) {
        Vehicle v=new Car();
        v.Run();
    }
}

class Vehicle{
    public void Run(){
        // 快捷键sout
        System.out.println("I'm running");
    }
}

class Car extends Vehicle{
//  不加Override这个检查用的东西，也可override
    @Override
    public void Run() {
        System.out.println("Car is running");
    }
}
```

`virtual`翻译成**虚**不合适，应该翻译为**名存实亡**, 或者**名义上**

```csharp
using System;

namespace ConsoleApp4 {
    class Program {
        static void Main(string[] args) {
            // 父类类型变量引用子类类型instance;类似cpp父类型指针指向子类型数据
            Vehicle v=new Car();
            v.Run();//Car is running
            v=new RaceCar();
            v.Run();//Race car is running
        }
    }

    class Vehicle {
        public virtual void Run() {
            Console.WriteLine("I'm running!");
        }
    }

    class Car:Vehicle {
        public int speed { get; set; }
        public override void Run() {
            Console.WriteLine("Car is running");
        }
    }

    class RaceCar:Vehicle {
        public override void Run() {
            Console.WriteLine("Race car is running");
        }
    }
}
```

多次override

```csharp
using System;

namespace ConsoleApp4 {
    class Program {
        static void Main(string[] args) {
            Car c=new RaceCar();
            c.Run();// Race car is running
        }
    }

    class Vehicle {
        public virtual void Run() {
            Console.WriteLine("I'm running!");
        }
    }

    class Car:Vehicle {
        public int speed { get; set; }
        public override void Run() {
            Console.WriteLine("Car is running");
        }
    }

    // 上面的例子继承Vehicle;
    // 这个例子继承Car;
    class RaceCar:Car {
        public override void Run() {
            Console.WriteLine("Race car is running");
        }
    }
}
```

以前面试下面这个奇葩的工程方面的东西；现在都是面试算法；

```csharp
using System;

namespace ConsoleApp4 {
    class Program {
        static void Main(string[] args) {
            Vehicle v=new RaceCar();
            // 因为RaceCar里面没有override; 继承链被打断
            // v顺着继承链往下找，找到的最新版本是Car中的Run
            v.Run();// Car is running
        }
    }

    class Vehicle {
        public virtual void Run() {
            Console.WriteLine("I'm running!");
        }
    }

    class Car:Vehicle {
        public int speed { get; set; }
        public override void Run() {
            Console.WriteLine("Car is running");
        }
    }

    class RaceCar:Car {
        public void Run() {
            Console.WriteLine("Race car is running");
        }
    }
}
```

1. `function members`

函数成员不仅是`method`, `property`也可以作为函数成员；详细参考前面的[C# 语言规范](https://www.ecma-international.org/publications/standards/Ecma-334.htm)

因为属性是对字段的用了get, set函数进行了包装

```csharp
using System;

namespace ConsoleApp4 {
    class Program {
        static void Main(string[] args) {
            Vehicle v=new Car();
            v.Run();// Car is running
            Console.WriteLine(v.Speed);//50
        }
    }

    class Vehicle {
        private int _speed;

        public virtual  int Speed {
            get { return _speed; }
            set { _speed = value; }
        }
        public virtual void Run() {
            Console.WriteLine("I'm running!");
            _speed = 100;
        }
    }

    class Car:Vehicle {
        private int _rpm;

        public override int Speed {
            get { return _rpm / 100; }
            set { _rpm = value * 100; }
        }

        public override void Run() {
            Console.WriteLine("Car is running");
            _rpm = 5000;
        }
    }

    class RaceCar:Car {
        public void Run() {
            Console.WriteLine("Race car is running");
        }
    }
}
```

2. 对子类visible(可见)

当父类是`public`或者`protected`的时候，是可见的

3. 签名一致

父子类：方法名，参数列表一致

4. 代差

因为python的类型没有前缀，变量的类型都是跟着instance的走的；所以不需要用到父类指针指向子类instance; 所以python, js有override, 但是没有csharp, java中的这种polymorphism; 但是可以通过第三方的lib实现奇葩操作；

但是typescript因为也是强类型的，所以可以表现polymorphism

```python
class Vehicle:
    def run(self):
        print("I'm running")


class Car(Vehicle):
    def run(self):
        print("Car is running")


class RaceCar(Car):
    def run(self):
        print("Race car is running")


v = Vehicle()
v.run() # I'm running
v = RaceCar()
v.run() # Race car is running
```