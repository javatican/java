---
layout:	post
title:	Java基礎 - Practice 4
date:	2017-01-03
categories: java
---
## 4-1 匯入既有的Java程式

練習使用NetBeans, 匯入既有的project, 編譯並執行.
```java
public class Quotation {
    public String quote="Welcome to Oracle, the new home of Java!";
    
    public void display(){
        System.out.println(quote);
    }
}
```

```java
public class QuotationTest {

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        Quotation q = new Quotation();
        q.display();
    }
    
}
```

## 4-2 建立Java class

利用NetBeans建立新的Java class, 編譯之. 再建立另一個Java class來測試之前的class.
```java
public class Shirt {

    /**
     * *****************************************
     * Attribute Variable Declaration Section
     *
     ******************************************
     */
    public int shirtID = 0; //Default ID for the shirt
    public String description = "-description required-";
    // The color codes are R=Red, B=Blue, G=Green
    public char colorCode = 'U';
    public double price = 0.0; //Default price for all shirts
    public int quantityInStock = 0;

    public void setPrice(double priceArg) {
        price = priceArg;
    }

    public void displayInformation() {
        System.out.println("Shirt ID: " + shirtID);
        System.out.println("Shirt description: " + description);
        System.out.println("Color Code: " + colorCode);
        System.out.println("Shirt price: " + price);
        System.out.println("Quantity in stock: " + quantityInStock);
    } // end of display method
}
```

```java
public class ShirtTest {

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        Shirt myshirt;
        myshirt = new Shirt();
        myshirt.displayInformation();
    }

}

```

## 4-3 使用Debugger

1. 建立中斷點(breakpoint)
1. debugger toolbar buttons:
  - Stop
  - Pause
  - Continue
  - Step Over
  - Step Over Expression
  - Step Into
  - Step Out
  - Run to Cursor
1. 按下Step Into按鈕, 進入到displayInformation()方法, 檢視Variables視窗, 嘗試改變每一個欄位的值.
1. 按下Step Out按鈕回到ShirtTest class

:sweat_smile:

