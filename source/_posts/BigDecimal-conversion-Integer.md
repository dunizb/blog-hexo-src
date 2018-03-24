---
title: BigDecimal互转Integer
date: 2013-10-26 20:57:51
categories:
- 服务端开发
- Java
tags:
- javase
- 备忘
---

## 一、Integer类型

```java
Integer a=new Integer(int value); 
Integer a=new Integer(String value);
```
<!-- more -->
**转换：**
1. 定义中就可以将int型和String型的转换为Integer型  
2. String类型转换为Integer型
```java
Integer.valueOf("");
Integer.getInteger("");
```

3. String、Integer类型转换为int型
```java
Integer.parseInt("");
Integer a;
a.intValue();
```

4. 上面定义的Integer a转换为float, double, long
```java
a.floatValue();
a.doubleValue();
a.longValue();
```

5. Integer a转换为String(其它的类型转换为String都可通用以下方法)
```java
toString();
String.valueOf(a);
```

**比较（比较的数Integer a）：**

1. `Int num=a.compareTo(Integer anotherInteger);`

如果该 Integer 等于 Integer 参数，则返回 0 值;如果该 Integer 在数字上小于 Integer 参数，则返回小于 0 的值;如果 Integer 在数字上大于 Integer 参数，则返回大于 0 的值(有符号的比较)。

2. 转换为int型再比较
a.intValue()与b.intValue比较大小；

## 二、BigDecimal

```java
BigDecimal a=new BigDecimal(String; val)
BigDecimal a=new BigDecimal(double val);
```

**转换：**
1. 定义中就可以将String型和double 型的转换为BigDecimal型
2. `Int,float, double, long转换为BigDecimal`
```java
a.floatValue();
a.doubleValue();
a.longValue();
a.intValue();
```

3. BigDecimal a转换为String(其它的类型转换为String都通用以下方法)
```java
toString();
String.valueOf(a);
```

**比较(比较的数BigDecimal a)**

1. `Int num=a.compareTo(BigDecimal anotherBigDecimal);`
当此BigDecimal在数字上小于、等于或大于 val 时，返回 -1、0 或 1。  
BigDecimal取其中最大、最小值、绝对值、相反数:
```java
当此BigDecimal在数字上小于、等于或大于 val 时，返回 -1、0 或 1。
BigDecimal取其中最大、最小值、绝对值、相反数:
```

计算：
```java
加: a.add(b);
减: a.subtract(b);
乘: a.multiply(b);
除: a.divide(b,2);//2为精度取值
```

int 、long、double、 float的取绝对值和同类型间比较大小都可用以下Math方法：
```java
Math.max(a,b);//比较取最大值
Math.min(a,b);//比较取最小值
Math.abs(a); //取最绝对值
```


2. 
