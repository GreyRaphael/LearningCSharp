# Generic,Partial Class,Enum,Struct

## Generic(泛型)

- 为什么需要泛型：避免成员膨胀或类型膨胀
- 正交性：泛型类型（类/接口/委托...）、泛型成员：（属性/方法/字段...)
- 类型方法方法的参数推断
- 泛型与委托，lambda表达式

### 类型膨胀和成员膨胀

```csharp
class Apple {
    public string Color { get; set; }
}

class Book {
    public string Name { get; set; }
}

// 成员膨胀
// 假设一家商店有多种商品，每种商品需要用特定的盒子来”装“
// 当商品类型不断增加就必须添加相应的盒子
// 这会造成类型膨胀
class AppleBox {
    public Apple Cargo { get; set; }
}

class BookBox {
    public Book Cargo { get; set; }
}

// 成员膨胀
// 同样的如果使用相同的盒子”装“不同的商品
// 每增加一种商品就需要在盒子（Box）类中加入一种商品
// 会造成盒子（Box）类的成员膨胀
class Box {
    public Apple Apple { get; set; }
    public Book Book { get; set; }
}
```

```csharp
// 使用泛型

class Apple {
    public string Color { get; set; }
}

class Book {
    public string Name { get; set; }
}

class Box<TCargo> {
    public TCargo Cargo { get; set; }
}

// 使用泛型类
Apple apple = new Apple(){ Color = "red"; };
Box<Apple> appleBox = new Box<Apple>();
Console.WriteLine(appleBox.Cargo.Color); // red

Book book = new Book(){ Name = "Book Name"; };
Box<Book> bookBox = new Box<Book>();
Console.WriteLine(bookBox.Cargo.Name); // Book Name
```

```csharp
// 泛型接口
// 第一种方式
// 如果一个类实现泛型接口那么这个类也是泛型的

interface IUnique<TId> {
    TId ID { get; set; }
}

class Student<TId>: IUnique<TId> {
    public TId Id { get; set; }
    public string Name { get; set; }
}

// 使用
Student<int> stu = new Student<int>();
stu.id = 10001;
```

```csharp
// 泛型接口
// 第二种方式
// 实现特化的泛型接口，实现它的类可以不是泛型类

interface IUnique<TId> {
    TId ID { get; set; }
}

class Student: IUnique<uint> {
    public uint Id { get; set; }
    public string Name { get; set; }
}

// 使用
Student stu = new Student();
stu.id = 10001;
```

## 集合

```csharp
using System.Collections.Generic;

IList<int> list = new List<int>();
for (int i = 0; i < 10; i ++) {
    list.Add(i);
}

IDictionary<int, string> dict = new Dictionary<int, string>();
dict[1] = "a";
dict[2] = "b";
```

## partial class

- 减少类的派生
- 将一个类拆分成多个类

## Enum(枚举)

- 人为限定取值范围
- 整数值对应
- 比较位用法

```csharp
// Level 的取值只能是 Level 枚举类型中限定的几种
// 避免不必要的错误
// 枚举类型的值可手动设定
class Person {
    public string Id { get; set; }
    public string Name { get; set; }
    public Level Level { get; set; }
    public Skill Skill { get; set; }
}

enum Level {
    Employee,// 0
    Manager,// 1
    Bose,// 2
}

enum Skill {
    Drive = 1,      // 00000000000000000000000000000001
    Cook = 2,       // 00000000000000000000000000000010
    Program = 4,    // 00000000000000000000000000000100
    Teach = 8,      // 00000000000000000000000000001000
}

Console.WriteLine(Level.Employee); // Employee
Console.WriteLine(Level.Manager); // Manager
Console.WriteLine(Level.Bose); // Bose

Console.WriteLine((int)Level.Employee); // 0
Console.WriteLine((int)Level.Manager); // 1
Console.WriteLine((int)Level.Bose); // 2

Person p = new Person();
p.Skill = Skill.Drive | Skill.Cook | Skill.Program | Skill.Teach;
Console.WriteLine((int)p.Skill); // 15 -> (2)00000000000000000000000000001111
Console.WriteLine(p.Skill & Skill.Tech == Skill.Teach); // True
```

## Struct(结构体)

- 值类型，可装箱/拆箱
- 可实现接口，不能派生自类/结构体
- 不能有显式无参构造器

```csharp
struct Student {
    public int Id { get; set; }
    public string Name { get; set; }
}

// stu2 的值的改变不会影响 stu1，它们是不同的 
Student stu1 = new Student(){ Id = 101, Name = "A" };
Student stu2 = stu1;
stu2.id = 102;
stu2.Name = "N";

Console.WriteLine($"{Stu1.Id}, Name:{stu1.Name}");// 101, Name:A
Console.WriteLine($"{Stu2.Id}, Name:{stu2.Name}");// 102, Name:B
```