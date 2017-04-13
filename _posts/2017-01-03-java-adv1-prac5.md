---
layout:	post
title:	Java基礎 - Practice 5
date:	2017-01-04
categories: java
---
## 5-1 類別中宣告成員變數

```java
public class Customer {
    public int customerID = 0; // Default ID for a customer
    public char status = 'N'; // Default status for a customer
    public double totalPurchases = 0.0; // Default totalPurchases for a customer

    public void displayCustomerInfo(){
        System.out.println("Customer ID: " + customerID); 
        System.out.println("Customer Status: " + status); 
        System.out.println("Customer totalPurchase: " + totalPurchases);    
    }
}

```

```java
public class CustomerTest {

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        Customer c = new Customer();
        c.displayCustomerInfo();
    }

}

```
## 5-2 練習使用運算子及資料轉型

```java
public class Person {

    public int ageYears = 1, ageDays;
    public long ageMinutes, ageSeconds, ageMilliseconds;

    public void calculateAge() {
        ageDays = ageYears * 365;
        ageSeconds = (long)ageDays * 24 * 60 * 60; //1  
        ageMinutes = ageSeconds / 60;  
        ageMilliseconds = ageSeconds * 1000;
        System.out.println("You are " + ageDays + " days old.");
        System.out.println("You are " + ageMinutes
                + " minutes old.");
        System.out.println("You are " + ageSeconds
                + " seconds old.");
        System.out.println("You are " + ageMilliseconds
                + " milliseconds old.");
    }
}
```
註解:
1. 這裡在計算ageSeconds時必須將ageDays先轉型成long, 否則`ageDays * 24 * 60 * 60`會以int的型態進行計算, 
計算結果如過超過int的最大值, 則會發生overflow(溢位).

```java
public class PersonTest {

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        Person p = new Person();
        p.calculateAge();
        p.ageYears=24;
        p.calculateAge();
        p.ageYears=80;
        p.calculateAge();
    }
    
}
```

```java
public class Order {

    public long orderValue = 0L;
    public int itemQuantity = 10_000_000;
    public int itemPrice = 555_500;

    public void calculateTotal() {
        orderValue =  (long)itemQuantity *  itemPrice;
        System.out.println("Order total: " + orderValue);
    }
}
```
註解:
1. 這裡在計算orderValue時必須將itemQuantity先轉型成long, 否則`itemQuantity *  itemPrice`會以int的型態進行計算, 
計算結果如過超過int的最大值, 則會發生overflow(溢位).

```java
public class OrderTest {

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        Order o= new Order();
        o.calculateTotal();
    }
    
}

```

```java
public class Temperature {

    public float fahrenheitTemp = 78.9F;

    public void calculateCelsius() {
        System.out.println((fahrenheitTemp - 32) * 5 / 9);
    }
}
```

```java
public class TemperatureTest {

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        Temperature t = new Temperature();
        t.calculateCelsius();
    }
    
}
```
:sweat_smile:

