---
layout:	post
title:	Java基礎 - Practice 7
date:	2017-01-06
categories: java
---
## 7-1 使用if/else 區塊敘述

這個練習分成兩的部份. 第一部份將使用if/else區塊敘述, 根據一個變數dayNumber(範圍從1到7)的值, 列印星期一到星期日.

```java
public class DateTwo {

    public int dayNumber;

    public void displayDay() {
        if (dayNumber == 1) {
            System.out.println("星期一");
        } else if (dayNumber == 2) {
            System.out.println("星期二");
        } else if (dayNumber == 3) {
            System.out.println("星期三");
        } else if (dayNumber == 4) {
            System.out.println("星期四");
        } else if (dayNumber == 5) {
            System.out.println("星期五");
        } else if (dayNumber == 6) {
            System.out.println("星期六");
        } else if (dayNumber == 7) {
            System.out.println("星期日");
        } else {
            System.out.println("錯誤");
        }
    }
}
```

```java
public class DateTwoTest {

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        DateTwo dt = new DateTwo();
        dt.dayNumber=3;
        dt.displayDay();
    }
    
}
```

第二部份同樣使用if/else區塊, 根據時間值來顯示早上, 下午, 晚上或凌晨.

1. 時間以整數的方式指定, 例如400代表04:00, 1505代表15:05
1. 不需要檢核時間是否在1-2400之外.
1. 因為0開頭的數字為8位元表示法, 所以0800不是正確的整數形式, 必須以800形式指定.

```java
public class Clock {

    public int currentTime;

    public void displayPartOfDay() {
        if (currentTime >= 801 && currentTime <= 1200) {
            System.out.println("早上");
        } else if (currentTime >= 1201 && currentTime <= 1700) {
            System.out.println("下午");
        } else if (currentTime >= 1701 && currentTime <= 2400) {
            System.out.println("晚上");
        } else {
            System.out.println("凌晨");
        }
    }
}
```

```java
public class ClockTest {

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        Clock c = new Clock();
        c.currentTime=800; //不能使用0800
        c.displayPartOfDay();
        c.currentTime=1400;
        c.displayPartOfDay();
    }
    
}
```

## 7-2 使用switch 區塊敘述

利用switch case區塊敘述, 根據一個整數變數monthNumber的值(1到12), 列印1月到12月.

```java
public class Month {
    public int monthNumber;
    public void displayMonth(){
        switch(monthNumber){
            case 1:
                System.out.println("一月");
                break;
            case 2:
                System.out.println("二月");
                break;
            case 3:
                System.out.println("三月");
                break;
            case 4:
                System.out.println("四月");
                break;
            case 5:
                System.out.println("五月");
                break;
            case 6:
                System.out.println("六月");
                break;
            case 7:
                System.out.println("七月");
                break;
            case 8:
                System.out.println("八月");
                break;
            case 9:
                System.out.println("九月");
                break;
            case 10:
                System.out.println("十月");
                break;
            case 11:
                System.out.println("十一月");
                break;
            case 12:
                System.out.println("十二月");
                break;
            default:
                System.out.println("錯誤");
        } // end of switch
    }
}
```

```java
public class MonthTest {

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        Month m = new Month();
        m.monthNumber=8;
        m.displayMonth();
    }
    
}
```
:sweat_smile:

