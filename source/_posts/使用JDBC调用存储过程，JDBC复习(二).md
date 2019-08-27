---
title: '使用JDBC调用存储过程，JDBC复习(二) '
date: 2014-07-07 10:41:26
categories:
- 技术
tags:
- 后端
- Java
- jdbc
---

当不直接使用SQL语句,而是调用数据库中的Store Procedure时,要用到`Callable Statement `，`CallabelStatement`从`PreparedStatement`继承。
<!-- more -->
**案例一：编写一个过程，可以学生名，新年龄，可修改学生的年龄**
```sql
CREATE OR REPLACE PROCEDURE pro1(prname VARCHAR2,newage NUMBER) IS
BEGIN
   UPDATE tb_student SET age=newage WHERE name=prname;
END;
```
演示JAVA如何让调用Oracle的存储过程示例
```java
public class callOracleProTest {
    public static void main(String[] args) {
        try {
	    //1.加载驱动
	    Class.forName("oracle.jdbc.driver.OracleDriver");
			
	    //2.得到连接
	    Connection conn=DriverManager.getConnection("jdbc:oracle:thin:@localhost:1521:ORCL", "root", "root");
			
            //3.创建CallableStatment
	    CallableStatement call=conn.prepareCall("{call pro1(?,?)}");
			
	    //4.给？号赋值
	    call.setString(1,"小强");
	    call.setInt(2,30);
			
	    //5.执行
	    call.execute();
			
	    //6.关闭资源
	    call.close();
	    conn.close();
	} catch (Exception e) {
	    System.out.println("出错了，程序异常，可能没导入JDBC驱动jar文件哦！");
	    e.printStackTrace();
	}
    }
}
```

**案例二：有返回值的存储过程**

编写一个过程，可以输入学生的编号，返回该学生的地址，并用Java程序调用执行
```sql
CREATE OR REPLACE PROCEDURE pro2(v_ID IN NUMBER,v_address OUT VARCHAR2) IS
BEGIN
   SELECT address INTO v_address FROM tb_student WHERE ID=v_ID;
END;
```

演示JAVA如何让调用Oracle带返回值的存储过程示例
```java
public class callOracleReturnValueProcedure {
    public void returnOne(){
    try {
	Connection conn = getConnection();
		
	//3.创建CallableStatment
        CallableStatement call=conn.prepareCall("{call pro2(?,?)}");
		
	//4.给第一个？号赋值
	call.setInt(1,2);
	//4.给第二个？号赋值
	call.registerOutParameter(2,oracle.jdbc.OracleTypes.CHAR);
			
	//5.执行
	call.execute();
			
	//6.取出返回值，注意？号的顺序
	String address=call.getString(2);
	System.out.println("编号为2的学生的地址是："+address);
			
	//7.关闭资源
	call.close();
	conn.close();
    } catch (Exception e) {
	System.out.println("出错了，程序异常，可能没导入JDBC驱动jar文件哦！");
	e.printStackTrace();
    }
}
```

**案例三：有返回值的存储过程（返回多个值）**

编写一个过程，可以输入学生的编号，返回该学生的地址，年龄，性别，并用Java程序调用执行。
```sql
CREATE OR REPLACE PROCEDURE pro3(v_ID IN NUMBER,v_sex OUT varchar2,v_age OUT INT, v_address OUT VARCHAR2) IS
BEGIN
   SELECT sex,age,address INTO v_sex,v_age,v_address FROM tb_student WHERE ID=v_ID;
END;
```
我们在callOracleReturnValueProcedure类中继续添加方法。

演示有返回值的存储过程（返回多个值）:
```java
public void returnMore(){
    try {
	Connection conn=this.getConnection();
			
	CallableStatement call=conn.prepareCall("{call pro3(?,?,?,?)}");
	
	call.setInt(1,2);
	call.registerOutParameter(2,oracle.jdbc.OracleTypes.CHAR);
	call.registerOutParameter(3,oracle.jdbc.OracleTypes.INTEGER);
	call.registerOutParameter(4,oracle.jdbc.OracleTypes.CHAR);
			
	call.execute();
			
	String sex=call.getString(2);
	int age=call.getInt(3);
	String address=call.getString(4);
			
	System.out.println("编号为2的学生性别是："+sex);
	System.out.println("编号为2的学生年龄是："+age);
	System.out.println("编号为2的学生地址是："+address);
			
	call.close();
	conn.close();
    } catch (Exception e) {
	// TODO Auto-generated catch block
	e.printStackTrace();
    }
}
```

