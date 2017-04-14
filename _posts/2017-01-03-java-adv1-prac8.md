---
layout:	post
title:	Java基礎 - Practice 8
date:	2017-01-07
categories: java
---
## 8-1 使用基本型態一維陣列

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

