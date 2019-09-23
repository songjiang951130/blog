---
title: 高精度计算 BigDecimal
date: '2019-09-09'
categories:
  - java
tags:
  - java
---

## 实例化问题
不能用double进行实例化，同样会出现精度丢失的问题

错误示例比如
```java
//正确结果 1000.033296
BigDecimal unitPrice = new BigDecimal(1.1644);
BigDecimal count = new BigDecimal(858.84);
System.out.println(unitPrice.multiply(count)+" \n "+1.1644*858.84);
//输出
//1000.0332960000001243302278908231560819935043394265724464066436016906891381950117647647857666015625 
//1000.0332960000002
```

正确做法：
```java
BigDecimal unitPrice = new BigDecimal("1.1644");
BigDecimal count = new BigDecimal("858.84");
System.out.println(unitPrice.multiply(count)+" "+1.1644*858.84);
```


## 基本方法
 - 加：add  
 - 减：subtract
 - 乘：multiply
 - 除：divide
特别说明一下除法，在除不尽（无限循环或者无限不循环的时候需要设置精度，保留到多少位以及最后一位的处理方式）  
否则会出现异常
> java.lang.ArithmeticException: Non-terminating decimal expansion; no exact representable decimal result.

```java
BigDecimal dividend = new BigDecimal("1");
BigDecimal negativeDividend = new BigDecimal("-1");
BigDecimal divisor = new BigDecimal("6");

System.out.println(dividend.divide(divisor,4,BigDecimal.ROUND_DOWN));
System.out.println(dividend.divide(divisor,4,BigDecimal.ROUND_UP));
System.out.println(dividend.divide(divisor,4,BigDecimal.ROUND_CEILING));
System.out.println(negativeDividend.divide(divisor,4,BigDecimal.ROUND_CEILING));
System.out.println(dividend.divide(divisor,4,BigDecimal.ROUND_FLOOR));
System.out.println(negativeDividend.divide(divisor,4,BigDecimal.ROUND_FLOOR));
```

原来值 | 模式|结果|
---|---|---|
1.666666 | ROUND_DOWN (舍弃后面的)| 1.6666
1.666600001 | ROUND_UP (后面任意位有数直接进)| 1.6667
1.666666 | ROUND_CEILING （结果为正数，效果同ROUND_UP）| 1.6667
-1.666666 | ROUND_CEILING （结果为负数，效果同ROUND_DOWN）| -1.6666
1.666666 | ROUND_FLOOR （结果为正数，效果同ROUND_DOWN）| 1.6666
-1.666666 | ROUND_FLOOR （结果为负数，效果同ROUND_UP）| -1.6667

//其他的待补充 懒了 四舍五入之类的
