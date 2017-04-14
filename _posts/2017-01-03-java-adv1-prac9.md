---
layout:	post
title:	Java基礎 - Practice 9
date:	2017-01-08
categories: java
---
## 9-1 使用for迴圈

建立Counter類別, 使用for迴圈來列印一序列的數字

```java
public class Counter {

    public final int MAX_COUNT = 100;

    public void displayCount() {
        for (int i = 1; i <= MAX_COUNT; i++) {
            if (i % 12 == 0) {
                System.out.print(i + " ");
            }
        }
    }

}
```

```java
public class CounterTest {

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        Counter c = new Counter();
        c.displayCount();
    }
    
}
```

## 9-2 使用while迴圈

- 建立Sequence類別, 列印0 1 1 2 3 5 8 13 21...(前面兩個數字相加, 產生下一個數字), 所謂的費氏序列(Fibonacci series)
- 僅列印出100以下的數字

```java
public class Sequence {

    public int firstNumber = 0;
    public int secondNumber = 1;
    public int nextNumber;
    public final int SEQUENCE_LIMIT = 100;

    public void displaySequence() {
        System.out.print(firstNumber + " " + secondNumber + " ");
        nextNumber = firstNumber + secondNumber;
        while (nextNumber < SEQUENCE_LIMIT) {
            System.out.print(nextNumber + " ");
            firstNumber = secondNumber;
            secondNumber = nextNumber;
            nextNumber = firstNumber + secondNumber;
        }
        System.out.println();
    }

}
```

```java
public class SequenceTest {

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        Sequence s = new Sequence();
        s.displaySequence();
    }
    
}
```

## 9-3 將while迴圈轉換成for迴圈

- 使用for迴圈改寫9-2範例
- 列印出最前面的10個數字


```java
public class ChallengeSequence {

    public int firstNumber = 0;
    public int secondNumber = 1;
    public int nextNumber;
    public final int SEQUENCE_LIMIT = 100;
    public final int SEQUENCE_COUNT = 10;

    public void displaySequence() {
        System.out.print(firstNumber + " " + secondNumber + " ");
        nextNumber = firstNumber + secondNumber;
        for(int i=2; i<SEQUENCE_COUNT; i++){
            System.out.print(nextNumber + " ");
            firstNumber = secondNumber;
            secondNumber = nextNumber;
            nextNumber = firstNumber + secondNumber;
        }
        System.out.println();
    }

}
```
```java
public class ChallengeSequenceTest {
    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        ChallengeSequence s = new ChallengeSequence();
        s.displaySequence();
    }
    
}
```

## 9-4 使用for迴圈來處理ArrayList物件

- 使用傳統的for迴圈來顯示ArrayList物件中的values:

```java
import java.util.ArrayList;

public class VacationScaleTwo {
    public ArrayList vacationDays;
    public int yearsOfService;
    public void setVacationScale(){
        vacationDays = new ArrayList();
        vacationDays.add(10);
        vacationDays.add(15);
        vacationDays.add(15);
        vacationDays.add(15);
        vacationDays.add(20);
        vacationDays.add(20);
        vacationDays.add(25);
    }
    public void displayVacationDays(){
        for(int years=0; years<vacationDays.size(); years++){
            System.out.println("The vacation for " + years+" year of service is :"
            +vacationDays.get(years));
        }
        
    } 
}
```

```java
public class VacationScaleTwoTest {

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        VacationScaleTwo vst = new VacationScaleTwo();
        vst.setVacationScale();
        vst.displayVacationDays();
    }
    
}
```

- 使用`for-each迴圈`來顯示ArrayList物件中的values:

```java
import java.util.ArrayList;

public class NameListTwo {
    public ArrayList theList;

    public void setList() {
        theList = new ArrayList();
        theList.add("Joe Smith");
        theList.add("Mary Palmer");
        theList.add("Jose Gonzales");
        theList.add("Linda Baker");
        System.out.println("Names list: " + theList);
        System.out.println("Size of ArrayList: " + theList.size());
    }

    public void manipulateList() {
        theList.remove("Joe Smith");
        System.out.println("Names list after removing element: " + theList);
        System.out.println("Size of ArrayList: " + theList.size());
        theList.add(1, "Joe Smith");

        System.out.println("Names list after adding element: " + theList);
        System.out.println("Size of ArrayList: " + theList.size());

    }
    
    public void displayNames(){
        System.out.println("Names in the ArrayList:");
        for(Object name : theList){
            System.out.println(name);
        }
    }
}
```

