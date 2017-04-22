---
layout:	post
title:	Java基礎 - Practice 8
date:	2017-01-07
categories: java
---
## 8-1 使用陣列元素為基本型態資料的一維陣列

- 使用一維陣列來儲存DukeChoice公司員工的年度休假日. 休假日乃根據員工在公司工作的年資來計算. 
- 使用陣列長度為7, 索引值範圍0到6. 在存取陣列時不能使用索引值大於或等於7. 

```java
public class VacationScale {
    public int[] vacationDays;
    public int yearsOfService;
    public void setVacationScale(){
        vacationDays = new int[7];
        vacationDays[0]=10;
        vacationDays[1]=15;
        vacationDays[2]=15;
        vacationDays[3]=15;
        vacationDays[4]=20;
        vacationDays[5]=20;
        vacationDays[6]=25; 
    }
    public void displayVacationDays(){
        if(yearsOfService>=6){
            System.out.println("Vacation days:"+vacationDays[6]);
        } else if(yearsOfService>=0){
            System.out.println("Vacation days:"+vacationDays[yearsOfService]);
        } else {
            System.out.println("Invalid years of service");
        }
    }
}
```

```java
public class VacationScaleTest {

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        VacationScale vs = new VacationScale();
        vs.setVacationScale();
        vs.yearsOfService=3;
        vs.displayVacationDays();
        vs.yearsOfService=4;
        vs.displayVacationDays();
        vs.yearsOfService=10;
        vs.displayVacationDays();
    }
    
}
```

## 8-2 使用ArrayList

- 建立`ArrayList`物件
- 利用`add()`把String物件加入到其中. 
- 利用`System.out.println()`, 列印ArrayList物件內容及其元素個數(`size()`)
- 利用`remove()`來移除某個元素. 
- 利用`add(int index, Object obj)`在某一個索引值位置放入新元素.

```java
import java.util.ArrayList;

public class NameList {

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
}
```

```java
public class NameListTest {

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        NameList nl = new NameList();
        nl.setList();
        nl.manipulateList();
    }
    
}
```

## 8-3 使用main()方法參數(args陣列)

寫一個猜數字的遊戲. 從命令列讀入一個整數的輸入值(1-5), 顯示是否猜對等訊息.
- 使用String中的compareTo()來判斷args[0]是否為"help", 如果是, 顯示如何執行程式的方式.
- 使用`(int) (Math.random() * 5) + 1`來產生1-5的整數亂數值.
- 使用`Integer.parseInt(args[0])`來將args[0]從String型態轉成int

```java
import java.util.Random;

public class GuessingGame {

    public static void main(String[] args) {
        int randomNum;
        int guess;
        if (args.length == 0 || args[0].compareTo("help") == 0) {
            System.out.println("java GuessingGame <a number between 1 and 5>");
        } else {
            randomNum = (int) (Math.random() * 5) + 1;
            guess = Integer.parseInt(args[0]);
            if (guess < 1 || guess > 5) {
                System.out.println("invalid argument (an integer between 1 and 5 expected!)");
            } else if (randomNum == guess) {
                System.out.println("Correct answer!");
            } else {
                System.out.println("Sorry. The number was " + randomNum + ". Try again!");
            }
        }
    }
}

```
:sweat_smile:

