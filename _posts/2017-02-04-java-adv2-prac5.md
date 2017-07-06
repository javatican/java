---
layout:	post
title:	Java進階 - Practice 5
date:	2017-02-04
categories: java
---

練習使用abstract, final, static等Java keywords, 以及辨識巢狀類別.

## 5-1 抽象類別

實作一個銀行帳戶的邏輯. 帳戶包含兩種(1)定存帳戶(time deposit account),(2)支票帳戶(checking account)

### 步驟一: 新增抽象類別Account

1. 將原本在TimeDepositAccount中的balance, getBalance(), deposit(), toString()移到Account類別中
1. 新增抽象方法: 
  - public abstract String getDescription();
  - public abstract boolean withdraw(double amount);
1. protected 成員變數: 
  - protected double balance;
1. Account 建構子:
  -public Account(double balance)

```java
package com.example;

public abstract class Account {

    protected double balance;

    public Account(double balance) {
        this.balance = balance;
    }

    public double getBalance() {
        return balance;
    }

    public void deposit(double amount) {
        balance += amount;
    }

    @Override
    public String toString() {
        return getDescription() + ": current balance is " + balance;
    }

    public abstract boolean withdraw(double amount);

    public abstract String getDescription();

}
```

### 步驟二: 更新TimeDepositAccount類別
1. TimeDepositAccount繼承Account
1. TimeDepositAccount建構子呼叫Account建構子
  - super(balance)
1. 覆蓋 withdraw() 及 getDescription()方法

```java
package com.example;

import java.util.Date;

public class TimeDepositAccount extends Account {

    private final Date maturityDate;

    public TimeDepositAccount(double balance, Date maturityDate) {
        super(balance);
        this.maturityDate = maturityDate;
    }

    @Override
    public boolean withdraw(double amount) {
        Date today = new Date();
        if (today.after(maturityDate)) {
            if (amount <= balance) {
                balance -= amount;
                return true;
            } else {
                return false;
            }
        } else {
            return false;
        }
    }

    @Override
    public String getDescription() {
        return "Time Deposit Account " + maturityDate;
    }

}
```

### 步驟三: 更新 Customer, CustomerReport類別

1. 原本Customer, CustomerReport類別中使用的TimeDepositAccount參考, 都改為使用Account參考

```java
package com.example;

public class Customer {

    private String firstName;
    private String lastName;
    private Account[] accounts;
    private int numberOfAccounts;

    public Customer(String f, String l) {
        firstName = f;
        lastName = l;
        // initialize accounts array
        accounts = new Account[10];
        numberOfAccounts = 0;
    }

    public String getFirstName() {
        return firstName;
    }

    public String getLastName() {
        return lastName;
    }

    public void addAccount(Account acct) {
        int i = numberOfAccounts++;
        accounts[i] = acct;
    }

    public int getNumOfAccounts() {
        return numberOfAccounts;
    }

    public Account getAccount(int account_index) {
        return accounts[account_index];
    }
}
```

```java
package com.example;

public class CustomerReport {

    private Bank bank;

    public Bank getBank() {
        return bank;
    }

    public void setBank(Bank bank) {
        this.bank = bank;
    }

    public void generateReport() {

        // Print report header
        System.out.println("\t\t\tCUSTOMERS REPORT");
        System.out.println("\t\t\t================");

        // For each customer...
        for (int cust_idx = 0; cust_idx < bank.getNumOfCustomers(); cust_idx++) {
            Customer customer = bank.getCustomer(cust_idx);

            // Print the customer's name
            System.out.println();
            System.out.println("Customer: "
                    + customer.getLastName() + ", "
                    + customer.getFirstName());

            // For each account for this customer...
            for (int acct_idx = 0; acct_idx < customer.getNumOfAccounts(); acct_idx++) {
                Account account = customer.getAccount(acct_idx);

                // Print the current balance of the account
                System.out.println("    " + account);
            }
        }
    }
}
```