**案例四：返回结果集的存储过程**
```sql
CREATE OR REPLACE PROCEDURE pro4(v_type IN VARCHAR2,p_cur OUT mypackage.my_cur) IS
BEGIN
  OPEN p_cur FOR
     SELECT * FROM tb_prod WHERE TYPE=v_type;
END;
```

演示返回结果集的存储过程:
```java
public void returnResultSet(){
    try {
	Connection conn=this.getConnection();
	CallableStatement call=conn.prepareCall("{call pro4(?,?)}");
			
	call.setString(1,"笔记本");
	call.registerOutParameter(2,oracle.jdbc.OracleTypes.CURSOR);
			
	call.execute();
	//得到结果集
	ResultSet rs=(ResultSet)call.getObject(2);
	while(rs.next()){
    	    System.out.println("ID:"+rs.getInt(1)+" type:"+rs.getString(2)+" mark:"+rs.getString(3)+" spec:"+rs.getString(4));
	}
			
	call.close();
	conn.close();
    } catch (Exception e) {
	// TODO Auto-generated catch block
	e.printStackTrace();
    }
}
```

**案例五，编写一个分页的存储过程**

要求：输入表名，每页显示的记录数，当前页,返回总记录数、总页数、结果集
定义一个包，建立游标：
```sql
CREATE OR REPLACE PACKAGE mypackage AS TYPE my_cur IS REF CURSOR;
```
建立存储过程如下:
```java
CREATE OR REPLACE PROCEDURE fenye(
     tableName IN VARCHAR2,        --表名
     pageSize IN NUMBER,           --每页显示的记录数
     pageNow IN NUMBER,            --当前页
     rowsCount OUT NUMBER,         --总记录数
     pageCount OUT NUMBER,         --总页数
     page_cur OUT mypackage.my_cur --结果集的游标
) IS
--定义一个变量拼接分页SQL语句
v_sql VARCHAR2(1000);
--定义两个整数，计算起始页和终止页
v_begin NUMBER:=(pageNow-1)*pageSize+1;
v_end NUMBER:=pageNow*pageSize;
BEGIN
    --执行
    v_sql:='SELECT * FROM (SELECT t.*,ROWNUM rn FROM (SELECT * FROM '||tableName||') t WHERE ROWNUM<='||v_end||') WHERE rn>='||v_begin||;
    --打开游标，把游标和sql关联起来
    OPEN page_cur FOR v_sql;
    --组织一个SQL，计算总记录数(rowsCount)和总页数(pageCount)
    v_sql:='select count(*) from'||tableName;
    --执行SQL,并把返回的结果赋值给rowsCount(总记录数)
    EXECUTE IMMEDIATE v_sql INTO rowsCount;
    --计算总页数(pageCount)
    IF MOD(rowsCount,pageSize)=0 THEN
    	pageCount:=rowsCount/pageSize;
    ELSE
    	pageCount:=rowsCount/pageSize+1;
    END IF;
    --关闭游标
    --CLOSE page_cur;
END;
```

调用存储过程：
```java
public static void main(String[] args) {
    try {
	//1.加载驱动
	Class.forName("oracle.jdbc.driver.OracleDriver");
	//2.得到连接
	Connection conn=DriverManager.getConnection("jdbc:oracle:thin:@localhost:1521:ORCL", "scott", "scott");
			
	CallableStatement call=conn.prepareCall("{call fenye(?,?,?,?,?,?)}");
        //给输入参数?号赋值
	call.setString(1,"emp");	//查询哪个表
	call.setInt(2,5);			//要显示几页
	call.setInt(3,1);			//从第几页开始显示
	
	//给输出参数？号赋值
	call.registerOutParameter(4, oracle.jdbc.OracleTypes.INTEGER);	//接收总记录数
	call.registerOutParameter(5, oracle.jdbc.OracleTypes.INTEGER);	//接收总页数
	call.registerOutParameter(6, oracle.jdbc.OracleTypes.CURSOR);	//接收结果集
			
	call.execute();
			
	int rowCount=call.getInt(4);	//取出总记录数
        int pageCount=call.getInt(5);	//取出总页数
	ResultSet rs=(ResultSet)call.getObject(6);	//取出结果集
			
	//显示一下看对不对
	System.out.println("总记录数:"+rowCount);
	System.out.println("总页数:"+pageCount);
	while(rs.next()){
    	    System.out.println("编号:"+rs.getInt(1)+" 名字:"+rs.getString(2)+" 职位:"+rs.getString(3));	
        }
			
	call.close();
	conn.close();
    } catch (Exception e) {
	// TODO Auto-generated catch block
	e.printStackTrace();
    }		
}
```
