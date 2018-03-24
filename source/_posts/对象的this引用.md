---
title: Java中对象的this引用
date: 2015-06-28 17:27:34
categories:
- 服务端开发
- Java
tags:
- javase
---

Java提供了this关键字，this关键字总是指向调用该方法的对象。根据this出现的位置不同，this作为对象的默认引用有两种情形：
1. 构造器中引用该构造器正在初始化的对象；
2. 在方法中引用调用该方法的对象。
<!-- more -->
## 在方法中引用调用该方法的对象

this关键字最大的作用就是让类中一个方法，访问该类里的另一个方法或Field。假设定义了一个Dog类，这个Dog对象的run方法需要调用它的jump方法，那么应该如何做呢？是否应该定义如下的Dog类呢？
```java
public class Dog{
    //定义一个jump方法
    public void jump(){
         System.out.println("正在执行jump方法");
    }
    //定义一个run方法，run方法需要借助jump方法
    public void run(){
         Dog d = new Dog();
         d.jump();
         System.out.println("正在执行run方法");
    }
}
```

使用这种方式来定义这个Dog类，确实可以实现在run方法中调用jump方法。那么这种做法是否够好呢？下面在提供一个程序来创建Dog对象，并调用该对象的run方法。
```java
public class DogTest{
    public void main(String[] args){
         //创建Dog对象
         Dog dog = new Dog();
         //调用Dog对象的run方法
         dog.run();
    }
}
```

在上面程序中，一共产生了两个Dog对象，在Dog类的run方法中，程序创建了一个Dog对象，并使用名为d的引用变量来指向该Dog对象；在DogTest的main方法中，程序再次创建了一个Dog对象，并使用名为dog的引用变量来指向该Dog对象。

这里产生了两个问题。第一个问题：在run方法中调用jump方法时是否需一定需要一个Dog对象？第二个问题：是否一定需要重新创建一个Dog对象？第一个问题的答案是肯定的，因为没有使用static修饰的Field和方法都必须使用对象来调用。第二个问题的答案是否定的，因为当程序调用run方法时，一定会提供一个Dog对象，这样就可以直接使用这个已经存在的Dog对象，而无需重新创建新的Dog对象了。

为此，我们需要在run方法中获得调用该方法的对象，通过this关键字就可以满足这个要求。

this可以代表任何对象，当this出现在某个方法中时，它所代表的对象是不确定的，但它的类型是确定的，它所代表的对象只能是当前类，只有当这个方法被调用时，它所代表的对象才被确定下来：**谁在调用这个方法，this就代表谁。**

将前面的Dog类改写为如下形式会更加合适：
```java
public class Dog{
    //定义一个jump方法
    public void jump(){
         System.out.println("正在执行jump方法");
    }
    //定义一个run方法，run方法需要借助jump方法
    public void run(){
         //使用this引用调用jump()方法的对象
         this.jump();
         //可以省略this
         //jump();
         System.out.println("正在执行run方法");
    }
}
```

可以省略this前缀，大部分时候，一个方法访问该类中定义的其他方法、Field时加不加this前缀的效果是完全一样的。

**为什么说this与static不能共存？**
对于static修饰的方法而言，则可以使用类来直接调用该方法，如果在static修饰的方法中使用this关键字，则这个关键字就无法指向合适的对象。所以，static修饰的方法中不能使用this引用。由于static修饰的方法不能使用this引用，所以static修饰的方法不能访问不使用static修饰的普通成员，因此，Java语法规定：**静态成员不能访问非静态成员。**

如果确实需要在静态方法中访问另一个普通方法，则只能通过重新创建一个对象，例如上面的jump调用可以改为如下形式：
```java
new Dog().jump();
```

**什么时候需要使用this前缀？**
大部分时候，普通方法访问其他方法、Field时无需使用this前缀，但如果方法里有局部变量和Field同名，但程序有需要在该方法里访问这个被覆盖的Filed，则必须使用this前缀。

## 构造器中引用该构造器正在初始化的对象

this引用也可以用于构造器中作为默认的引用，这恐怕是我们看到this应用最多最明显的地方吧，由于构造器是直接使用new关键字来调用的，而不是使用对象来调用的，所以this在构造器中引用的是该构造器进行初始化的对象。
```java
public class ThisInConstructor {
    //定义一个名为foo的Field
    public int foo;
    public ThisInConstructor(){
        //在构造器里定义一个foo变量
        int foo = 0;
        //使用this代表此构造器进行初始化的对象
        //下面的代码将会把刚创建对象的foo Field设置为6
        this.foo = 6;
    }
    public static void main(String[] args) {
        //所有使用ThisInConstructor创建的对象的foo Field
        //都将被设为6，所以下面代码将会输出6
        System.out.println(new ThisInConstructor().foo);
    }
}
```

在ThisInConstructor构造器中使用this引用时，this总是引用该构造器正在初始化的对象。程序弟9行将正在执行初始化的ThisInConstructor对象的foo Field设为6，这意味着该构造器返回的所有对象的foo Field都等于6.

与普通方法类似的是，大部分时候，在构造器中访问其他Field和方法时都可以省略this前缀，但如果构造器中有一个与Field同名的局部变量，又必须在构造器中访问这个被覆盖的Field，则必须使用this前缀。

当this作为对象的默认引用时，程序可以像访问普通引用变量一样来访问这个this引用，甚至可以把this当成普通方法的返回值。