```java
public class NameListTwoTest {

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        NameListTwo nlt= new NameListTwo();
        nlt.setList();
        nlt.displayNames();
    }
    
}
```

## 9-5 使用巢狀的for迴圈來處理二維陣列

一個教室裡有12張桌子, 放置在3列, 每列有4欄的格子裡. 然後指定學生從第一列, 第一欄開始, 坐到這些桌子去. 第一列坐滿後再坐到第二列去. 

```java
public class ClassMap {

    public String[][] deskArray;
    public String name;

    public void setClassMap() {
        deskArray = new String[3][4];
    }

    public void setDesk() {
        boolean flag = false;
        lab1:
        for (int row = 0; row < 3; row++) {
            for (int col = 0; col < 4; col++) {
                if (deskArray[row][col] == null) {
                    flag = true;
                    deskArray[row][col] = name;
                    System.out.println(name + " desk is at position : Row:"
                            + row + ", Column:" + col);
                    break lab1;
                }
            }
        }
        if (!flag) {
            System.out.println("All desk occupied.");
        }
    }

    public void displayDeskMap() {
        for (int row = 0; row < 3; row++) {
            for (int col = 0; col < 4; col++) {
                System.out.print(deskArray[row][col] + "\t");
            }
            System.out.println();
        }

    }
}
```

```java
public class ClassMapTest {

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        ClassMap cm = new ClassMap();
        cm.setClassMap();
        cm.name = "Ann";
        cm.setDesk();
        cm.name = "Bond";
        cm.setDesk();
        cm.name = "Cindy";
        cm.setDesk();
        cm.name = "Donald";
        cm.setDesk();
        cm.displayDeskMap();
    }

}
```

## 9-6 上述的ClassMap程式中加入search的方法

```java
public class ClassMap {

    public String[][] deskArray;
    public String name;

    public void setClassMap() {
        deskArray = new String[3][4];
    }

    public void setDesk() {
        boolean flag = false;
        lab1:
        for (int row = 0; row < 3; row++) {
            for (int col = 0; col < 4; col++) {
                if (deskArray[row][col] == null) {
                    flag = true;
                    deskArray[row][col] = name;
                    System.out.println(name + " desk is at position : Row:"
                            + row + ", Column:" + col);
                    break lab1;
                }
            }
        }
        if (!flag) {
            System.out.println("All desk occupied.");
        }
    }

    public void displayDeskMap() {
        for (int row = 0; row < 3; row++) {
            for (int col = 0; col < 4; col++) {
                System.out.print(deskArray[row][col] + "\t");
            }
            System.out.println();
        }
    }

    public void searchDesk() {
        boolean found = false;
        lab1:
        for (int row = 0; row < 3; row++) {
            for (int col = 0; col < 4; col++) {
                if (deskArray[row][col] != null && 
                        deskArray[row][col].equals(name)) {
                    System.out.println(name + " desk position : Row:"
                            + row + ", Column:" + col);
                    found = true;
                    break lab1;
                }
            }
        }
        if (!found) {
            System.out.println("Desk not allocated for "+name);
        }
    }
}
```

```java
public class ClassMapTest {

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        ClassMap cm = new ClassMap();
        cm.setClassMap();
        cm.name = "Ann";
        cm.setDesk();
        cm.name = "Bond";
        cm.setDesk();
        cm.name = "Cindy";
        cm.setDesk();
        cm.name = "Donald";
        cm.setDesk();
        cm.displayDeskMap();
        //
        cm.name = "Ann";
        cm.searchDesk();
        cm.name = "Tom";
        cm.searchDesk();
    }

}
```

:sweat_smile:

