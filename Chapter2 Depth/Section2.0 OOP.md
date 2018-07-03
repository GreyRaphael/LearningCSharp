# C# OOP

- [C# OOP](#c-oop)
    - [Csharp fps games](#csharp-fps-games)
    - [java fps game](#java-fps-game)
    - [kotlin fps](#kotlin-fps)

## Csharp fps games

```csharp
//Program.cs
using System;

namespace ConsoleApp1
{
    class Program
    {
        static void Main(string[] args)
        {
            var player1 = new Player("grey");
            var enermy1 = new Player("zoombie");
            var ak47 = new Gun("AK47");
            var clip1 = new Clip(20);
            for (int i = 0; i < 12; i++) {
                var bullet = new Bullet(33);
                player1.reloadBullet(clip1, bullet);
            }
            player1.reloadClip(ak47, clip1);
            player1.pickUp(ak47);
            for (int i = 0; i < 6; i++) {
                Console.WriteLine($"{player1}|{enermy1}");
                player1.shoot(enermy1);
                System.Threading.Thread.Sleep(1000);
            }
        }
    }
}
```

```csharp
//Player.cs
using System;
using System.Collections.Generic;
using System.Text;

namespace ConsoleApp1
{
    class Player {
        public string Name { get; set; }
        public int HP { get; set; }
        public Gun MyGun { get; set; }

        public Player(string name_temp) {
            this.Name = name_temp;
            this.MyGun = null;
            this.HP = 100;
        }

        internal void reloadBullet(Clip clip, Bullet bullet) {
            //bullet2clip
            clip.saveBullet(bullet);
        }

        internal void reloadClip(Gun gun, Clip clip) {
            //clip2gun
            gun.saveClip(clip);
        }
        internal void pickUp(Gun gun) {
            //player pickup gun
            this.MyGun = gun;
        }
        internal void shoot(Player player) {
            this.MyGun.fire(player);
        }
        internal void dropHP(int damage_temp) {
            this.HP -= damage_temp;
        }
        public override string ToString() {
            if (this.HP > 0) {
                if (this.MyGun != null) {
                    return $"{this.Name}'s HP={this.HP},gun={this.MyGun.ToString()}";
                }
                else {
                    return $"{this.Name}'s HP={this.HP},without gun";
                }
            }
            else {
                return $"{this.Name} is dead!";
            }
        }
    }
}
```

```csharp
//Gun.cs
using System;
using System.Collections.Generic;
using System.Text;

namespace ConsoleApp1
{
    class Gun {
        public string Name { get; set; }
        private Clip clip;

        public Gun(string name) {
            this.Name = name;
            this.clip = null;
        }
        internal void saveClip(Clip clip) {
            this.clip = clip;
        }

        internal void fire(Player player) {
            Bullet bullet_temp = this.clip.popBullet();
            if (bullet_temp != null) {
                bullet_temp.hit(player);
            }
            else {
                Console.WriteLine("no bullet left in clip");
            }
        }
        public override string ToString() {
            if (this.clip != null) {
                return $"{this.Name},{this.clip.ToString()}";
            }
            else {
                return $"{this.Name} without clip";
            }
        }
    }
}
```

```csharp
//Clip.cs
using System;
using System.Collections.Generic;
using System.Text;

namespace ConsoleApp1
{
    class Clip {
        public int MaxBullets { get; set; }
        public Stack<Bullet> BulletStack { get; set; }
        public Clip(int max_bullets) {
            this.MaxBullets = max_bullets;
            this.BulletStack = new Stack<Bullet>();
        }
        internal void saveBullet(Bullet bullet) {
            this.BulletStack.Push(bullet);
        }

        internal Bullet popBullet() {
            if (this.BulletStack.Count != 0) {
                return this.BulletStack.Pop();
            }
            else {
                return null;
            }
        }
        public override string ToString() {
            return $"clip={this.BulletStack.Count}/{this.MaxBullets}";
        }
    }
}
```

```csharp
//Bullet.cs
using System;
using System.Collections.Generic;
using System.Text;

namespace ConsoleApp1
{
    class Bullet {
        public int Damage { get; set; }
        public Bullet(int damage) {
            this.Damage = damage;
        }
        internal void hit(Player player) {
            player.dropHP(this.Damage);
        }
    }

}
```

```bash
#output
grey's HP=100,gun=AK47,clip=12/20|zoombie's HP=100,without gun
grey's HP=100,gun=AK47,clip=11/20|zoombie's HP=67,without gun
grey's HP=100,gun=AK47,clip=10/20|zoombie's HP=34,without gun
grey's HP=100,gun=AK47,clip=9/20|zoombie's HP=1,without gun
grey's HP=100,gun=AK47,clip=8/20|zoombie is dead!
grey's HP=100,gun=AK47,clip=7/20|zoombie is dead!
```

## java fps game

```bash
src/com.company/
                Bullet.java
                Clip.java
                Gun.java
                Player.java
                main.java
```

```java
//main.java
package com.company;

public class Main {

    public static void main(String[] args) {
	// write your code here
        Player player1=new Player("grey");
        Player enermy1=new Player("zombie");
        Gun ak47=new Gun("AK47");
        Clip clip1=new Clip(20);
        for (int i = 0; i < 12; i++) {
            Bullet bullet=new Bullet(33);
            player1.reloadBullet(clip1,bullet);
        }
        player1.reloadClip(ak47,clip1);
        player1.pickUp(ak47);
        for (int i = 0; i < 6; i++) {
            System.out.println(String.format("%s|%s",player1,enermy1));
            player1.shoot(enermy1);
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }

    }
}
```

```java
//Player.java
package com.company;

public class Player {
    public String Name;
    public int HP;
    public Gun gun;

    public Player(String name){
        this.Name=name;
        this.gun=null;
        this.HP=100;
    }

    public void reloadBullet(Clip clip,Bullet bullet){
        //bullet2clip
        clip.saveBullet(bullet);
    }

    public void reloadClip(Gun gun,Clip clip){
        gun.saveClip(clip);
    }

    public void pickUp(Gun gun){
        this.gun=gun;
    }

    public void shoot(Player player){
        this.gun.fire(player);
    }

    public void dropHP(int damage){
        this.HP-=damage;
    }

    @Override
    public String toString() {
        if(this.HP>0){
            if(this.gun!=null){
                return String.format("%s's HP=%d,gun=%s",this.Name,this.HP,this.gun);
            }
            else {
                return String.format("%s's HP=%d,without gun",this.Name,this.HP);
            }
        }
        else {
            return String.format("%s is dead",this.Name);
        }
    }
}
```

```java
//Gun.java
package com.company;

public class Gun {
    public String Name;
    private Clip clip;
    public Gun(String name){
        this.Name=name;
        this.clip=null;
    }
    public void saveClip(Clip clip) {
        this.clip=clip;
    }

    public void fire(Player player) {
        Bullet bullet=this.clip.popBullet();
        if (bullet!=null){
            bullet.hit(player);
        }
        else {
            System.out.println("no bullet left in clip");
        }
    }

    @Override
    public String toString() {
        if (this.clip!=null){
            return String.format("%s,%s",this.Name,this.clip);
        }
        else {
            return String.format("%s,without clip",this.Name);
        }
    }
}
```

```java
//Clip.java
package com.company;

import java.util.Stack;

public class Clip {
    public int MaxBullets;
    public Stack<Bullet> BulletStack;

    public Clip(int maxbullets) {
        this.MaxBullets = maxbullets;
        this.BulletStack = new Stack<Bullet>();
    }

    public void saveBullet(Bullet bullet) {
        this.BulletStack.push(bullet);
    }

    public Bullet popBullet() {
        if (this.BulletStack.size()!=0){
            return this.BulletStack.pop();
        }
        else {
            return null;
        }
    }

    @Override
    public String toString() {
        return String.format("clip=%d/%d",this.BulletStack.size(),this.MaxBullets);
    }
}
```

```java
//Bullet.java
package com.company;

public class Bullet {
    public int Damage;
    public Bullet(int damage) {
        this.Damage=damage;
    }

    public void hit(Player player) {
        player.dropHP(this.Damage);
    }
}
```

```bash
#output
grey's HP=100,gun=AK47,clip=12/20|zoombie's HP=100,without gun
grey's HP=100,gun=AK47,clip=11/20|zoombie's HP=67,without gun
grey's HP=100,gun=AK47,clip=10/20|zoombie's HP=34,without gun
grey's HP=100,gun=AK47,clip=9/20|zoombie's HP=1,without gun
grey's HP=100,gun=AK47,clip=8/20|zoombie is dead
grey's HP=100,gun=AK47,clip=7/20|zoombie is dead
```

## kotlin fps

```bash
src/
    main.kt
    player.kt
    gun.kt
    clip.kt
    bullet.kt
```

```kotlin
//main.kt
fun main(args: Array<String>) {
    var player1 = Player("grey")
    var enermy1 = Player("zombie")
    var ak47 = Gun("AK47")
    var clip1 = Clip(20)
    for (i in 0..11) {
        var bullet = Bullet(33)
        player1.ReloadBullet(clip1, bullet)
    }
    player1.ReloadClip(ak47, clip1)
    player1.PickUp(ak47)
    for (i in 0..5) {
        println("%s|%s".format(player1, enermy1))
        player1.Shoot(enermy1)
        Thread.sleep(1000)
    }
}
```

```kotlin
//player.kt
class Player constructor(name: String) {
    var name: String = name
    var HP: Int = 100
    var gun: Gun? = null

    fun ReloadBullet(clip: Clip, bullet: Bullet) {
        clip.SaveBullet(bullet)
    }

    fun ReloadClip(gun: Gun, clip: Clip) {
        gun.SaveClip(clip)
    }

    fun PickUp(gun: Gun) {
        this.gun = gun
    }

    fun Shoot(enermy: Player) {
        this.gun?.Fire(enermy)
    }

    fun DropHP(damage: Int) {
        this.HP -= damage
    }

    override fun toString(): String {
        if (this.HP > 0) {
            if (this.gun != null) {
                return "%s's HP=%d,gun=%s".format(this.name, this.HP, this.gun)
            } else {
                return "%s's HP=%d,without gun".format(this.name, this.HP)
            }
        } else {
            return "%s is dead!".format(this.name)
        }
    }
}
```

```kotlin
//gun.kt
class Gun constructor(name: String) {
    var name: String = name
    var clip: Clip? = null

    fun SaveClip(clip: Clip) {
        this.clip = clip
    }

    fun Fire(enermy: Player) {
        var bullet: Bullet? = this.clip?.PopBullet();
        if (bullet != null) {
            bullet.Hit(enermy)
        } else {
            println("not bullet left in clip")
        }
    }

    override fun toString(): String {
        if (this.clip != null) {
            return "%s,%s".format(this.name, this.clip)
        } else {
            return "%s,without clip".format(this.name)
        }
    }
}
```

```kotlin
//clip.kt
import java.util.*

class Clip constructor(max_bullets: Int) {
    var max_bullets: Int = max_bullets
    var BulletStack = Stack<Bullet>()

    fun SaveBullet(bullet: Bullet) {
        this.BulletStack.push(bullet)
    }

    fun PopBullet(): Bullet? {
        if (this.BulletStack.size != 0) {
            return this.BulletStack.pop()
        } else {
            return null
        }
    }

    override fun toString(): String {
        return "clip=%d/%d".format(this.BulletStack.size, this.max_bullets)
    }
}
```

```kotlin
//bullet.kt
class Bullet constructor(damage: Int) {
    var damage: Int = damage
    fun Hit(player: Player): Unit {
        player.DropHP(this.damage)
    }
}
```

```bash
#output
grey's HP=100,gun=AK47,clip=12/20|zombie's HP=100,without gun
grey's HP=100,gun=AK47,clip=11/20|zombie's HP=67,without gun
grey's HP=100,gun=AK47,clip=10/20|zombie's HP=34,without gun
grey's HP=100,gun=AK47,clip=9/20|zombie's HP=1,without gun
grey's HP=100,gun=AK47,clip=8/20|zombie is dead!
grey's HP=100,gun=AK47,clip=7/20|zombie is dead!
```