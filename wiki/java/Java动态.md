[toc]

### 写在之前

`Java` 中的多态是三大特性中的一个比较重要的特性，之前有很多经典书籍对该概念做了一些阐述，本人尝试从一个菜鸡的角度谈一谈自己的理解，如有不妥，还望再次交流。

《 Java 编程思想》中用了一个极度简单的话形容多态。



> 多态通过分离做什么和怎么做来达到灵活编程的目的。

初学者难免觉得晦涩难懂，但是却极为经典。本文会尝试从**是什么，怎么玩**，通过举例子来分析多态。



### 为什么存在多态问题



多态存在是有一定条件的，主要有三个核心条件：

1. 类之间要有继承关系。【无继承，何谈多态】
2. 子类要重写父类的方法。【子类要有自己的思想】
3. 父类引用指向子类对象。【这一点尤其重要，稍后加以阐述】

首先从例子出发，讲讲多态怎么玩？



**定义一个父类，简单粗暴就叫做 Father 类吧**
有两个属性：
一个成员变量 age，一个静态成员变量 name。
三个方法
一个静态方法、两个非静态方法

```java
public class Father {

    int age = 68;

    static String name = "yourFather";

    public void playChess () {
        System.out.println("父亲下象棋……");
    }

    public void eat () {
        System.out.println("父亲吃饭……");
    }

    public static void sleep () {
        System.out.println("父亲睡觉……");
    }
}
```



**定义一个子类，Son**

有三个属性：
两个从父类继承的属性：age、name，一个子类独有的属性 hight。
三个方法：
一个从父类继承的方法（eat）、两个子类独有的方法 playGame()、sleep()

```java
public class Son extends Father {

    int age = 68;

    static String name = "小明";

    Integer hight = 178;

    public void playGame () {
        System.out.println("小明打游戏……");
    }

    @Override
    public void eat () {
        System.out.println("小明吃饭……");
    }

    public static void sleep () {
        System.out.println("小明睡觉……");
    }
}
```

测试类

```java
public class Test {

    public static void main(String[] args) {
        // 记住，这一行是关键
        Father father = new Son();
        father.playChess();     
        father.eat();
        father.sleep(); // 这里默认编译不规范，静态方法建议用 类.方法 () 调用
        System.out.println(father.age);
        System.out.println(father.name);
    }
}
```

上面的实例代码中，已经很好了满足了刚才说的几个条件，前面两点很好理解，下面说说第三点：**父类引用指向子类对象**，这句话如果要理解还要对象的内存结构说起。

![多态内存结构 ](https://img-blog.csdnimg.cn/2021020110282089.png)
`Father father = new Son();`如上图所示，栈中的引用`father`,指向了位于堆中的对象`new Son()`，这就是上面所说的父类引用指向子类对象。

可以手动运行一下方法，看看下面的类运行结果：



> 父亲下象棋……【father.playChess();是父类方法独有的方法，子类没有重写，因此调用的是父类的方法】
> 小明吃饭……【father.eat();由于子类重写了，因此默认调用子类的方法】
> 父亲睡觉……【sleep() 是一个静态方法，实现了自己的逻辑，默认调用父类的方法】
> 68      【直接采用父类的属性】
> yourFather      【直接采用父类属性】



### 参考《Java 编程思想》及实践得到如下结果

**对于成员变量**
编译看左边，运行看左边。（看父类的属性）
**对于成员方法**
编译看左边，运行看右边。（这就是多态的核心，只有运行的时候才知道调用的是哪个类，这就是为什么编程思想中说"多态决定的是**怎么做**"）
**对于静态方法**
编译看左边 (父类)，运行看左边 (父类)。（取决于父类怎么运行的，其实本质上是没有重写父类方法的）

如果强制将父类引用指向子类，则所有的调用方法和属性都是调用子类的，本质是在内存的栈引用（`son`引用）指向子类对象。

内存结构如下：
![强制转化之后内存 ](https://img-blog.csdnimg.cn/2021020111321377.png)


```java
public class Test {

    public static void main(String[] args) {
        // 记住，这一行是关键
        Father father = new Son();
        Son son = (Son) father;
        son.eat();
        son.playGame();
        son.playChess();
        System.out.println(son.age);
        System.out.println(son.hight);
        System.out.println(son.name);
    }
}
```



> 输出结果如下：
> 小明吃饭……
> 小明打游戏……
> 父亲下象棋……【子类没有该方法，只能用父类的方法】
> 20
> 178
> 小明



看到这里，可以发现多态的好处，可以用两个引用指向同一个对象，减少了重复创建对象的过程，很好的解决**对象泛滥**的问题，有利于方法的横向扩展。



### 总结

关于多态，其核心主要在于什么是**父类引用指向子类对象**，核心是执行时是依据哪个对象重写的逻辑执行。【课本通俗的称为动态绑定】


