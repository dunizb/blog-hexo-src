---
title: 'JDBC操作数据库的元数据信息，JDBC复习(四) '
date: 2014-07-09 12:14:07
categories:
- 服务端开发
- Java
tags:
- jdbc
- 数据库
description: "简单的说，元数据（Metadata）就是数据的数据。在数据仓库系统中，元数据可以帮助数据仓库管理员和数据仓库的开发人员非常方便地找到他们所关心的数据；元数据是描述数据仓库内数据的结构和建立方法的数据。"
---

## 目录

[什么是数据库元数据？](./#什么是数据库元数据？)
[JDBC中获取数据库元数据信息](./#JDBC中获取数据库元数据信息)
[参数的元数据信息](./#参数的元数据信息)
[结果集元数据信息](./#结果集元数据信息)

## 什么是数据库元数据？

简单的说，元数据（Metadata）就是数据的数据。在数据仓库系统中，元数据可以帮助数据仓库管理员和数据仓库的开发人员非常方便地找到他们所关心的数据；元数据是描述数据仓库内数据的结构和建立方法的数据。

可将其按用途的不同分为两类：技术元数据（Technical Metadata）和业务元数据（Business Metadata）。

**技术元数据**
存储关于数据仓库系统技术细节的数据，是用于开发和管理数据仓库使用的数据，它主要包括以下信息：数据仓库结构的描述，包括仓库模式、视图、维、层次结构和导出数据的定义，以及数据集市的位置和内容；业务系统、数据仓库和数据集市的体系结构和模式；汇总用的算法，包括度量和维定义算法，数据粒度、主题领域、聚集、汇总、预定义的查询与报告；；由操作环境到数据仓库环境的映射，包括源数据和它们的内容、数据分割、数据提取、清理、转换规则和数据刷新规则、安全(用户授权和存取控制)。

**业务元数据**
从业务角度描述了数据仓库中的数据，它提供了介于使用者和实际系统之间的语义层，使得不懂计算机技术的业务人员也能够"读懂"数据仓库中的数据。业务元数据主要包括以下信息：使用者的业务术语所表达的数据模型、对象名和属性名；访问数据的原则和数据的来源；系统所提供的分析方法以及公式和报表的信息。

更多数据库元数据信息请百度吧，有点复杂深奥.........

## JDBC中获取数据库元数据信息 

JDBC中Connection有一个方法getMetaData()方法，可以获的数据库的元数据信息。
```java
DatabaseMetaData meta = connection.getMetaData();
```

通过DatabaseMetaData可以获得数据库相关的信息如：数据库版本、数据库名、数据库厂商信息、是否支持事务、是否支持某种事务隔离级别，是否支持滚动结果集等。

DataBaseMetaData对象：
- **getURL()** 返回一个String类对象，代表数据库的URL。 
- **getUserName()** 返回连接当前数据库管理系统的用户名。 
- **getDatabaseProductName()** 返回数据库的产品名称。 
- **getDatabaseProductVersion()** 返回数据库的版本号。
- **getDriverName()** 返回驱动驱动程序的名称。 
- **getDriverVersion()** 返回驱动程序的版本号。 
- **isReadOnly()** 返回一个boolean值，指示数据库是否只允许读操作。

获取数据库的元数据信息:
```java
public static void main(String[] args) throws Exception {
    java.sql.Connection conn = JdbcUtils.getConnenction();
    DatabaseMetaData dbmd = conn.getMetaData();
    //获取使用的数据库是什么数据库  MySQL
    System.out.println("db name: " + dbmd.getDatabaseProductName());
    //是否支持事物  true
    System.out.println("tx: " + dbmd.supportsTransactions());
    conn.close();
}
```

## 参数的元数据信息

PreparedStatement.getParameterMetaData() 
获得代表PreparedStatement元数据的ParameterMetaData对象。 
ParameterMetaData对象：
- **getParameterCount()** 获得指定参数的个数
- **getParameterClassName()** 获得参数的类型，比如String等等
- **getParameterType() **
- **getParameterTypeName()** 获得参数对应数据库的数据类型，比如VARCHAR等等

获取ParameterMetaData元数据信息
```java
conn = JdbcUtils.getConnection();
ps = conn.prepareStatement(sql);
ParameterMetaData pmd = ps.getParameterMetaData();
int count = pmd.getParameterCount();
for (int i = 1; i <= params.length; i++) {
    System.out.print(pmd.getParameterClassName(i) + "\t");
    System.out.print(pmd.getParameterType(i) + "\t");
    System.out.println(pmd.getParameterTypeName(i));
    ps.setObject(i, params[i - 1]);
}
```

## 结果集元数据信息

结果集(ResultSet)是数据中查询结果返回的一种对象，可以说结果集是一个存储查询结果的对象，但是结果集并不仅仅具有存储的功能，他同时还具有操纵数据的功能，可能完成对数据的更新等。

**PreparedStatement中的方法**
```java
getMetaData ResultSetMetaData getMetaData() throws SQLException
```
获取包含有关 ResultSet 对象列信息的 `ResultSetMetaData` 对象，ResultSet 对象将在执行此 `PreparedStatement`对象时返回。

因为 `PreparedStatement` 对象被预编译，所以不必执行就可以知道它将返回的 ResultSet 对象。因此，可以对 `PreparedStatement` 对象调用 getMetaData 方法，而不必等待执行该对象，然后再对返回的 ResultSet 对象调用 `ResultSet.getMetaData` 方法。  

> 注：对于某些缺乏底层 DBMS 支持的驱动程序，使用此方法开销可能很大。 

**ResultSet中**
```java
getMetaData
ResultSetMetaData getMetaData() throws SQLException 
```
获取此 ResultSet 对象的列的编号、类型和属性。    
返回： 此 ResultSet 对象的列的描述 

获取ResultSetMetaData元数据信息:
```java
public List<Map<String, Object>> read(String sql,Object[] params){   
    try {    
	ps = conn.prepareStatement(sql);    
	for(int i=0;i<params.length;i++){     
	    ps.setObject(i+1, params[i]);     
	}     
	rs=ps.executeQuery();     
	ResultSetMetaData rsm=rs.getMetaData();    
	//获取查询结果有多少列     
	int count=rsm.getColumnCount();     
	//存储查询结果的列名     
	String[] strColumns=new String[rsm.getColumnCount()];    
	//循环遍历结果集       
	for(int i=1;i<=count;i++){ 
	    //返回构造其实例的 Java 类的完全限定名称//    
	    System.out.print(rsm.getColumnClassName(i)+"\t"); //   
	    //获取指定列的 SQL 类型,返回值为int类型  //    
	    System.out.print(rsm.getColumnType(i)+"\t"); //    
	    //获取指定列的数据库特定的类型名称。  //    
	    System.out.print(rsm.getColumnTypeName(i)+"\t"); //    
	    //获取指定列的名称。  //    
	    System.out.print(rsm.getColumnName(i)+"\t"); //       
	    /*获取用于打印输出和显示的指定列的建议标题。 
	    //     * 建议标题通常由 SQL AS 子句来指定。  //     
	    * 如果未指定 SQL AS，则从 getColumnLabel 返回的值将和 //     
	    *  getColumnName 方法返回的值相同。 */  //    
	    System.out.println(rsm.getColumnLabel(i));     
	    strColumns[i-1]=rsm.getColumnLabel(i);     
	}     
	List<Map<String, Object>> datas=new ArrayList<Map<String,Object>>();    
        while(rs.next()){     
	    Map<String, Object> data=new HashMap<String, Object>();     
	    for(int i=0;i<strColumns.length;i++){       
	        data.put(strColumns[i], rs.getObject(strColumns[i]));      
	    }      
	    datas.add(data);     
        }     
        return datas;    
    } catch (Exception e) {     
        e.printStackTrace();    
        return null;    
    }   
} 
```