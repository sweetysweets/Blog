---
title: java中Date相关的总结
date: 2019-04-03 10:49:59
tags:
- Java
---

继昨天被嘲讽没用过LocalDateTime之后今天调试发现一个bug，输入2019-4-4 12:00:00 会变为 2019-4-5 0:0:0，引起的原因是SimpleDateFormat的格式问题……所以趁此机会总结一下吧23333333

> 关于获取时间的一些类，如java.util.Date, java.util.Calendar都不是线程安全的
> 还有就是对时间格式化时,DateFormat和SimpleDateFormat也不是线程安全的~

<!--more-->

## SimpleDateFormat

==要注意HH和hh的区别，HH是24小时制，hh是12小时制== 

如果要加上上午或下午标志符是a，若不想显示‘上午’、‘下午’，而是想显示AM、PM的话，需要加上

```java
SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss a", Locale.ENGLISH);
```

```java
/**
          SimpleDateFormat函数语法：
      
          G 年代标志符
          y 年
          M 月
          d 日
          h 时 在上午或下午 (1~12)
          H 时 在一天中 (0~23)
          m 分
          s 秒
          S 毫秒
          E 星期
          D 一年中的第几天
          F 一月中第几个星期几
          w 一年中第几个星期
          W 一月中第几个星期
          a 上午 / 下午 标记符
          k 时 在一天中 (1~24)
          K 时 在上午或下午 (0~11)
          z 时区
         */
 SimpleDateFormat aDate=new SimpleDateFormat("yyyy-mm-dd  HH:mm:ss");
        SimpleDateFormat bDate=new SimpleDateFormat("yyyy-mmmmmm-dddddd");
			bDate.setTimeZone(TimeZone.getTimeZone("GMT+8"));  //设置东八区
        long now=System.currentTimeMillis();
        System.out.println(aDate.format(now));
        System.out.println(bDate.format(now));
        
         SimpleDateFormat myFmt=new SimpleDateFormat("yyyy年MM月dd日 HH时mm分ss秒");
            SimpleDateFormat myFmt1=new SimpleDateFormat("yy/MM/dd HH:mm");
            SimpleDateFormat myFmt2=new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");//等价于now.toLocaleString()
            SimpleDateFormat myFmt3=new SimpleDateFormat("yyyy年MM月dd日 HH时mm分ss秒 E ");
            SimpleDateFormat myFmt4=new SimpleDateFormat(
                    "一年中的第 D 天 一年中第w个星期 一月中第W个星期 在一天中k时 z时区");
         System.out.println(myFmt.format(now));
         System.out.println(myFmt1.format(now));
         System.out.println(myFmt2.format(now));
         System.out.println(myFmt3.format(now));
         System.out.println(myFmt4.format(now));
      

```

> 2017-10-17  11:10:21
> 2017-000010-000017
> 2017年10月17日 11时10分21秒
> 17/10/17 11:10
> 2017-10-17 11:10:21
> 2017年10月17日 11时10分21秒 星期二 
> 一年中的第 290 天 一年中第42个星期 一月中第3个星期 在一天中11时 CST时区

1.DateFormat 可以直接使用，但其本身是一个抽象类，可以根据Locate指定的区域得到对应的日期时间格式

2.SimpleDateFormat 类是DateFormat 类的子类，一般情况下来讲 DateFormat 类很少会直接使用。而都使用SimpleDateFormat 类完成。

## LocalDateTime

With Java 8, a new time API is introduced, namely the java.time.LocalDate etc., but java.util.Date is not marked as deprecated.

I am writing a new project, which does not need to be backwards compatible. Should I only use LocalDate, LocalDateTime etc.? Are there any drawbacks to using this new API as opposed to the good old java.util.Date?

I would definitely use the new API because of greater features:

- Easier format/parsing. The API has its own format/parse methods
- The API includes addition/subtraction operation (minusMinutes, plusDays, etc)

## Java.util.Date
包含年、月、日、时、分、秒信息。

// String转换为Date
String dateStr="2013-8-13 23:23:23";
String pattern="yyyy-MM-dd HH:mm:ss";
DateFormate dateFormat=new SimpleDateFormat(pattern);
Date date=dateFormat.parse(dateStr);
date=dateFormat.format(date);
## Java.sql.Date
包含年、月、日信息。

继承自java.util.Date。在数据库相关操作中使用，如rs.getDate，ps.setDate等。rs是指ResultSet，ps是指PreparedStatement。

## java.util.Date转换为java.sql.Date
new java.sql.Date(utilDate.getTime());// 其中utilDate为java.util.Date类型的对象
##  Java.util.Calendar
包含年、月、日、时、分、秒、毫秒信息。

JDK1.1引入，用以代替java.util.Date。


// Date转为Calendar
Date date=new Date();
Calendar calendar=Calendar.getInstance();
calendar.setTime(date);

// Calendar转为Date
Calendar ca=Calendar.getInstance();  
Date d =(Date) ca.getTime();
##  Java.sql.Timestamp
包含年、月、日、时、分、秒、纳秒（nano）信息。

继承自java.util.Date。比java.sql.Date包含更多信息。在数据库相关操作中使用，如rs.getTimestamp，ps.setTimeStamp等。例如：若数据库中某字段hireDate为Oracle的Date类型，则使用getTimestamp时能够将年、月、日、时、分、秒信息取出；但使用getDate时则只能取出年、月、日信息。因此，一般推荐使用getTimestamp。

// java.util.Calendar转换为java.sql.Timestamp
new Timestamp(Calendar.getInstance().getTimeInMillis());
// java.util.Date转换为java.sql.Timestamp
new Timestamp(date.getTime());
// String转换为java.sql.Timestamp，String格式：yyyy-mm-dd hh:mm:ss[.f...] ，方括号表示可选
Timestamp.valueOf("2013-07-06 01:49:30");
5 Oracle数据库提供的日期和时间类型
Oracle数据库提供了DATE，TIMESTAMP，TIMESTAMP WITH TIME ZONE和TIMESTAMP WITH LOCAL TIME ZONE四种类型。

DATE包含世纪、年、月、日、时、分、秒信息。

TIMESTAMP是DATE的扩展，包含年、月、日、时、分、秒和fractional seconds信息。定义TIMESTAMP的格式如下：

TIMESTAMP [(fractional_seconds_precision)]
// 格式
TIMESTAMP 'YYYY-MM-DD HH24:MI:SS.FF'
// 一个例子
TIMESTAMP '1997-01-31 09:26:50.12'
其中fractional_seconds_precision是可选的，用于指定秒使用含几位小数的浮点数表示，它的取值范围是0到9，默认是6。上述例子中表示采用两位小数，它的秒值是50.12。注意：12不是毫秒值，也不是微秒值。