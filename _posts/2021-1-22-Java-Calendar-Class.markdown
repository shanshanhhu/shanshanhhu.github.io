---
title: Java Calendar Class
date: 2021-01-21 15:10:22 +0800
categories: [java, class]
tags: [class]
---

# java.util.Calendar
- 继承关系：\
java.lang.Object ---> java.util.Calendar ---> GregorianCalendar

- All implemented Interface : Serializable, Cloneable,
  Comparable<Calendar>


# methods
[fields and methods summary](https://docs.oracle.com/javase/7/docs/api/java/util/Calendar.html)
## initialization
> Calendar instance = Calendar.getInstance(); // get instance of current
> time

# How to determine Day of Week
## Day of Week as a Number
```
public static int getDayNumberOld(Date date) {
    Calendar cal = Calendar.getInstance();
    cal.setTime(date);
    return cal.get(Calendar.DAY_OF_WEEK);
}
```
## Day of Week as Text
```
public static String getDayStringOld(Date date, Locale locale) {
    DateFormat formatter = new SimpleDateFormat("EEEE", locale);
    return formatter.format(date);
}
```

# DateFormat
[Source](https://blog.csdn.net/cuiyaoqiang/article/details/90710250)
```
	/**
	 * %后的1指第一个参数，当前只有var一个可变参数，所以就是指var。
	 * $后的0表示，位数不够用0补齐，如果没有这个0（如%1$nd）就以空格补齐，
	 * 0后面的n表示总长度，总长度可以可以是大于9例如（%1$010d），d表示将var按十进制转字符串，长度不够的话用0或空格补齐。
	 */
	@Test
	public void myTest1(){
		System.out.println(String.format("%1$01d", 1));
		System.out.println(String.format("%1$03d", 1));
		System.out.println(String.format("我叫%s,她叫%s", "小明","小方"));
		System.out.println(String.format("我叫%2$s,她叫%1$s", "小明","小方"));
		System.out.println(String.format("%o", 8)); //转变为八进制 10
		System.out.println(String.format("%x", 16)); //转变为16进制 10
		System.out.println(String.format("%d", 10)); //转变为10进制 10
		System.out.println(String.format("%1$,d", 12302562)); // 金额千分位隔开12,302,562
		System.out.println(String.format("%,d", 12302562)); // 金额千分位隔开12,302,562
		System.out.println(String.format("%1$.2f", 12.12555));// f表示传入的数字是浮点型,用于四舍五入保留两位小数12.13
		//调用String.format()方法是会new一个Formatter对象
		System.out.println(new Date());//Fri May 31 08:43:32 CST 2019
		System.out.println(String.format("%1$tY-%1$tm-%1$td", new Date()));//日期转换 2019-05-31
		System.out.println(String.format("%1$tY-%1$tm-%1$td %1$tH:%1$tM:%1$tS", new Date()));//日期转换 2019-05-31 08:45:24
		System.out.println(String.format("%1$tY-%1$tm-%1$td %1$tH:%1$tM:%1$tS:%1$tL", new Date()));//日期转换 2019-05-31 08:45:24
		System.out.println(String.format("%1$tY-%1$tm-%1$td %1$tH:%1$tM:%1$tS %tA", new Date()));//日期转换 2019-05-31 08:49:52 星期五
		System.out.println(String.format("%1$tY-%1$tm-%1$td %1$tH:%1$tM:%1$tS:%1$tL %1$tA %1$tp", new Date()));//日期转换 2019-05-31 08:49:52:034 星期五 上午
		System.out.println(String.format("%1$tY-%1$tm-%1$td %1$tH:%1$tM:%1$tS:%1$tL %1$tA %1$tp %tZ", new Date()));//日期转换 2019-05-31 08:49:52:034 星期五 上午 CST
		System.out.println(String.format("%tF", new Date()));//日期转换 2019-05-31
		System.out.println(String.format("%tD", new Date()));//日期转换 05/31/19
		System.out.println(String.format("%tc", new Date()));//日期转换 星期五 五月 31 08:53:58 CST 2019
		System.out.println(String.format("%tr", new Date()));//日期转换 08:54:21 上午
		System.out.println(String.format("%tT", new Date()));//日期转换 08:54:38
		System.out.println(String.format("%tR", new Date()));//日期转换 08:54
	}
}

```
# usage
- abstract void add(int field, int amount)
```java
import java.util.*;
public class Calendar5 {
    public static void main(String[] args)
    {
        // creating calendar object
        Calendar calendar = Calendar.getInstance();
        calendar.add(Calendar.DATE, -15);
        System.out.println("15 days ago: " + calendar.getTime());
        calendar.add(Calendar.MONTH, 4);
        System.out.println("4 months later: " + calendar.getTime());
        calendar.add(Calendar.YEAR, 2);
        System.out.println("2 years later: " + calendar.getTime());
    }
}
```
- int get(int field)
```java
import java.util.*;
public class Calendar2 {
    public static void main(String[] args)
    {
        // creating Calendar object
        Calendar calendar = Calendar.getInstance();

        // Demonstrate Calendar's get()method
        System.out.println("Current Calendar's Year: " + calendar.get(Calendar.YEAR));
        System.out.println("Current Calendar's Day: " + calendar.get(Calendar.DATE));
        System.out.println("Current MINUTE: " + calendar.get(Calendar.MINUTE));
        System.out.println("Current SECOND: " + calendar.get(Calendar.SECOND));
    }
}
```
- abstract int getMaximum(int field)
- abstract int getMinimum(int field)
```java
import java.util.*;
public class Calendar3 {
    public static void main(String[] args)
    {
        // creating calendar object
        Calendar calendar = Calendar.getInstance();

        int max = calendar.getMaximum(Calendar.DAY_OF_WEEK);
        System.out.println("Maximum number of days in a week: " + max);

        int min = calendar.getMinimum(Calendar.DAY_OF_WEEK);
        System.out.println("Minimum number of days in week: " + min);
    }
}
```
- Date getTime()


```java
public class test {
    public static void main(String[] args) {
        Calendar instance = Calendar.getInstance();
        Long time = new Long("1611281534745");
        instance.setTimeInMillis(time);
        //Date date = instance.getTime();
        System.out.printf("Original Time : ");
        System.out.println(instance.getTime() + "\n");
        int minute = instance.get(Calendar.MINUTE);

        int i = minute;
        while (i % 5 != 0) {
            i++;
        }
        System.out.printf("minute : %d\n", i);
        instance.set(Calendar.MINUTE, i);
        instance.set(Calendar.SECOND, 0);
        instance.set(Calendar.MILLISECOND, 0);
        System.out.printf("Revised Time : ");
        System.out.println(instance.getTime() + "\n");

    }
}
```

