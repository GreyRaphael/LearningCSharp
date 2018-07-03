# C# Method's definition, calling, debug

- [C# Method's definition, calling, debug](#c-methods-definition-calling-debug)
    - [method define](#method-define)
    - [Constructor](#constructor)
    - [Overload](#overload)
    - [Debug](#debug)
        - [方法的调用和stack](#%E6%96%B9%E6%B3%95%E7%9A%84%E8%B0%83%E7%94%A8%E5%92%8Cstack)

## method define

C# Main包裹在`Program`类中的,而且Main开头大写

方法(Method,不是function)永远都是类或者结构体的成员:

- C#,Java(纯面向对象语言)中函数不可能独立于类（或者结构体）之外，只有作为类（或者结构体）的成员时，才被称为方法，C++是可以将函数放在class的外面，称为“全局函数”
- 一个class,最基本的2个：字段与方法（成员变量(Property)与成员函数(Method)）

需要方法或者函数：

- 进行一次封装，隐藏复杂的逻辑
- 大算法分解为小算法(自顶向下，逐步求精)
- 复用

```csharp
using System;

namespace ReuseSample {
    class Program {
        static void Main(string[] args) {
            Calculator c = new Calculator();
            Console.WriteLine(c.GetCircleArea(10));
            Console.WriteLine(c.GetCylinderVolume(10, 10));
            Console.WriteLine(c.GetConeVolume(10, 10));
        }
    }

    class Calculator {
        public double GetCircleArea(double r) {
            return Math.PI * r * r;

        }
        public double GetCylinderVolume(double r, double h) {
            return GetCircleArea(r) * h;

        }
        public double GetConeVolume(double r, double h) {
            return GetCylinderVolume(r, h) / 3;
        }
    }
}
```

C#方法声明定义不分家

- 形参：formal-parameter(Parameter)
- 实参: Argument

argument是值得角色，parameter是变量的角色；

C#中`()`方法调用操作符

## Constructor

特殊的成员函数；如果没写，编译器准备一个默认的ctor；

狭义的构造器指的是:instance constructor

必须是public，因为一般在别人的class中使用；没有返回值(连void也没有)

`string`是一个class，所以string在内存中占4Bytes,存储的是heap上的地址:

默认构造器将string全部刷成0，也就是`null`

- 如果是局部变量的string, 那么string在stack上面，指向heap的内容
- 如果是class中的string，那么string在heap上，指向heap的内容

## Overload

方法的名字能够一样，方法的签名(method-signature)不一样，方法的签名不包含返回类型

方法签名:

- 方法的名称
- 类型形参个数(泛型中的`<T>`)
- 从左往右每一个形参的类型和种类(值、ref、out)

overload决策，不用选择，会自动根据实参调整调用哪一个重载方法

```csharp
using System;

namespace ConsoleApp6
{
    class Program
    {
        static void Main(string[] args)
        {
            Calculator my1 = new Calculator();
            Console.WriteLine(my1.myAdd(10,10));
            Console.WriteLine(my1.myAdd(10.1,10.3));
        }
    }

    class Calculator {
        public T myAdd<T>(T a,T b) {
            dynamic x = a;
            dynamic y = b;
            return (T)(x + y);
        }
    }
}
```

## Debug

用求圆锥体积的例子，反复练习step-in,step-over,step-out

VS的CallStack窗口，可以得知是哪个函数调用了现在断点所在的函数；step-out比call-back窗口强大

主调函数：caller；被调函数: callee;

### 方法的调用和stack

stack从高字节向低字节增长；

stack-frame:方法被调用的时候，它们在stack内存中的布局(调用的时候压栈出栈)

变量的压栈从左往右；

return副本机制: return之前的结果存在CPU的寄存器之中，return之后，结果刷入栈中，再之后，清空方法的栈