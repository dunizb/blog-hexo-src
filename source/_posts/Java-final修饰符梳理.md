---
title: Java-final修饰符梳理
date: 2015-07-18 22:27:20
categories:
- 服务端开发
- Java
tags:
- javase
- 总结
---

final关键字可用于修饰方法、类和变量，表示它修饰的类、方法和变量不可改变。final修饰变量时，表示该变量一旦获得了初始值就不可改变，final既可以修饰成员变量，也可以修饰局部变量、形参。final修饰的成员变量不可被改变，一旦获得了初始值，该final变量的值就不能被重新赋值。
<!-- more -->
## 一、final成员变量

成员变量时随类初始化或对象初始化而初始化的，当类初始化时，系统会为该类的类Field分配内存，并分配默认值；当创建对象时，系统会为该对象的实例Field分配内存并分配默认初始值。也就是说，当执行静态初始化块、构造器时可以对类Field赋初始值；当执行普通初始化块、构造器时可以对实例Field赋初始值。因此，成员变量的初始值可以在定义改变了时指定默认值，也可以在初始化块、构造器中指定初始值。

对于final修饰的成员变量而言，一但有了初始值，就不能被重新赋值，如果既没有在定义成员变量时指定初始值，也没在初始化块、构造器中为成员变量指定初始值，那么这些成员变量的值是系统默认分配的`0`、`\u000`、`false`或`null`，这些成员变量也就全失去了存在的意义。因此Java语法规定：**final修饰的成员变量必须由程序员显示的指定初始值。**

归纳起来，final修饰的类Field、实例Field能指定初始值的地方如下：
1. 类Field：必须在静态初始化块中或声明该File的时指定初始化。
2. 实例File的：必须在非静态初始化块、声明该File的或构造器中指定初始值。

下面程序演示了final修饰成员变量的效果，详细示范了final修饰成员变量的各种具体情况。
```java
class FinalVarTest {
    //定义成员变量时指定默认值，合法
    final int a = 6;
    //下面变量将在构造器中或初始化块中分配初始值
    final String str;
    final int c;
    final static double d;
    //即没有指定默认值，也没用在初始化块中或构造器中指定初始值
    //下面定义char Filed是不合法的
    //final char ch;
    //初始化块，可对没有指定默认值的实例Filed指定初始值
    {
        //在初始化块中为实例Field指定初始值，合法
        str = "hello";
        //定义a Field时已经指定了默认值，不能为a重新赋值，下面语句非法
        a = 9;
    }
    
    //静态初始化块，可对没有指定默认值的类Field指定初始值
    static{
        //在静态初始化块中为类指定初始值，合法
        d = 5.6;
    }
    
    //构造器，可对既没有指定默认值，又没有在初始化块中指定初始值的实例Field指定初始值
    public FinalVarTest(){
        //如果初始化块中对str指定了初始值
        //则构造器中不能对final变量重新赋值，下面语句非法。
        //str = "Java";
        c = 5;
    }
    public void changeFinal(){
        //普通方法不能为final修饰的成员变量赋值
        //d = 1.2;
        //不能在普通方法中为final成员变量指定初始值
        //ch = 'a';
    }
    public static void main(String[] args) {
        FinalVarTest ft = new FinalVarTest();
        System.out.println(ft.a);
        System.out.println(ft.c);
        System.out.println(ft.d);
    }
}
```

## 二、final局部变量

系统不会对局部变量进行初始化，局部变量必须由程序员显示初始化。因此，使用final修饰的局部变量时，既可以在定义时指定默认值，也可以不指定默认值。

规则：如果final修饰的局部变量在定义时没有指定默认值，则可以在后面的代码中对final变量赋初始值，但只能一次，不能重复赋值；如果final修饰的局部变量定义在定义时已经指定默认值，则后面的代码中不能再次对该变量赋值。下面程序示范了final修饰局部变量、形参的情形。
```java
class FinalLocalVarTest {
    public void test(final int a){
        //不能对final修饰的形参赋值，下面语句非法
        //a = 5;
    }
    public static void main(String[] args) {
        //定义final局部变量时指定默认值，则str变量无法重新赋值
        final String str = "hello";
        //下面语句非法
        //str = "Java";
        //定义final局部变量没有指定默认值，则d变量可以被赋值一次
        final double d;
        //第一次赋值，成功
        d = 5.6;
        //对final变量重复赋值，下面语句非法
        //d = 3.4;
    }
}
```

