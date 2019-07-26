---
title: 'JDBC编程与SQL注入、大数据、事物处理，JDBC复习(一) '
date: 2014-07-06 18:44:07
categories:
- 服务端开发
- Java
tags:
- jdbc
- sql
---

[一、什么是JDBC](./#一、什么是JDBC)  
[二、JDBC驱动](./#二、JDBC驱动)  
[三、数据库驱动](./#三、数据库驱动)  
[四、应用程序、JDBC API、数据库驱动及数据库之间的关系](./#四、应用程序、JDBC API、数据库驱动及数据库之间的关系)  
[五、连接数据库的6步骤](./#五、连接数据库的6步骤)  
[六、规范和封装JDBC代码](./#六、规范和封装JDBC代码)  
[七、对数据库进行CRUD操作](./#七、对数据库进行CRUD操作)  
[八、Statement的SQL注入问题](./#八、Statement的SQL注入问题)  
[九、使用JDBC处理数据](./#九、使用JDBC处理数据)  
[十、JDBC事物处理](./#十、JDBC事物处理)
<!-- more -->
## 一、什么是JDBC

1. SUN公司为统一对数据库的操作，定义了一套Java操作数据库的规范，称之为JDBC。
2. JDBC 是一种用于执行SQL 语句的 Java API.是 Java DataBase java数据库连接)的缩写. 
3. 它由一组用 java编程语言编写的类和接口组成. JDBC 为工具/数据库开发人员提供了一个标准的API,使得他们能够用纯 Java API 来编写数据库应用程序.
4. J2SE的一部分，由java.sql,javax.sql包组成。

开发JDBC应用需要：
1. java.sql和javax.sql2个包的支持外，
2. 还需要导入相应JDBC的数据库实现(即数据库驱动)。

## 二、JDBC驱动

### 2.1 JDBC-ODBC桥

由ODBC驱动提供JDBC访问 ODBC(Open Database Connectivity，开放数据库连接)是微软公司开放服务结构中有关数据库的一个组成部分，它建立了一组规范，并提供了一组对数据库访问的的标准API

缺点： 要求客户端必须安装ODBC驱动 执行效率比较低,对于那些大数据量存取的应用是不适合的 适用于快速的原型系统，没有提供JDBC驱动的数据库如Access

### 2.2 纯Java类库

他将JDBC 请求直接翻译成为特定的数据库协议。这种驱动直接把JDBC调用转换为符合相关数据库系统规范的请求。 最高的性能，通过自己的本地协议直接与数据库引擎通信

特点：适合那些连接单一或多个数据库的工作组应用。

## 三、数据库驱动

1）数据库厂商提供的实现接口

2）应用程序实现的编程接口 JDBC制定标准；数据厂商实现标准；用户使用标准。 

一个JDBC驱动程序的实现：
- 在一个静态代码段中生成自己的一个实例
- 当系统调用它的构造方法时，它会向driver manger进行注册

## 四、应用程序、JDBC API、数据库驱动及数据库之间的关系
![](http://img1.ph.126.net/W8X6UYnbh3Z0Jl3fQ6vEwA==/6608410129586287978.jpg)

## 五、连接数据库的6步骤

1）注册驱动 (只做一次)   
2）建立连接(Connection)  
3）创建执行SQL的语句(Statement)  
4）执行语句  
5）处理执行结果(ResultSet)  
6）释放资源  

下面我就依照上面六个步骤来写一个示例：
```java
public static void test() throws SQLException{
    //1.注册驱动
    DriverManager.registerDriver(new com.mysql.jdbc.Driver());
    //2.建立连接
    Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/test", "root", "root");
    //3.创建语句
    Statement st = conn.createStatement();
    //4.执行语句
    ResultSet rs = st.executeQuery("select * from oa_id_user");
    //6.处理结果
    while(rs.next()){
	System.out.println("user_id:"+rs.getString("user_id"));
	System.out.println("name:"+rs.getString("name"));
	System.out.println("pass_word:"+rs.getString("pass_word"));
	System.out.println("phone:"+rs.getString("phone"));
    }
    //7.释放资源
    rs.close();
    st.close();
    conn.close();
}
```

当然，暂时不考虑其他代码优化性的东西，只是简单实现结果，我们这个代码其实是有很多问题的，比如关闭连接、异常的处理等，在后面会依次对6个步骤进行详细讲解。并在此基础上逐步优化代码。

### 5.1 注册驱动（Driver）

Driver是一个接口，每个驱动程序类都必须实现这个接口
- Class.forName(“com.mysql.jdbc.Driver”);推荐这种方式，采用此种方式，程序仅仅只需要一个字符串，不需要import驱动的API，这样可使程序不依赖具体的驱动，使程序的灵活性更高。 
- DriverManager.registerDriver(com.mysql.jdbc.Driver);会造成DriverManager中产生两个一样的驱动，并会对具体的驱动类产生依赖。 注意：在实际开发中，并不推荐采用这个方法注册驱动。
- System.setProperty(“jdbc.drivers”, “driver1:driver2”);虽然不会对具体的驱动类产生依赖；但注册不太方便，所以很少使用。 根据url获取数据库的连接。如果能建立连接，则返回一个Connection对象，Connection对象代表与一个特定数据库的会话过程。
- 驱动类型(四种类型)

### 5.2 建立连接（Connection）

JDBC程序中的Connection，它用于代表数据库的连接，Collection是数据库编程中最重要的一个对象，客户端与数据库所有交互都是通过connection对象完成的，这个对象的常用方法：
- createStatement()：创建向数据库发送sql的statement对象。
- prepareStatement(sql) ：创建向数据库发送预编译sql的PrepareSatement对象。
- prepareCall(sql)：创建执行存储过程的callableStatement对象。
- setAutoCommit(boolean autoCommit)：设置事务是否自动提交。
- commit() ：在链接上提交事务。
- rollback() ：在此链接上回滚事务

Connection conn = DriverManager.getConnection(url, user, password); 

- url格式：JDBC:子协议:子名称//主机名:端口/数据库名？属性名=属性值&… 
- User,password可以用“属性名=属性值”方式告诉数据库； 
- 其他参数如：useUnicode=true&characterEncoding=GBK。

### 5.3 创建和执行SQL的语句(Statement)

JDBC程序中的Statement对象用于向数据库发送SQL语句， Statement对象常用方法：
- execute(String sql)：用于向数据库发送任意sql语句
- executeQuery(String sql) ：只能向数据发送select语句。
- executeUpdate(String sql)：只能向数据库发送insert、update或delete语句
- addBatch(String sql) ：把多条sql语句放到一个批处理中。
- executeBatch()：向数据库发送一批sql语句执行。
- clearBatch()：清空缓冲

### 5.4 处理结果（ResultSet）

JDBC程序中的ResultSet用于代表Sql语句的执行结果。Resultset封装执行结果时，采用的类似于表格的方式。ResultSet 对象维护了一个指向表格数据行的游标，初始的时候，游标在第一行之前，调用ResultSet.next() 方法，可以使游标指向具体的数据行，进行调用方法获取该行的数据。

ResultSet既然用于封装执行结果的，所以该对象提供的都是用于获取数据的get方法：

**获取任意类型的数据**
getObject(int index)  
getObject(string columnName)  

**获取指定类型的数据，例如：**
getString(int index)  
getString(String columnName)  

**ResultSet还提供了对结果集进行滚动的方法：**
next()：移动到下一行  
previous()：移动到前一行  
absolute(int row)：移动到指定行  
beforeFirst()：移动resultSet的最前面。  
afterLast() ：移动到resultSet的最后面。

### 5.5 释放资源

释放ResultSet, Statement,Connection. 

数据库连接（Connection）是非常稀有的资源，用完后必须马上释放，如果Connection不能及时正确的关闭将导致系统宕机。Connection的使用原则是尽量晚创建，尽量早的释放。

## 六、规范和封装JDBC代码

我们来规范和封装前面所写的[程序1]代码，把注册驱动、建立连接、关闭资源封装成一个工具类jdbcUtils，建立一个机遇单例设计模式的jdbcUtils类，使用final修饰类，确保是一个不可变类；在static代码块中写注册驱动，因为注册驱动只需要注册一次就可以了。

jdbcUtils.java:
```java
public final class JdbcUtilsSingleton {
    private static String url = "jdbc:mysql://localhost:3306/test";
    private static String user = "root";
    private static String password = "root";
	
    private JdbcUtilsSingleton(){};
	
    private static JdbcUtilsSingleton instance = null;
	
    public static JdbcUtilsSingleton getInstance(){
    	if(instance == null){
            synchronized (JdbcUtilsSingleton.class) {
                if(instance == null){
                        instance = new JdbcUtilsSingleton();
                }
	        }
	    }
	    return instance;
    }
	
    //1.注册驱动
    static{
    	try {
	        Class.forName("com.mysql.jdbc.Driver");
	    } catch (ClassNotFoundException e) {
	        throw new ExceptionInInitializerError(e);
	    }
    }
	
    //2.获得连接
    public Connection getConnenction() throws SQLException{
	    return DriverManager.getConnection(url, user, password);
    }
	
    //6.关闭资源
    public static void close(ResultSet rs,Statement stat,Connection conn){
        try {
            if(rs!=null)rs.close();
        }catch (SQLException e) {
            // TODO: handle exception
        }finally{
            try {
            if(stat!=null)stat.close();
            }catch (SQLException e) {
                // TODO: handle exception
            }finally{
                try {
                    if(conn!=null)conn.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

然后我们一个测试方法进行调用
```java
public static void template() throws Exception{
    Connection conn = null;
    Statement stat = null;
    ResultSet rs = null;
    try {
        //conn = JdbcUtils.getConnenction();
        conn = JdbcUtilsSingleton.getInstance().getConnenction();
        //3.创建语句
        stat = conn.createStatement();
        //4.执行语句
        rs = stat.executeQuery("select * from oa_id_user");
        //5.处理结果
        while(rs.next())
            System.out.println("user_id:"+rs.getString("user_id"));
            System.out.println("name:"+rs.getString("name"));
            System.out.println("pass_word:"+rs.getString("pass_word"));
            System.out.println("phone:"+rs.getString("phone"));
        }
    }finally{
	    JdbcUtils.close(rs, stat, conn);
    }
}
```

## 七、对数据库进行CRUD操作

CRUD操作
```java
public static void main(String[] args) throws Exception {
    //read();
    //create();
    //update();
    delete();
}
	
public static void create() throws Exception{
	Connection conn = null;
	Statement stat = null;
	ResultSet rs = null;
	try {
	    //conn = JdbcUtils.getConnenction();
	    conn = JdbcUtilsSingleton.getInstance().getConnenction();
	    //3.创建语句
	    stat = conn.createStatement();
	    //4.执行语句
	    String sql = "insert into user(id,name,login_name,pass_word,sex) values('3','zhangsan','zhangsan','123456',1)";
	    int i = stat.executeUpdate(sql);
	    //5.处理结果
	    System.out.println("i="+i);
    }finally{
        JdbcUtils.close(rs, stat, conn);
    }
}
	
public static void read() throws Exception{
    Connection conn = null;
    Statement stat = null;
    ResultSet rs = null;
    try {
        //conn = JdbcUtils.getConnenction();
        conn = JdbcUtilsSingleton.getInstance().getConnenction();
        //3.创建语句
        stat = conn.createStatement();
        //4.执行语句
        rs = stat.executeQuery("select id,name,login_name,pass_word from USER");
        //5.处理结果
        while(rs.next()){
            System.out.println("id:"+rs.getString("id"));
            System.out.println("name:"+rs.getString("name"));
            System.out.println("pass_word:"+rs.getString("pass_word"));
            System.out.println("login_name:"+rs.getString("login_name"));
	    }
    }finally{
	    JdbcUtils.close(rs, stat, conn);
    }
}
	
public static void update() throws Exception{
    Connection conn = null;
    Statement stat = null;
    ResultSet rs = null;
    try {
        //conn = JdbcUtils.getConnenction();
        conn = JdbcUtilsSingleton.getInstance().getConnenction();
        //3.创建语句
        stat = conn.createStatement();
        //4.执行语句
        String sql = "update user set sex=2 where sex=1";
        int i = stat.executeUpdate(sql);
        //5.处理结果
        System.out.println("i="+i);
    }finally{
	    JdbcUtils.close(rs, stat, conn);
    }
}
	
public static void delete() throws Exception{
    Connection conn = null;
    Statement stat = null;
    ResultSet rs = null;
    try {
        //conn = JdbcUtils.getConnenction();
        conn = JdbcUtilsSingleton.getInstance().getConnenction();
        //3.创建语句
        stat = conn.createStatement();
        //4.执行语句
        String sql = "delete from user where sex=1";
        int i = stat.executeUpdate(sql);
        //5.处理结果
        System.out.println("i="+i);
    }finally{
	    JdbcUtils.close(rs, stat, conn);
    }
}
```

## 八、Statement的SQL注入问题

**1、什么是PreparedStatement？**
预编译语句PreparedStatement 是java.sql中的一个接口，它是Statement的子接口。通过Statement对象执行SQL语句时，需要将SQL语句发送给DBMS，由DBMS首先进行编译后再执行。PreperedStatement（从Statement扩展而来）。

**2、SQL注入**
在SQL中包含特殊字符或SQL的关键字(如：`' or 1 or '`)时Statement将出现不可预料的结果（出现异常或查询的结果不正确），可用PreparedStatement来解决。

**3、使用它有什么好处？**
预编译语句和Statement不同，在创建PreparedStatement 对象时就指定了SQL语句，正常情况下，当你向数据库提交SQL语句后，数据库要对这条语句进行编译，例如语法分析、优化路径选择、分配资源等一系列操作，这是需要时间的。而使用PreparedStatement接口，数据库只需要编译一次，其他只是更改参数就可以了。 所以，当你向数据库中进行批量操作的时候，预编译效率比较高。 另外，Statement会使数据库频繁编译SQL，可能造成数据库缓冲区溢出。

相对于Statement对象而言：
- 提高效率。
  当需要对数据库进行数据插入、更新或者删除的时候，程序会发送整个SQL语句给数据库处理和执行。数据库处理一个SQL语句，需要完成解析SQL语句、检查语法和语义以及生成代码；一般说来，处理时间要比执行语句所需要的时间长。 PreparedStatement可以尽可能的提高访问数据库的性能，数据库在处理SQL语句时都有一个预编译的过程，而预编译对象就是把一些格式固定的SQL编译后，存放在内存池中即数据库缓冲池，当我们再次执行相同的SQL语句时就不需要编译的了，只需DBMS运行SQL语句。所以当你需要执行Statement对象多次的时候，PreparedStatement对象将会大大降低运行时间，特别是的大型的数据库中，它可以有效的也加快了访问数据库的速度。
- 提高安全性。PreperedStatement可以避免SQL注入的问题。
- 更加灵活。PreperedStatement对于sql中的参数，允许使用占位符(?)的形式进行替换，简化sql语句的编写。

下面我写一个使用PreperedStatement方式的程序
```java
/**
 * 使用预编译的PreparedStatementSQL语句
 * @param login_name
 * @throws Exception
 */
public static void read2(String login_name) throws Exception{
    Connection conn = null;
    PreparedStatement ps = null;
    ResultSet rs = null;
    try {
        //conn = JdbcUtils.getConnenction();
        conn = JdbcUtilsSingleton.getInstance().getConnenction();
        //3.创建语句
        String sql = "select id,name,login_name,pass_word from USER where login_name=?";
	    ps = conn.prepareStatement(sql);
	    //设置参数
	    ps.setString(1, login_name);
	    //4.执行语句，千万记住不要再给executeQuery()sql参数了
	    rs = ps.executeQuery();
	    //5.处理结果
	    while(rs.next()){
            System.out.println("id:"+rs.getString("id"));
            System.out.println("name:"+rs.getString("name"));
	    }
      }finally{
          JdbcUtils.close(rs, ps, conn);
      }
}
public static void main(String[] args) throws Exception {
    read2("admin");
}
```

## 九、使用JDBC处理数据

详细信息见java.sql.Types 

几种特殊且比较常用的类型：  
1. DATA,TIME,TIMESTAMP → date,time,datetime 
  存：ps.setDate(i,d); ps.setTime(i,t); ps.setTimestamp(i, ts);
  取：rs.getDate(i); rs.getTime(i); rs.getTimestamp(i);     
2. CLOB → text       
  存：ps.setCharacterStream(index, reader, length); ps.setString(i, s);       
  取：reader = rs. getCharacterStream(i);reader = rs.getClob(i).getCharacterStream();string = rs.getString(i);     
3. BLOB → blob      
  存：ps.setBinaryStream(i, inputStream, length);        
  取：rs.getBinaryStream(i);rs.getBlob(i).getBinaryStream();

### 9.1 CLOB（Text）

我们新建一个测试表：clob_text:
![](http://ww4.sinaimg.cn/large/006tNc79ly1g5d7zn249zj30ki05iglx.jpg)

那么怎么插入一条带Clob数据呢？其实就是IO操作。。。

插入clob字段：
```java
public static void create() throws Exception{
    Connection conn = null;
    PreparedStatement ps = null;
    ResultSet rs = null;
    try {
        conn = JdbcUtilsSingleton.getInstance().getConnenction();
        String sql = "insert into clob_text(id,news) values('1',?)";
        ps = conn.prepareStatement(sql);
	
        //处理clob字段	
	    ew File("src/com/ideal/jdbc/JdbcUtils.java");
        Reader reader = new BufferedReader(new FileReader(file));
	    ps.setCharacterStream(1, reader, file.length());
		
        int i = ps.executeUpdate();
        reader.close();
	
        //5.处理结果
        System.out.println("i="+i);
    }finally{
        JdbcUtils.close(rs, ps, conn);
    }
}
```

查询、读取clob字段：
```java
public static void read() throws Exception{
    Connection conn = null;
    Statement st = null;
    ResultSet rs = null;
    try {
        conn = JdbcUtilsSingleton.getInstance().getConnenction();
        st = conn.createStatement();
        String sql = "select id,news from clob_text";
        rs = st.executeQuery(sql);
		
        //处理clob结果
        while(rs.next()){
            Clob clob = rs.getClob("news");
            Reader reader = clob.getCharacterStream();
                
            File file = new File("JdbcUtils_bak.java");
            Writer writer = new BufferedWriter(new FileWriter(file));
            char[] buff = new char[1024];
            for (int i = 0; (i = reader.read(buff)) > 0;) {
                writer.write(buff, 0, i);
            }
            writer.close();
            reader.close();
        }
    }finally{
	    JdbcUtils.close(rs, st, conn);
    }
}
```

运行后刷新工程，既可以看到跟目下有一个`JdbcUtils_bak.java`文件了。

### 9.2 BLOB（二进制数据）

MySQL中，BLOB是一个二进制大型对象，是一个可以存储大量数据的容器，它能容纳不同大小的数据。BLOB类型实际是个类型系列（TinyBlob、Blob、MediumBlob、LongBlob），除了在存储的最大信息量上不同外，他们是等同的。

插入blob字段:
```java
static void create() throws SQLException, IOException {
    Connection conn = null;
    PreparedStatement ps = null;
    ResultSet rs = null;
    try {
        // 2.建立连接
        conn = JdbcUtils.getConnection();
        // conn = JdbcUtilsSing.getInstance().getConnection();
        // 3.创建语句
        String sql = "insert into blob_test(big_bit) values (?) ";
        ps = conn.prepareStatement(sql);
        File file = new File("IMG_0002.jpg");
        InputStream in = new BufferedInputStream(new FileInputStream(file));
        ps.setBinaryStream(1, in, (int) file.length());
        // 4.执行语句
        int i = ps.executeUpdate();
        in.close();
        System.out.println("i=" + i);
    } finally {
	    JdbcUtils.free(rs, ps, conn);
    }
}
```

查询、读取blob字段:
```java
static void read() throws SQLException, IOException {
    Connection conn = null;
    Statement st = null;
    ResultSet rs = null;
    try {
        // 2.建立连接
        conn = JdbcUtils.getConnection();
        // conn = JdbcUtilsSing.getInstance().getConnection();
        // 3.创建语句
        st = conn.createStatement();
        // 4.执行语句
        rs = st.executeQuery("select big_bit  from blob_test");
        // 5.处理结果
        while (rs.next()) {
            // Blob blob = rs.getBlob(1);
            // InputStream in = blob.getBinaryStream();
            InputStream in = rs.getBinaryStream("big_bit");
            File file = new File("IMG_0002_bak.jpg");
            OutputStream out = new BufferedOutputStream(new FileOutputStream(file));
            byte[] buff = new byte[1024];
            for (int i = 0; (i = in.read(buff)) > 0;) {
                out.write(buff, 0, i);
            }
            out.close();
            in.close();
	    }
    } finally {
	    JdbcUtils.free(rs, st, conn);
    }
}
```

## 十、JDBC事物处理

### 10.1 事物的特性（ACID）

原子性（Atomicity）原子性是指事务是一个不可分割的工作单位，事务中的操作要么都发生，要么都不发生。    
一致性（Consistency）事务必须使数据库从一个一致性状态变换到另外一个一致性状态。   
隔离性（Isolation）事务的隔离性是多个用户并发访问数据库时，数据库为每一个用户开启的事务，不能被其他事务的操作数据所干扰，多个并发事务之间要相互隔离。   
持久性（Durability）持久性是指一个事务一旦被提交，它对数据库中数据的改变就是永久性的，接下来即使数据库发生故障也不应该对其有任何影响。

### 10.2 JDBC控制事物的三个方法

connection.setAutoCommit(false);//打开事务。   
connection.commit();//提交事务。   
connection.rollback();//回滚事务。

> 注意：事物跟数据库启用的引擎有关系，数据库引擎在这里略。

### 10.3 事物的保存点设置

在JDBC的事物处理中，可以应用保存点技术，对一个事物中的处理进行部分提交。

如下示例，三个处理：
1. 张三减10元
2. 李四加10元
3. 李四加100元，

在3的地方出错的话，把1和2的处理进行提交，使用了保存点技术。

演示保存点:
```java
public class SavePoint {
    public static void main(String[] args) throws Exception {
	    savepoint();
    }
    public static void savepoint() throws Exception {
        System.setProperty("jdbc.drivers", "com.mysql.jdbc.Driver");
        Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/jdbc", "root", "");
        //初始化不自动提交
        conn.setAutoCommit(false);
        // 张三减10元
        String sql = "update user set money = money - 10 where id = 1";
        PreparedStatement st = conn.prepareStatement(sql);
        st.executeUpdate();
        // 李四加10元
        sql = "update user set money = money + 10 where id = 2";
        st = conn.prepareStatement(sql);
        st.executeUpdate();
		
        // 设立保存点
        Savepoint sp = conn.setSavepoint();
		
        // 李四加再加100元（SQL的where关键字写的不对，会出现异常）
        sql = "update user set money = money + 100 wherewhere id = 2";
        st = conn.prepareStatement(sql);
		
        try {
            st.executeUpdate();
        } catch (Exception e){
            // 回滚到李四加10元后的状态
            conn.rollback(sp);
            System.out.println("回滚到李四加10元后的状态");
        }
        conn.commit();
        st.close();
        conn.close();
    }
}
```

### 10.4 事物的隔离级别

多个线程开启各自事务操作数据库中数据时，数据库系统要负责隔离操作，以保证各个线程在获取数据时的准确性。 如果不考虑隔离性，可能会引发如下问题：
- **脏读（dirty reads）**，一个事务读取了另一个未提交的并行事务写的数据。 这是非常危险的。
- **不可重复读（non-repeatable reads）**， 在一个事物内读取表中的某一行数据，多次读取结果不同。 
  a）例如银行想查询A帐户余额，第一次查询A帐户为200元，此时A向帐户内存了100元并提交了，银行接着又进行了一次查询，此时A帐户为300元了。银行两次查询不一致，可能就会很困惑，不知道哪次查询是准的。 
  b）和脏读的区别是，脏读是读取前一事务未提交的脏数据，不可重复读是重新读取了前一事务已提交的数据。 
  c）很多人认为这种情况就对了，无须困惑，当然是后面的为准。我们可以考虑这样一种情况，比如银行程序需要将查询结果分别输出到电脑屏幕和写到文件中，结果在一个事务中针对输出的目的地，进行的两次查询不一致，导致文件和屏幕中的结果不一致，银行工作人员就不知道以哪个为准了。
- **虚读/幻读（phantom read）**，是指在一个事务内读取到了别的事务插入的数据，导致前后读取不一致。 
  如丙存款100元未提交，这时银行做报表统计account表中所有用户的总额为500元，然后丙提交了，这时银行再统计发现帐户为600元了，造成虚读同样会使银行不知所措，到底以哪个为准。

> 注意：隔离级别越高并发性能越差。

### 10.5 事务隔离性的设置语句

数据库共定义了四种隔离级别： 
- Serializable：可避免脏读、不可重复读、虚读情况的发生。（串行化） 
- Repeatable read：可避免脏读、不可重复读情况的发生。（可重复读） 
- Read committed：可避免脏读情况发生（读已提交）。 
- Read uncommitted：最低级别，以上情况均无法保证。(读未提交)   

```sql
set transaction isolation level; --设置事务隔离级别
select @@tx_isolation;  --查询当前事务隔离级别
```

```java
connection.setTransactionIsolation(Connection.TRANSACTION_READ_COMMITTED); 
```

V:可能出现，X:不会出现  
![](http://ww3.sinaimg.cn/large/006tNc79ly1g5d7zolv7yj30h105eq2v.jpg)


