### 步驟四: 新增CheckingAccount類別

1. 新增CheckingAccount類別, 繼承Account
1. 新增overDraftLimit 成員變數
  - private final double overDraftLimit;
1. 新增建構子:
  - public CheckingAccount(double balance, double overDraftLimit)
  - public CheckingAccount(double balance), 設定overDraftLimit為0.
1. 覆蓋getDescription(), withdraw()方法

```java
package com.example;

public class CheckingAccount extends Account {

    private final double overDraftLimit;

    public CheckingAccount(double balance) {
        this(balance, 0);
    }

    public CheckingAccount(double balance, double overDraftLimit) {
        super(balance);
        this.overDraftLimit = overDraftLimit;
    }

    @Override
    public boolean withdraw(double amount) {
        if (amount <= balance + overDraftLimit) {
            balance -= amount;
            return true;
        } else {
            return false;
        }
    }

    @Override
    public String getDescription() {
        return "Checking Account";
    }

}

```

### 步驟五: 更新AbstractBankingMain類別

1. 產生CheckingAccount物件

```java
package com.example;

import java.util.Calendar;

public class AbstractBankingMain {

    public static void main(String[] args) {
        Bank bank = new Bank();
        initializeCustomers(bank);

        // run the customer report
        CustomerReport report = new CustomerReport();
        report.setBank(bank);
        report.generateReport();
    }

    private static void initializeCustomers(Bank bank) {
        Customer customer;

        //180 day term
        Calendar cal = Calendar.getInstance();
        cal.add(Calendar.DAY_OF_YEAR, 180);

        // Create several customers and their accounts
        bank.addCustomer("Jane", "Simms");
        customer = bank.getCustomer(0);
        customer.addAccount(new TimeDepositAccount(500.00, cal.getTime()));
        customer.addAccount(new CheckingAccount(200.00, 400.00));

        bank.addCustomer("Owen", "Bryant");
        customer = bank.getCustomer(1);
        customer.addAccount(new CheckingAccount(200.00));

        bank.addCustomer("Tim", "Soley");
        customer = bank.getCustomer(2);
        customer.addAccount(new TimeDepositAccount(1500.00, cal.getTime()));
        customer.addAccount(new CheckingAccount(200.00));

        bank.addCustomer("Maria", "Soley");
        customer = bank.getCustomer(3);
        // Maria and Tim have a shared checking account
        customer.addAccount(bank.getCustomer(2).getAccount(1));
        customer.addAccount(new TimeDepositAccount(150.00, cal.getTime()));
    }
}

```

## 5-2 使用Singleton Design Pattern

利用static 及 final keywords 來限制Bank類別只能產生一個Bank物件.

### 步驟一: 更新Bank類別, 實作Singleton Design Pattern
1. 改變Bank建構子為private
1. 新增靜態成員變數 instance, 型態為Bank
  - private static final Bank instance = new Bank();
1. 新增static的getInstance()方法, 回傳instance成員變數

```java
package com.example;

public class Bank {

    private static final Bank instance = new Bank();

    private Customer[] customers;

    private int numberOfCustomers;

    private Bank() {
        customers = new Customer[10];
        numberOfCustomers = 0;
    }

    public static Bank getInstance() {
        return instance;
    }

    public void addCustomer(String f, String l) {
        int i = numberOfCustomers++;
        customers[i] = new Customer(f, l);
    }

    public int getNumOfCustomers() {
        return numberOfCustomers;
    }

    public Customer getCustomer(int customer_index) {
        return customers[customer_index];
    }
}
```

### 步驟二: 更新SingletonBankingMain類別
1. 將原本呼叫Bank建構子的敘述都改成呼叫getInstance()方法
1. 建立第二個Bank參考bank2, 並且呼叫getInstance()做為其初始值 
1. 利用 == 判斷bank與bank2兩個參考是否參考到同一個物件