## 三、final修饰基本类型变量和引用类型变量的区别

当使用final修饰基本类型变量时，不能对基本类型变量重新赋值，因此基本类型变量不能被改变。但对于引用类型变量而言，它保存的仅仅是一个引用，final只保证这个引用类型变量所引用的地址不会改变，即一直引用同一个对象，但这个对象完全可以发生改变。

## 四、可执行“宏替换”的final变量

对于一个final变量来说，不管它是类Field、实例Field，还是局部变量，只要该变量满足3个条件，这个final变量就不再是一个变量，而是相当于一个直接量：
1. 使用final修饰符修饰；
2. 在定义该final变量时指定了初始值；
3. 该初始值可以在编译时就被确定下来。

看如下程序：
```java
class FinalLocalTest {
    public static void main(String[] args) {
        //定义一个普通局部变量
        final int a =5;
        System.out.println(a);
    }
}
```

对于上面程序来说，变量a其实根本不存在，当程序执行`System.out.println(a);`代码时，实际转换为执行`System.out.prinltn(5)`。“宏变量”，编译器会把程序中所有用到该变量的地方直接替换成该变量的值。

## 五、final方法

final修饰的方法不可被重写，如果处于某种原因，不希望子类重写父类的某个方法，则可以使用final修饰该方法。

Java提供的Object类里就有一个final方法：`getClass()`。因为Java不希望任何类重写该方法，所以使用final把这个方法密封起来。但对于该类提供的`toString()`和`equals()`方法，都允许子类重写，因此没有使用final修饰它们。

下面程序试图重写final方法，将会引发编译错误。
```java
class FinalMethodTest {
    public final void test(){};
}
class Sub extends FinalMethodTest{
    //下面方法定义将出现编译错误，不能重写final方法
    public void test(){};
}
```

对于一个private方法，因为它仅在当前类中可见，其子类无法访问该方法，所以子类无法重写该方法——如果子类定义一个与父类private方法有相同的方法名、相同形参列表、相同返回值类的方法，也不是方法重写，只是重新定义了一个新方法。因此。即使final修饰一个private访问权限的方法，依然可以在其子类中定义与该方法具有相同方法名、相同形参列表、相同返回值类的方法。

下面程序示范了如何在子类中“重写”父类的private final方法:
```java
class PrivateFinalMethodTest {
    public final void test(){};
}
class Sub extends PrivateFinalMethodTest{
    //下面方法定义不会出现任何问题
    public void test(){};
}
```

final修饰的方法仅仅是不能被重写，并不是不能被重载，因此，下面程序完全没有问题：
```java
class FinalOverload {
    public final void test(){};
    public final void test(String arg){};
}
```

## 六、final类

final修饰的类不可有子类，例如`java.lang.Math`类就是一个final类，它不可以有子类。

当子类继承父类时，可以访问到父类内部数据，并可以通过重写父类方法来改变父类方法的实现细节，这可能导致一些不安全的因素。为了保证某个类不可被继承，则可以使用final修饰这个类。下面代码示范了final修饰的类不可被继承。
```java
public final class FinalOverload {}
//下面的类定义将出现编译错误
class Sub extends FinalClass{}
```

## 七、不可变类

不可变类的意思是，创建该类的实例后，该实例的Field是不可以被改变的。Java提供的8个包装类和Java.lang.String类都是不可变类，当创建它们的实例后，其实例的Field不可改变。

如果需要创建自定义的不可变类，可遵守如下规则：
1. 使用private和final修饰符修饰该类的Field；
2. 提供带参数的构造器，用于根据传入的参数来初始化类里面的Field；
3. 仅为该类的Field的提供getter方法，不要为该类的Field提供setter方法，因为普通方法无法修改final修饰的Field；
4. 如果有必要，重写Object类的hashCode和equals方法，equal方法以关键Field来作为判断两个对象是否相等的标准，除此之外，还应该保证两个用equal方法判断为相等的对象的hashCode也相等。