# Reflection

<!-- TOC -->

- [Reflection](#reflection)
    - [Introduction](#introduction)
    - [fat interface](#fat-interface)
    - [显式隔离实现(C#独有)](#显式隔离实现c独有)
    - [Reflection(反射)](#reflection反射)
        - [reflection mechnism](#reflection-mechnism)
        - [dependency injection](#dependency-injection)
        - [reflection松开耦合](#reflection松开耦合)

<!-- /TOC -->

## Introduction

特性、依赖注入(dependency injection)都是基于.Net的Reflection机制;

Reflection通常与interface配合使用;

接口中的契约: 提供方不会**少给**, 使用方不会**多要**:
- 少给: 提供方必须实现所有的方法, 如果只是实现到`abstract class`那么照样不能实例化;
- 多要: 传递给使用方的方法，使用方没有用完; 也就是传入的interface太胖了;

related SOLID:
- SOLID中的I: 接口隔离原则，(不能**多要**)
- SOLID中的S: 单一职责原则，一个class只做一件事或者一组相关的事;

以上两者其实是一回事: 接口隔离原则是从使用者的角度来看interface, 单一职责原则是从提供者的角度来看interface;

传入胖interfae, 违反了接口隔离原则(I, 多要了)，单一职责原则(S)

## fat interface

interface太胖原因:
- 设计失误, 一个interface里面包含太多method: 将胖interface拆分，弄成多个本质不同的小的interface
- 多个interface合并导致的胖接口

```csharp
// 一个interface里面包含太多method
// 接口隔离：分开本质不同的interface
using System;

namespace ConsoleApp7 {
    class Program {
        static void Main(string[] args) {
            var driver1 = new Driver(new Car());
            driver1.Drive();
            // 胖接口
            // 传入的fire永远用不到, 所以要将胖interface分开
            // 然后driver不会多要
            var driver2 = new Driver(new T59());
            driver2.Drive();
        }
    }

    class Driver {
        private IVehicle _vehicle;

        public Driver(IVehicle vehicle) {
            _vehicle = vehicle;
        }

        public void Drive() {
            _vehicle.Run();
        }
    }

    interface IVehicle {
        void Run();
    }

    class Car : IVehicle {
        public void Run() {
            Console.WriteLine("car is running");
        }
    }

    class Truck : IVehicle {
        public void Run() {
            Console.WriteLine("truck is running");
        }
    }

    interface IWeapon {
        void Fire();
    }

    // class继承的时候只能有一个基类, 而interface可以继承多个interface
    interface ITank : IVehicle, IWeapon { }

    class T50 : ITank {
        public void Fire() {
            Console.WriteLine("Boom!");
        }

        public void Run() {
            Console.WriteLine("Ka ka ka");
        }
    }

    class T59 : ITank {
        public void Fire() {
            Console.WriteLine("Boooom!");
        }

        public void Run() {
            Console.WriteLine("Ka!Ka!Ka!");
        }
    }

    class T98 : ITank {
        public void Fire() {
            Console.WriteLine("Boooooom!");
        }

        public void Run() {
            Console.WriteLine("Ka!!Ka!!Ka!!");
        }
    }
}
```

Attention: 控制interface的颗粒度, 免得一个interface里面只有一个method(也就是颗粒度太细);

```csharp
// 对于上面的例子，如果将IVehicle变成ITank
// 传入的只能是tank, 也是胖接口，Car, Truck就被挡在外面了
class Driver {
    private ITank _tank;
    public Driver(ITank tank) {
        _tank = tank;
    }
    
    public void Drive() {
        _tank.Run();
    }
}
```

```csharp
// 修改组合而成的胖接口
using System;
using System.Collections;

namespace ConsoleApp8 {
    class Program {
        static void Main(string[] args) {
            int[] nums1 = {1, 2, 3, 4, 5};
            ArrayList nums2 = new ArrayList {1, 2, 3, 4, 5};
            Console.WriteLine(Sum(nums1));
            Console.WriteLine(Sum(nums2));
            //
            var roc = new ReadOnlyCollection(nums1);
            // foreach能够迭代roc
            foreach (var item in roc) {
                Console.WriteLine(item);
            }

            Console.WriteLine(Sum(roc));

            // 修改前传入的interface为ICollection, IColletion的功能在Sun中根本用不到;
            // 传入这样的胖接口
            // 就会将实现IEnumberable的class(比如ReadOnlyCollection)挡在外部
        }

        // ICollection来自IEnumerable
        // Array, ArrayList都实现了IEnumerable, ICollection
//        // 修改前
//        static int Sum(ICollection nums) {
//            int sum = 0;
//            foreach (var num in nums) {
//                sum += (int) num;
//            }
//
//            return sum;
//        }
        static int Sum(IEnumerable nums) {
            int sum = 0;
            foreach (var num in nums) {
                sum += (int) num;
            }

            return sum;
        }
    }

    // 为了测试，自己实现一个class; 只是实现了IEnumerable接口; 没有实现ICollection接口;
    // 也即是只读的实现IEnumerable的class(也就是只能迭代不能添加、删除元素的class)
    class ReadOnlyCollection : IEnumerable {
        private int[] _array;

        public ReadOnlyCollection(int[] array) {
            _array = array;
        }

        // 当外部要迭代这个class的instance的时候，要给别人一个迭代器IEnumerator的instance
        public IEnumerator GetEnumerator() {
            return new Enumerator(this);
        }

        // 为了防止污染外部namespace, 将Enumerator放在class内部
        // 成员class, 全名是ReadOnlyCollection.Enumerator
        // 
        public class Enumerator : IEnumerator {
            private ReadOnlyCollection _collection;
            private int _head;

            public Enumerator(ReadOnlyCollection collection) {
                _collection = collection;
                _head = -1;
            }

            public bool MoveNext() {
                if (++_head < _collection._array.Length) {
                    return true;
                }
                else {
                    return false;
                }
            }

            public void Reset() {
                _head = -1;
            }

//            public object Current {
//                get {
//               //_collection._array[_head]是structure类型,需要boxing成引用类型          
//                    object o = _collection._array[_head];
//                    return o;
//                }
//            }
            // C#6.0新语法; Expression Bodied Members
            // 因为Enumerator是class成员, 所以能够看到private的_array
            // 
            public object Current => _collection._array[_head];
        }
    }
}
```

可能涉及的C#6.0新语法, [Expression Bodied Members](https://csharp.christiannagel.com/2017/01/25/expressionbodiedmembers/)

## 显式隔离实现(C#独有)

C#的interface隔离比其他语言要好; 甚至可以让隔离的接口隐藏起来; 

```csharp
using System;

namespace ConsoleApp9 {
    class Program {
        static void Main(string[] args) {
            // WarmKiller到Killer
            // 这个wk无法看到kill()
            var wk1 = new WarmKiller();
            wk1.love();
            // 这个wk无法看到love()
            IKiller wk2 = wk1;
            wk2.kill();
            
            // Killer到WarmKiller
            // 这个wk无法看到love()
            IKiller wk3 = new WarmKiller();
            wk3.kill();
            // 强制类型转换
            var wk4 = wk3 as WarmKiller;
            var wk5 = (WarmKiller) wk3;
            var wk6 = (IGentleman) wk3;
            // 这个几个wk都看不到kill()
            wk4.love();
            wk5.love();
            wk6.love();
        }
    }

    interface IGentleman {
        void love();
    }

    interface IKiller {
        void kill();
    }

    class WarmKiller : IGentleman, IKiller {
        // 显式interface segregation
        // 只有把这个class的instance当作是IKiller的instance的时候才能调用kill()
        void IKiller.kill() {
            Console.WriteLine("I will kill the enermy!");
        }

        // 普通的interface segragation
        public void love() {
            Console.WriteLine("I will love you forever...");
        }
    }
}
```

## Reflection(反射)

Refletion的意义: 程序在动态运行期, 无法枚举所有的用户操作的类型，所以采用Reflection很方便在动态运行时创建对象;

Reflection的本质: 动态地在内存中拿到对象的类型描述(`Type`， 如果名字采用`TypeInfo`更好理解),然后用`Type`去创建新对象, 所以对于性能有影响;

Reflection是.Net的功能: 给一个对象，即使不用知道具体的静态类型，可以不使用`new`创建一个同类型的对象, 还能访问这个对象的各个members; 

>Reflection意味着可以进一步解耦; 因为有`new`的地方一定是有数据类型的, 就会产生依赖, 而且是紧耦合;

>C#, Jave这些托管类型语言与C/C++原生类型语言的最大的区别, Reflection就能算上一个;

单元测试、依赖注入、泛型编程都基于Reflection;

我们大部分不是直接使用Reflection而是使用封装好的Reflection;

### reflection mechnism

```csharp
// 反射的原理
using System;
using System.Reflection;

namespace ConsoleApp10 {
    class Program {
        static void Main(string[] args) {
            ITank tank = new T98();
            // 其中ITank, T98都是静态类型
            //===below using Reflection===
            // 下面都采用动态类型
            // GetType()得到的是该对象在内存中运行时，与它关联的TypeInfo
            var t = tank.GetType();
            // CreateInstance返回值是object类型, 根据o不能得到methods
            var o = Activator.CreateInstance(t);
            MethodInfo fireMi = t.GetMethod("Fire");
            MethodInfo runMi = t.GetMethod("Run");
            // null指的是没有参数传入
            fireMi.Invoke(o, null);
            runMi.Invoke(o, null);
        }
    }

    class Driver {
        private IVehicle _vehicle;

        public Driver(IVehicle vehicle) {
            _vehicle = vehicle;
        }

        public void Drive() {
            _vehicle.Run();
        }
    }

    interface IVehicle {
        void Run();
    }

    class Car : IVehicle {
        public void Run() {
            Console.WriteLine("car is running");
        }
    }

    class Truck : IVehicle {
        public void Run() {
            Console.WriteLine("truck is running");
        }
    }

    interface IWeapon {
        void Fire();
    }

    interface ITank : IVehicle, IWeapon { }

    class T98 : ITank {
        public void Fire() {
            Console.WriteLine("Boooooom!");
        }

        public void Run() {
            Console.WriteLine("Ka!!Ka!!Ka!!");
        }
    }
}
```

### dependency injection

Nuget安装**Microsoft.Extensions.DependencyInjection**;依赖注入最重要的是容器(Service Provider), 将各种类型、接口放到容器中, 然后向容器要instance(甚至可以设置是单例模式或者创建新对象))

```csharp
// 使用封装好的Reflection
// 其中最主要的就是dependency injection

// 基本用法
class Program {
    static void Main(string[] args) {
        var sc = new ServiceCollection();
        sc.AddScoped(typeof(ITank), typeof(T98));
        var sp = sc.BuildServiceProvider();
        // ===上面是程序开始时的注册
        // 好处在于可以更改T98到根据ITank实现的任意class; 如果是new, 那么要修改每一个new后面的静态类型
        ITank tank = sp.GetService<ITank>();
        tank.Fire();
        tank.Run();
    }
}
```

```csharp
// dependency injection
// Java中的spring框架将依赖注入称作 auto-wire: 自动牵线
class Program {
    static void Main(string[] args) {
        var sc = new ServiceCollection();
        sc.AddScoped(typeof(ITank), typeof(T98));
        sc.AddScoped(typeof(IVehicle), typeof(Car));
        sc.AddScoped<Driver>();
        var sp = sc.BuildServiceProvider();
        // ===上面是程序开始时的注册
        // 向sp中拿instance, 并且会自动在sc中找到IVehicle的类型并创建instance，也就是Car
        // 同时Car可以修改为Truck, T98.....
        // 用注册的类型创建的instance注入到Driver的构造器中去了
        var driver = sp.GetService<Driver>();
        driver.Drive();
    }
}
```

### reflection松开耦合

这种特别松的耦合用于插件式编程;
>插件式编程: 不与主体程序一起编译，但是同主体程序一起工作; 方便生成生态圈;

一般主体程序会发布包含程序开发接口(API)的程序开发包(SDK)
>API: Application Programming Interface; SDK: Software Development Kit

>API里面不只是Interface, 可能是一组函数、class、interface;

既然有了Reflection为何还要用SDK里面的API:主要是用SDK中的api来约束开发者的开发;

```csharp
// 主体程序
using System;
using System.Collections.Generic;
using System.IO;
using System.Runtime.Loader;

namespace BabyStoller {
    class Program {
        static void Main(string[] args) {
            // 从程序同目录的Animails文件夹加载第三方的插件
//            Console.WriteLine(AppContext.BaseDirectory);
            var foler = Path.Combine(AppContext.BaseDirectory, "Animals");
            var files = Directory.GetFiles(foler);
            var animalTypes = new List<Type>();
            foreach (var file in files) {
                // 将所有file中的动物类型加入animalType的list
                var assembly = AssemblyLoadContext.Default.LoadFromAssemblyPath(file);
                var types = assembly.GetTypes();
                foreach (var type in types) {
                    if (type.GetMethod("Voice") != null) {
                        animalTypes.Add(type);
                    }
                }
            }

            // 开始玩游戏
            while (true) {
                for (int i = 0; i < animalTypes.Count; i++) Console.WriteLine($"{i + 1}: {animalTypes[i].Name}");
                Console.WriteLine("=================");
                Console.WriteLine("Choose Animal:");
                int index = int.Parse(Console.ReadLine());
                if (index > animalTypes.Count || index < 1) {
                    Console.WriteLine("No such an animal. Try again!");
                    continue;
                }

                Console.WriteLine("Choose Voice times:");
                int times = int.Parse(Console.ReadLine());
                var t = animalTypes[index - 1];
                var m = t.GetMethod("Voice");
                var o = Activator.CreateInstance(t);
                // 参数必须是object[]
                m.Invoke(o, new object[] {times});
            }
        }
    }
}
```

```csharp
// 下面两个插件dll, 放到/bin/Animals文件夹中，就可以读取了
// Animals.lib
// Cat.cs
using System;

namespace Animals.lib {
    public class Cat {
        public void Voice(int times) {
            for (int i = 0; i < times; i++) {
                Console.WriteLine("Miao...");
            }
        }
    }
}
```

```csharp
//Animals.lib
//Sheep.cs
using System;

namespace Animals.lib {
    public class Sheep {
        public void Voice(int times) {
            for (int i = 0; i < times; i++) {
                Console.WriteLine("Mie...");
            }
        }
    }
}
```

```csharp
//Animails.lib2
//Dog.cs
using System;

namespace Animals.lib2 {
    public class Dog {
        public void Voice(int times) {
            for (int i = 0; i < times; i++) {
                Console.WriteLine("wang...");
            }
        }
    }
}
```

```csharp
//Animals.lib2
//Cow.cs
using System;

namespace Animals.lib2 {
    public class Cow {
        public void Voice(int times) {
            for (int i = 0; i < times; i++) {
                Console.WriteLine("Moo...");
            }
        }
    }
}
```

然而，下面两个插件没有被规范好，如果`Voice`写成`voice`就不能被主题程序调用;所以需要sdk

在主体程序添加`BabyStroller.sdk`project,并在主体程序`add reference`(project); build sdk; 然后再第三方插件中add reference(dll);

其中Attribute的作用: 在使用Reflection的时候，是应该调用还是放弃;

```csharp
// BabyStroller.sdk/IAnimal.cs
namespace BabyStroller.sdk {
    public interface IAnimal {
        void Voice(int times);
    }
}
```

```csharp
//BabyStroller.sdk/UnfinishedAttribute.cs
using System;

namespace BabyStroller.sdk {
    public class UnfinishedAttribute : Attribute { }
}
```

```csharp
// Animals.lib/Cat.cs
using System;
using BabyStroller.sdk;

namespace Animals.lib {
    [Unfinished]
    public class Cat : IAnimal {
        public void Voice(int times) {
            for (int i = 0; i < times; i++) {
                Console.WriteLine("Miao...");
            }
        }
    }
}
```

```csharp
// Animals.lib/Sheep.cs
using System;
using BabyStroller.sdk;

namespace Animals.lib {
    public class Sheep : IAnimal {
        public void Voice(int times) {
            for (int i = 0; i < times; i++) {
                Console.WriteLine("Mie...");
            }
        }
    }
}
```

```csharp
// Animals.lib2/Dog.cs
using System;
using BabyStroller.sdk;

namespace Animals.lib2 {
    public class Dog:IAnimal {
        public void Voice(int times) {
            for (int i = 0; i < times; i++) {
                Console.WriteLine("wang...");
            }
        }
    }
}
```

```csharp
// Animals.lib2/Cow.cs
using System;
using BabyStroller.sdk;

namespace Animals.lib2 {
    // 采用Attribute表示没有开发完毕
    [Unfinished]
    public class Cow:IAnimal {
        public void Voice(int times) {
            for (int i = 0; i < times; i++) {
                Console.WriteLine("Moo...");
            }
        }
    }
}
```

```csharp
// 修改主体程序
// BabyStroller/Program.cs
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Reflection;
using System.Runtime.Loader;
using BabyStroller.sdk;

namespace BabyStoller {
    class Program {
        static void Main(string[] args) {
            // 从程序同目录的Animails文件夹加载第三方的插件
//            Console.WriteLine(AppContext.BaseDirectory);
            var foler = Path.Combine(AppContext.BaseDirectory, "Animals");
            var files = Directory.GetFiles(foler);
            var animalTypes = new List<Type>();
            foreach (var file in files) {
                // 将所有file中的动物类型加入animalType的list
                var assembly = AssemblyLoadContext.Default.LoadFromAssemblyPath(file);
                var types = assembly.GetTypes();
                foreach (var type in types) {
                    if (type.GetInterfaces().Contains(typeof(IAnimal))) {
                        // false表示修饰这个class不是从外层继承的，而是直接修饰的
                        var isUnfinished = type.GetCustomAttributes(false)
                            .Any(a => a.GetType() == typeof(UnfinishedAttribute));
                        if (isUnfinished) continue; // 过滤unfinished修饰的
                        animalTypes.Add(type);
                    }
                }
            }

            // 开始玩游戏
            while (true) {
                for (int i = 0; i < animalTypes.Count; i++) Console.WriteLine($"{i + 1}: {animalTypes[i].Name}");
                Console.WriteLine("=================");
                Console.WriteLine("Choose Animal:");
                int index = int.Parse(Console.ReadLine());
                if (index > animalTypes.Count || index < 1) {
                    Console.WriteLine("No such an animal. Try again!");
                    continue;
                }

                Console.WriteLine("Choose Voice times:");
                int times = int.Parse(Console.ReadLine());
                var t = animalTypes[index - 1];
                var o = Activator.CreateInstance(t);
                // 参数必须是object[]
                var a = o as IAnimal;
                a.Voice(times);
            }
        }
    }
}
```