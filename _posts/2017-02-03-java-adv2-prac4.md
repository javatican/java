---
layout:	post
title:	Java進階 - Practice 4
date:	2017-02-03
categories: java
---

練習方法覆蓋(Override), 包含覆蓋`Object.toString()`方法. 也練習使用`instanceof`運算子來決定物件的型態. 

## 4-1 方法覆蓋與多型

在`Employee`類別與`Manager`類別, 覆蓋`Object.toString()`方法. 建立`EmployeeStockPlan`類別, 
包含一個`grantStock()`方法, 用來計算員工的股票選擇權的股數. 使用`instanceof`運算子來決定不同employee型態應該有的股數.

### 步驟一: Employee類別
1. 覆蓋`Object.toString()`方法
  - 回傳一個String, 內容包括`empId, name, ssn, salary`等成員變數的值
  - 選擇性的加上`@Override`的註解

```java
package com.example.domain;

import java.text.NumberFormat;

public class Employee {

    private int empId;
    private String name;
    private String ssn;
    private double salary;

    public Employee(int empId, String name, String ssn, double salary) {
        this.empId = empId;
        this.name = name;
        this.ssn = ssn;
        this.salary = salary;
    }

    public int getEmpId() {
        return empId;
    }

    public String getName() {
        return name;
    }

    public String getSsn() {
        return ssn;
    }

    public double getSalary() {
        return salary;
    }

    public void setName(String name) {
        // Make sure the name value is not null or empty
        if (name != null && !name.equals("")) {
            this.name = name;
        }
    }

    public void raiseSalary(double increase) {
        // Make sure the increase is not less than 0
        if (increase > 0) {
            salary += increase;

        }
    }

    public String toString() {
        return "Employee ID:     " + getEmpId() + "\n"
                + "Employee Name:   " + getName() + "\n"
                + "Employee SSN:    " + getSsn() + "\n"
                + "Employee Salary: " + NumberFormat.getCurrencyInstance().format(getSalary());
    }
}
```

### 步驟二: Manager類別
1. 覆蓋`Object.toString()`方法
  - 回傳一個String, 內容包括`empId, name, ssn, salary, deptName`等成員變數的值
  - 可以利用`super.toString()`呼叫`Employee`類別的`toString()`, 來產生部份的字串內容, 然後串接額外的`deptName`變數值.
  - 選擇性的加上`@Override`的註解

```java
package com.example.domain;

public class Manager extends Employee {

    private String deptName;

    public Manager(int empId, String name, String ssn, double salary, String deptName) {
        super(empId, name, ssn, salary);
        this.deptName = deptName;
    }

    public String getDeptName() {
        return deptName;
    }

    @Override
    public String toString() {
        return super.toString() + "\nDepartment:      " + getDeptName();
    }
}
```

### 步驟三: Director類別
1. 覆蓋`Object.toString()`方法
  - 回傳一個String, 內容包括`empId, name, ssn, salary, deptName, budget`等成員變數的值
  - 可以利用`super.toString()`呼叫`Manager`類別的`toString()`, 來產生部份的字串內容, 然後串接額外的`budget`變數值.
  - 選擇性的加上`@Override`的註解

```java
package com.example.domain;

public class Director extends Manager {

    private double budget;

    public Director(int empId, String name, String ssn, double salary, String department, double budget) {
        super(empId, name, ssn, salary, department);
        this.budget = budget;
    }

    public double getBudget() {
        return budget;
    }

    @Override
    public String toString() {
        return super.toString() + "\nBudget:          " + getBudget();
    }
}
```


### 步驟四: EmployeeStockPlan類別
1. 建立在`com.example.business`套件中
1. 定義單一個方法`int grantStock(Employee e)`, 內容即根據Employee物件的型態(使用instanceof運算子), 回傳股票選擇權的數目.

```java
package com.example.business;

import com.example.domain.Director;
import com.example.domain.Employee;
import com.example.domain.Manager;

public class EmployeeStockPlan {

    private final int employeeShares = 10;
    private final int managerShares = 100;
    private final int directorShares = 1000;

    public EmployeeStockPlan() {
    }

    public int grantStock(Employee emp) {
        // Stock is granted based on the employee type
        if (emp instanceof Director) {
            return directorShares;
        } else if (emp instanceof Manager) {
            return managerShares;
        } else {
            return employeeShares;
        }
    }
}
```

### 步驟五: EmployeeTest類別 
1. 更新`printEmployee(Employee emp)`方法
  - 呼叫`emp.getClass().getSimpleName()`列印出emp物件的類別名稱(也就是印出傳入emp物件的實際類別型態) 
  - 改變為呼叫`System.out.println(emp)`. 這會利用所覆蓋的`Object.toString()`方法來產生String資訊.
1. 定義**過載**的`printEmployee(Employee emp, EmployeeStockPlan esp)`方法
  - 呼叫`printEmployee(Employee emp)`來列印相關的Employee資訊之外, 
  - 利用`esp.grantStock(emp)`列印出股票選擇權的數目.

```java
package com.example;

import com.example.business.EmployeeStockPlan;

import com.example.domain.Admin;
import com.example.domain.Director;
import com.example.domain.Employee;
import com.example.domain.Engineer;
import com.example.domain.Manager;

public class EmployeeTest {

    public static void main(String[] args) {

        // Create the classes as per the practice
        Engineer eng = new Engineer(101, "Jane Smith", "012-34-5678", 120_345.27);

        Manager mgr = new Manager(207, "Barbara Johnson", "054-12-2367", 109_501.36, "Marketing");

        Admin adm = new Admin(304, "Bill Munroe", "108-23-6509", 75_002.34);

        Director dir = new Director(12, "Susan Wheeler", "099-45-2340", 120_567.36, "Global Marketing", 100_000.00);

        // Test the EmployeeStockPlan class
        EmployeeStockPlan esp = new EmployeeStockPlan();
        printEmployee(eng, esp);
        printEmployee(adm, esp);
        printEmployee(mgr, esp);
        printEmployee(dir, esp);

        System.out.println("\nTesting raiseSalary and setName on Manager:");
        mgr.setName("Barbara Johnson-Smythe");
        mgr.raiseSalary(10_000.00);
        printEmployee(mgr, esp);
    }

    public static void printEmployee(Employee emp) {
        System.out.println(); // Print a blank line as a separator
        // Print out the data in this Employee object
        System.out.println("Employee type:   " + emp.getClass().getSimpleName());
        System.out.println(emp);
    }

    public static void printEmployee(Employee emp, EmployeeStockPlan esp) {
        printEmployee(emp);
        System.out.println("Stock Options:   " + esp.grantStock(emp));
    }
}
```

:sweat_smile:

