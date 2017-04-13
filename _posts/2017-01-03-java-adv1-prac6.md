---
layout:	post
title:	Java基礎 - Practice 6
date:	2017-01-05
categories: java
---
## 6-1 建立物件, 改變物件

```java
public class Customer {

    public int customerID = 0; // Default ID for a customer
    public String name;
    public String email;

    public void displayCustomerInfo() {
        System.out.println("********Customer Information********");
        System.out.println("Customer ID: " + customerID);
        System.out.println("Name: " + name);
        System.out.println("Email: " + email);
        System.out.println("************************************");
    }
}

```

```java
public class CustomerTest {

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        Customer cust1, cust2;
        cust1 = new Customer();
        cust2 = new Customer();
        cust1.customerID = 1;
        cust1.name="Mary Smith";
        cust1.email="mary.smith@gmail.com";
        cust1.displayCustomerInfo();
        //
        cust2.customerID = 2;
        cust2.name="Frank Jones";
        cust2.email="frank.jones@gmail.com";
        cust2.displayCustomerInfo();
    }

}
```

改變物件參考, 將一個物件參考指定給另一個物件參考:

```java
        cust2 = cust1;
        cust2.displayCustomerInfo();
```

## 6-2 使用String及[StringBuffer類別](https://docs.oracle.com/javase/7/docs/api/java/lang/StringBuilder.html)

1. String是immutable(唯讀的, 不可改變值)的字串物件.
1. StringBuilder是mutable(可以改變值)的字串物件, 其提供一些可以改變內容的方法, 例如:
  - append(): 從後面加上字串
  - insert(): 從某個索引位置加入字串
1. StringBuilder的其他一些方法:
  - toString(): 轉換成字串
  - capacity(): 取得內部容量值
  - substring(): 擷取子字串.

```java
public class PersonTwo {

    public StringBuilder name = new StringBuilder(8);
    public StringBuilder phoneNumber = new StringBuilder();

    public void displayPersonInfo() {
        name.append("Fernando");
        name.append(" ");
        name.append("Gonzalez");
        // Display the name object
        System.out.println("Name: " + name.toString());
        // Display the capacity
        System.out.println("Name object capacity: " + name.capacity());
        //
        phoneNumber.append("5551234567");
        phoneNumber.insert(3, "-");
        phoneNumber.insert(7, "-");
        System.out.println("Phone number: " + phoneNumber.toString());
        System.out.println("First name: "+ name.substring(0,8));
    }
}
```

```java
public class PersonTwoTest {

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        PersonTwo pt = new PersonTwo();
        pt.displayPersonInfo();
    }
    
}
```

## 6-3 查詢Java API說明文件

- 熟悉Java API文件的呈現方式
- 如何尋找類別, 方法
- 尋找Math類別, 旗下有幾個方法? 有幾個成員變數?
- 文件中是否可以找到所有類別的父類別?
- String類別中比較兩個字串是否相同的方法?

:sweat_smile:

