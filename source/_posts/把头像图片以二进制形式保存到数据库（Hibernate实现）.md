---
title: 把头像图片以二进制形式保存到数据库（Hibernate实现）
date: 2014-07-13 20:47:28
categories:
- 后端 
tags:
- Java
- Hibernate
- 数据库
description: "我们把头像直接保存到数据库，而不是保存一个地址。使用Hibernate自动创建表方式，数据库photo字段的数据类型是CLOB，这是针对MySQL，其他数据库可能不一样。"
---

我们把头像直接保存到数据库，而不是保存一个地址。
使用Hibernate自动创建表方式，**数据库photo字段的数据类型是CLOB，这是针对MySQL，其他数据库可能不一样。**

1、Hibernate环境搭建、建立工程略。  
2、首先我们新建一个User类，储存一些用户信息字段，在Java中photo字段要申明为应该byte[]类型

User.java：
```java
public class User {
    private int id;
    private String name;
    private Integer age;
    private Date birthday; // 生日
    private String desc; // 一大段说明
    private byte[] photo; // 头像图片
    //省略getter and setter Method...
}
```

3、然后配置User的映射文件
User.hbm.xml
```xml
<?xml version="1.0"?>
<!DOCTYPE hibernate-mapping PUBLIC
        "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
        "http://hibernate.sourceforge.net/hibernate-mapping-3.0.dtd">
<hibernate-mapping package="cn.itcast.c_hbm_property">
    <!-- name属性：哪个类
	 table属性：对应哪个表，如果不写，默认的表名就是类的简单名称
    -->
    <class name="User" table="t_user">
	<id name="id" type="int" column="id">
        <generator class="native"/>
    </id>
	<!-- 普通的属性（数据库中的基本类型，如字符串、日期、数字等） 
    	name属性：对象中的属性名，必须要有。
	     type属性：类型，如果不写，Hibernate会自动检测。
	     可以写Java中类的全名。
	     或是写hibernate类型。
	     column属性：对应表中的列名，如果没有，默认为属性名。
	     length属性：长度，不是所有的类型都有长度属性，比如varchar有，但int没有，如果不写默认为255
	     not-null属性：非空约束，默认为false
	 -->
	 <!-- 
	      <property name="name"/>
	 -->
	 <property name="name" type="string" column="name" length="20" not-null="true"/>
	 <property name="age" type="int" column="age_"/>
		
	 <property name="birthday" type="date" column="birthday_"/>
	 <!-- 当列表与关键字冲突时，可以通过column属性指定一个其他的列名。
	      或是使用反引号包围起来。
	      指定使用text类型时，最好再指定length，以确定生成的SQL类型是能够存放指定数量的字符的。
              <property name="desc">
                  <column name="desc_" length="5000" sql-type="text"/>
              </property>		
	 -->
	 <property name="desc" type="text" length="5000" column="`desc`" ></property>
	 <!-- 头像，二进制类型，最好指定长度 -->
	 <property name="photo" type="binary" length="102400"></property>
    </class>
</hibernate-mapping>
```

4、测试类测试新增User信息

photoTest.java
```java
public class photoTest{
    private static SessionFactory sessionFactory;
    static {
	sessionFactory = new Configuration()//
	    .configure()// 读取配置文件
	    .addClass(User.class)//
	    .buildSessionFactory();
    }
    @Test
    public void testSave() throws Exception {
        // 读取图片文件
        InputStream in = new FileInputStream( "c:/test.png");
        byte[] photo = new byte[in.available()];
        in.read(photo);
        in.close();
		
        // 创建对象实例
        User user = new User();
        user.setName("张三");
        user.setAge(20);
        user.setBirthday(new Date());
        user.setDesc("一大段的说明，此处省略5000字……");
        user.setPhoto(photo);
        // 保存
        Session session = sessionFactory.openSession(); // 打开一个新的Session
        Transaction tx = session.beginTransaction(); // 开始事务
        session.save(user);
        tx.commit(); // 提交事务
        session.close(); // 关闭Session，释放资源
    }
}
```

上面代码就是把c盘根目录的test.png图片保存到了数据库。
通过SQL查询该字段可看到是二进制数据，那么证明保存成功。

5、我们再把图片读取出来放在D盘下，取名copy.png

photoTest.testGet():
```java
@Test
public void testGet() throws Exception
    Session session = sessionFactory.openSession();
    Transaction tx = session.beginTransaction();
    User user = (User) session.get(User.class, 1); // 获取
    System.out.println(user.getId());
    System.out.println(user.getName());
    System.out.println(user.getDesc());
    System.out.println(user.getPhoto());
		
    OutputStream out = new FileOutputStream("D:/copy.png");
    out.write(user.getPhoto());
    out.close();
    tx.commit();
    session.close();
}
```

经测试成功运行...........