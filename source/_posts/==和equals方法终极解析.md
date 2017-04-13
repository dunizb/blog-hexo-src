---
title: ==和equals方法终极解析
date: 2012-09-02 11:38:57
categories:
- Java
tags: 
- java
- javase
---

Java程序中测试两个变量是否相等有两种方式，一种是利用==运算符，另一种是利用equals方法。

当使用==来判断两个变量是否相等时，**如果两个变量都是基本类型变量，且都是数字类型**（不要求数据类严格相同），则只要两个变量的值相等，就会返回true。

**但对于两个引用类型变量**，他们必须指向同一个对象时，==判断才会返回true。==可用于比较两个类型上没有父子关系的两个对象。下面的程序示范了使用==来判断两种类型变量对否相等的结果。
```java
public class EqualsTest{
    public static void main(String[] args){
        int it = 65;
        float fl=65.0f;
        System.out.println("65和65.0f是否相等？"+(it == fl));      //输出true;

        char ch = 'A';
        System.out.println("65和'A'是否相等？"+(it == ch));      //输出true;

        String str1= new String("hello");
        String str2 = new String("hello");
        System.out.println("str1和str2是否相等？"+(str1 == str2));      //输出false;
        System.out.println("str1是否equals str2？"+(str1.equals(str2)));      //输出true;

        //由于java.lang.string与EqualsTest类没有继承关系
        //所以下面的语句导致编译错误
        // System.out.println("hello"==new EqualsTest());
    }
}
```

运行上面的程序，可以看到65、65.0f和'A'相等。但对于str1和str2，因为他们都是引用类型变量，他们分别指向两个通过new关键字创建的String对象，因此str1和str2两个变量不相等。

对于Java菜鸟而言，String还有一个非常容易迷惑人的地方：''hello''直接量和 new String(“hello”)有什么区别呢？当Java程序中直接使用形如“hello”的字符串直接量（包括子编译时就可以计算出来的字符串值）时，JVM将会使用常量池来管理这些字符串；当使用new String("hello")时，JVM先会使用常量池来管理"hello"直接量，再调用String类的构造器来创建一个新的String对象，新创建的String对象被保存在对内存中。换句话说`new String("hello")`一共产生了两个对象。

**提示** 
> 常量池，专门用于管理在编译期被确定并保存在编译的.class文件中的一些数据，它包括了关于类、方法、接口中的常量，还包括字符串常量。

下面程序示范了JVM使用常量池管理字符串直接量的情形。
```java
class stringCompareTest{
    //下面程序示范了JVM使用常量池管理字符串直接量的情形
    public static void main(String[] args){
        //s1直接引用常量池中的''你好Java"
        String s1 = "你好Java";
        String s2 = "你好";
        String s3 = "Java";

            //s4后面的字符串值可以在编译期就确定下来
            //s4直接引用常量池中的“你好Java”
        String  s4="你好"+"Java";

        //s5后面的字符串值可以在编译期就确定下来
        //s5直接引用常量池中的“你好Java”
        String s5 = "你" + "好" + "Java";

        //s6后面的字符串值不能在编译期就确定下来
        //不能引用常量池中字符串
        String s6 =s2+s3;

        //使用new调用构造器将会创建一个新的string对象
        //s7引用对内存中新创建的string对象
        String s7 = new String("你好Java");

        System.out.println(s1==s4);   //true
        System.out.println(s1==s5);   //true
        System.out.println(s1==s6);   //false
        System.out.println(s1==s7);   //false
    }
}
```

JVM常量池保证相同的字符串直接量只有一个，不会产生多个副本。例子中的s1、s4、s5所引用的字符串可以在编译期就确定下来，因此他们都讲引用常量池的同一个字符窜对象。

使用`new String()`创建的字符串对象是运行时创建出来的，他被保存在运行时内存内（即对内存），不会放入常量池中。

但在很多时候，程序判断两个引用变量是否相等时，也希望有一种类似于“值相等”的判断规则，并不严格要求两个引用变量指向同一个对象。例如对于两个字符串变量，可能只是要求他们的引用字符串对象里包含的字符串序列相同即可认为相等。此时就可以利用String对象的equals反法来进行判断，例如上面程序中`str1.equals(str2)`将返回true。

equals方法是Object类提供的一个实例方法，因此所有引用变量都可以调用该方法来判断是否与其他引用变量相等。但是用这个方法判断两个对象相等的标准与==运输符没有区别，同样要求两个引用变量指向同一个对象才会返回true。因此这个Object类提供的equals反法没有太大的意义，如果希望采用自定义的相等标准，则可采用重写equals方法来实现。  

**提示**
> String已经重写了Object的equals()方法，String的equals()方法判断两个字符串相等的标准是：只要两个字符串所包含的字符序列相同，通过equals比较将返回true，否则将返回false。
> 
> 网上很多地方和书上都说equals反法是判断两个对象值相等。这个说法是相当错误的，什么叫对象值呢？对象的值如何相等？实际上，重写equals方法就提供了自定义相等标准，你认为怎样是相等，那就怎样相等，一切都是你做主！

重写equals反法就不再说明，写着太累了、、、。

Object默认提供的`equals()`只是比较地址，即Object类的equals方法比较的结果与==比较的结果完全相同。因此，在实际应用中常常需要重写equals方法，重写equals方法时，相等条件是又系统要求决定的，因此equals方法的实现也是又系统要求决定的。