```java
package com.example;

import java.util.Calendar;

public class SingletonBankingMain {

    public static void main(String[] args) {
        Bank bank = Bank.getInstance();
        Bank bank2 = Bank.getInstance();

        if (bank == bank2) {
            System.out.println("bank and bank2 are the same object");
        }

        initializeCustomers(bank2);

        // run the customer report
        CustomerReport report = new CustomerReport();
        report.setBank(bank);
        report.generateReport();
    }

    private static void initializeCustomers(Bank bank) {
        Customer customer;

        //180 day term
        Calendar cal = Calendar.getInstance();
        cal.add(Calendar.DAY_OF_YEAR, 180);

        // Create several customers and their accounts
        bank.addCustomer("Jane", "Simms");
        customer = bank.getCustomer(0);
        customer.addAccount(new TimeDepositAccount(500.00, cal.getTime()));
        customer.addAccount(new CheckingAccount(200.00, 400.00));

        bank.addCustomer("Owen", "Bryant");
        customer = bank.getCustomer(1);
        customer.addAccount(new CheckingAccount(200.00));

        bank.addCustomer("Tim", "Soley");
        customer = bank.getCustomer(2);
        customer.addAccount(new TimeDepositAccount(1500.00, cal.getTime()));
        customer.addAccount(new CheckingAccount(200.00));

        bank.addCustomer("Maria", "Soley");
        customer = bank.getCustomer(3);
        // Maria and Tim have a shared checking account
        customer.addAccount(bank.getCustomer(2).getAccount(1));
        customer.addAccount(new TimeDepositAccount(150.00, cal.getTime()));
    }
}
```


## 5-3 使用Enumeration

使用enum來替TimeDepositAccount類別中的到期日(maturityDate)成員變數增加設定限制, 只允許90及180天兩種選項.

### 步驟一: 新增DepositLength enum
1. 宣告days的成員變數
1. 新增private 建構子
  - private DepositLength(int days)
1. 新增public int getDays()方法
1. 建立兩個DepositLength enum實體, 建立時須呼叫建構子, 並傳入days參數, 分別為90, 180
  - THREE_MONTHS
  - SIX_MONTHS

```java
package com.example;

public enum DepositLength {
    THREE_MONTHS(90),
    SIX_MONTHS(180);

    private int days;

    private DepositLength(int days) {
        this.days = days;
    }

    public int getDays() {
        return days;
    }
}
```
### 步驟二: 更新TimeDepositAccount
1. 更新建構子, 改成接受DepositLength物件

```java
package com.example;

import java.util.Calendar;
import java.util.Date;

public class TimeDepositAccount extends Account {

    private final Date maturityDate;

    public TimeDepositAccount(double balance, DepositLength duration) {
        super(balance);
        Calendar cal = Calendar.getInstance();
        cal.add(Calendar.DAY_OF_YEAR, duration.getDays());
        this.maturityDate = cal.getTime();
    }

    @Override
    public boolean withdraw(double amount) {
        Date today = new Date();
        if (today.after(maturityDate)) {
            if (amount <= balance) {
                balance -= amount;
                return true;
            } else {
                return false;
            }
        } else {
            return false;
        }
    }

    @Override
    public String getDescription() {
        return "Time Deposit Account " + maturityDate;
    }

}
```


### 步驟三: 更新EnumBankingMain類別
1. 在呼叫TimeDepositAccount建構子時, 使用DepositLength.SIX_MONTHS作為參數

```java
package com.example;

public class EnumBankingMain {

    public static void main(String[] args) {
        Bank bank = Bank.getInstance();
        Bank bank2 = Bank.getInstance();

        if (bank == bank2) {
            System.out.println("bank and bank2 are the same object");
        }

        initializeCustomers(bank2);

        // run the customer report
        CustomerReport report = new CustomerReport();
        report.setBank(bank);
        report.generateReport();
    }

    private static void initializeCustomers(Bank bank) {
        Customer customer;

        // Create several customers and their accounts
        bank.addCustomer("Jane", "Simms");
        customer = bank.getCustomer(0);
        customer.addAccount(new TimeDepositAccount(500.00, DepositLength.SIX_MONTHS));
        customer.addAccount(new CheckingAccount(200.00, 400.00));

        bank.addCustomer("Owen", "Bryant");
        customer = bank.getCustomer(1);
        customer.addAccount(new CheckingAccount(200.00));

        bank.addCustomer("Tim", "Soley");
        customer = bank.getCustomer(2);
        customer.addAccount(new TimeDepositAccount(1500.00, DepositLength.SIX_MONTHS));
        customer.addAccount(new CheckingAccount(200.00));

        bank.addCustomer("Maria", "Soley");
        customer = bank.getCustomer(3);
        // Maria and Tim have a shared checking account
        customer.addAccount(bank.getCustomer(2).getAccount(1));
        customer.addAccount(new TimeDepositAccount(150.00, DepositLength.THREE_MONTHS));
    }
}
```

## 5-4 辨識巢狀類別
1. 對於原始檔OuterClass.java 
  - 總共定義了幾個類別?
  - 有幾個最上層的外部類別(top-level class)?
  - 有幾個巢狀類別(包含static nested class及non-static nested class)?
  - 有幾個內部類別(Inner class 也就是non-static nested class)?
  - 有幾個成員類別(member class)?
  - 有幾個區域類別(local class)?
  - 有幾個匿名類別(anonymous class)?
  - 有幾個靜態巢狀類別(static nested class)?

```java
package com.example;

public class OuterClass { //OuterClass.class

    public int x = 42;

    public void method1() {
        // local classes are inner classes but not members of the outer class
        class LocalClass { //OuterClass$1LocalClass.class

            public void localPrint() {
                System.out.println("In local class");
                System.out.println(x);
                //System.out.println(OuterClass.this.x);
            }
        }
        LocalClass lc = new LocalClass();
        lc.localPrint();
    }

    public void method2() {
        Runnable r = new Runnable() { //OuterClass$1.class

            @Override
            public void run() {
                System.out.println("In an anonymous local class method");
                System.out.println(x);
            }
        };
        r.run();
    }
    public Runnable r = new Runnable() { //OuterClass$2.class

        @Override
        public void run() {
            System.out.println("In an anonymous class method");
            System.out.println(x);
        }
    };
    Object o = new Object() { //OuterClass$3.class

        @Override
        public String toString() {
            return "In an anonymous class method";
        }
    };

    public class InnerClass { //OuterClass$InnerClass.class

        // hides OuterClass x
        public int x = 43;
        //static requires final
        public static final int y = 44;

        public void innerPrint() {
            System.out.println("In a inner class method");
            System.out.println(x);
            System.out.println(OuterClass.this.x);
            System.out.println(y);
        }
    }

    // not an inner class because it is static
    public static class StaticNestedClass { //OuterClass$StaticNestedClass.class

        public void staticNestedPrint() {
            System.out.println("In a static nested class method");
            //compile error
            //System.out.println(x);
        }
    }

    // nesting...
    public class A { //OuterClass$A.class

        public class B { //OuterClass$A$B.class

            public void method() { //OuterClass$A$B$1C.class
                class C {
                }
            }
        }
    }
}
```

```java
package com.example;

public class NestedClassesMain {
    
    public static void main(String[] args) {
        
        OuterClass co = new OuterClass();
        co.method1();
        co.method2();
        
        co.r.run();
        
        OuterClass.InnerClass i = co.new InnerClass();
        i.innerPrint();
        
        OuterClass.StaticNestedClass sn = new OuterClass.StaticNestedClass();
        sn.staticNestedPrint();
        
        OuterClass.A.B nested = co.new A().new B();
    }
}
```

解答:
OuterClass.java檔中有:
- 10 classes
- 1 top-level class
- 9 nested classes
- 8 inner classes
- 3 member classes
- 2 local classes
- 3 anonymous classes
- 1 static nested class


:sweat_smile:

