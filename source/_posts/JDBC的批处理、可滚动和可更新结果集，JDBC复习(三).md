---
title: JDBC的批处理、可滚动和可更新结果集，JDBC复习(三)
date: 2014-07-08 13:11:52
categories:
- 服务端开发
- Java
tags:
- 数据库
- jdbc
description: "业务场景：当需要向数据库发送一批SQL语句执行时，应避免向数据库一条条的发送执行，而应采用JDBC的批处理机制，以提升执行效率。"
---

## 一、使用JDBC的批处理功能

业务场景：当需要向数据库发送一批SQL语句执行时，应避免向数据库一条条的发送执行，而应采用JDBC的批处理机制，以提升执行效率。 

实现批处理有两种方式

### 第一种方式
`Statement.addBatch(sql)` 执行批处理SQL语句   
`executeBatch()方法`：执行批处理命令   
`clearBatch()方法`：清除批处理命令  

采用Statement.addBatch(sql)方式实现批处理：  
优点：可以向数据库发送多条不同的ＳＱＬ语句。  
缺点： SQL语句没有预编译。 当向数据库发送多条语句相同，但仅参数不同的SQL语句时，需重复写上很多条SQL语句。  

### 实现批处理的第二种方式：PreparedStatement.addBatch()

优点：发送的是预编译后的SQL语句，执行效率高。   
缺点：只能应用在SQL语句相同，但参数不同的批处理中。因此此种形式的批处理经常用于在同一个表中批量插入 数据，或批量更新表的数据。

批处理实例:
```java
static void createBatch() throws SQLException {
    Connection conn = null;
    PreparedStatement ps = null;
    ResultSet rs = null;
    try {
	conn = JdbcUtils.getConnection();
	String sql = "insert into user(name,birthday, money) values (?, ?, ?) ";
	ps = conn.prepareStatement(sql, Statement.RETURN_GENERATED_KEYS);
	for (int i = 0; i < 100; i++) {
	    ps.setString(1, "batch name" + i);
	    ps.setDate(2, new Date(System.currentTimeMillis()));
	    ps.setFloat(3, 100f + i);
	    ps.addBatch();
	}
	int[] is = ps.executeBatch();
    } finally {
	JdbcUtils.free(rs, ps, conn);
    }
}
```

## 二、JDBC可滚动和可更新结果集

JDBC中ResultSet类中，我们可以使用next()方法从结果集中的一条记录移动到下一条记录。同时我们还可以在定义Statement或PrepareStatement时指定结果集是否可滚动或更新。以下是注意事项：

- 即使使用了CONCUR_UPDATABLE参数来创建Statement，得到的结果集也不一定是“可更新的”。如果你的记录集来自合并查询，这样的结果集就可能是不可更新的。可以使用ResultSet类的getConcurrency()方法来确定是否为可更新结果集。
- 在JDBC中使用可更新的结果集来更新数据库，不能使用"select * from table"方式的sql语句，必须写成以下两种形式之一：
  - select table.* from table 
  - select column1,column2,column3 from table

- 如果使用的JDBC版本是3.0 (JDK1.4以后的版本)，使用CallableStatement的setXXX()方法来设置IN参数时，既可以使用索引，也可以使用参数名称。如果使用JDBC2.0及以下版本，只能使用使用索引。对于对应的getXXX()中的参数也一样。

> 为了使照片能够快速、正确的存入oracle数据库中，最好使用oracle 10g的JDBC驱动！

ResultSet 可取值常量值：
- TYPE_FORWARD_ONLY 结果集不能滚动
- TYPE_SCROLL_INSENSITIVE 结果集可以滚动，但对数据库变化不敏感
- TYPE_SCROLL_SENSITIVE 结果集可以滚动，但对数据库变化敏感
- CONCUR_READ_ONLY 结果集不能用于更新数据库(默认值)
- CONCUR_UPDATABLE 结果集可以用于更新数据库 

例如：只想滚动遍历结果集，而不想修改他的数据，那么可以使用一下语句：
```java
Statement stat = conn.createStatement(ResultSet.TYPE_SCROLL_INSENSITIVE, ResultSet.CONCUR_READ_ONLY);
```
向前滚动：if(rs.previous)...
向前或者向后滚动：rs.relative(n); n为正数或者负数
返回当前行号：rs.getRow();

可滚动的结果集实例:
```java
static void scroll() throws SQLException {
    Connection conn = null;
    Statement st = null;
    ResultSet rs = null;
    try {
	// 2.建立连接
	conn = JdbcUtils.getConnection();
	st = conn.createStatement(ResultSet.TYPE_SCROLL_SENSITIVE,ResultSet.CONCUR_READ_ONLY);
	rs = st.executeQuery("select id, name, money, birthday  from user limit 150, 10");
	while (rs.next()) {
	    System.out.println(rs.getObject("id") + "\t"
		+ rs.getObject("name") + "\t"
		+ rs.getObject("birthday") + "\t"
		+ rs.getObject("money"));
	    }
	    System.out.println("------------");
	    //绝对定位，定位到结果集的第5行
	    rs.absolute(150);
	    int i = 0;
	    while (rs.next() && i < 10) {
		i++;
		System.out.println(rs.getObject("id") + "\t"
	    	    + rs.getObject("name") + "\t"
		    + rs.getObject("birthday") + "\t"
		    + rs.getObject("money"));
	    }
		
	    //结果集的上一行数据
	    if (rs.previous())
		System.out.println(rs.getObject("id") + "\t"
		    + rs.getObject("name") + "\t"
		    + rs.getObject("birthday") + "\t"
		    + rs.getObject("money"));
	} finally {
	    JdbcUtils.free(rs, st, conn);
    }
}
```

在读取数据的时候可以更新数据

可更新的结果集实例:
```java
public static void readRs() throws Exception{
    Connection conn = null;
    Statement stat = null;
    ResultSet rs = null;
    try {
	//conn = JdbcUtils.getConnenction();
	conn = JdbcUtilsSingleton.getInstance().getConnenction();
	//3.创建语句
	stat = conn.createStatement(ResultSet.TYPE_SCROLL_INSENSITIVE,ResultSet.CONCUR_UPDATABLE);
	//4.执行语句
	rs = stat.executeQuery("select id,name,login_name,pass_word from USER where id>2");
	//5.处理结果
	while(rs.next()){
    	    System.out.println("id:"+rs.getString("id"));
	    System.out.println("name:"+rs.getString("name"));
			
	    //更新数据：如果name=zhangsan,那么就更新name=zhangsan2014
	    String name = rs.getString("name");
	    if("zhangsan".equals(name)){
		rs.updateString("name", "zhangsan2014");
                rs.updateRow();
	    }
	}
    }finally{
	JdbcUtils.close(rs, stat, conn);
    }
}
```
在实际开发中并不建议这样直接更新数据，因为不明确，不只管。还是建议写一条update的更新语句去更新数据。