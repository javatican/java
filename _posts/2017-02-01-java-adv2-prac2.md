---
layout:	post
title:	Java進階 - Practice 2
date:	2017-02-01
categories: java
---

利用NetBeans IDE建立project, packages及一的Java main class. 練習使用類別及子類別. 
然後在IDE中執行project, 並傳遞命令列參數給main class.

## 2-1 建立類別


### 步驟一: 建立Employee類別

1. 建立com.example.domain套件
1. 建立Employee類別, 包含empId, name, ssn, salary等成員變數
1. 定義預設建構子
1. 增加getter, setter等方法

```java
package com.example.domain;

public class Employee {

    public int empId;
    public String name;
    public String ssn;
    public double salary;

    public Employee() {
    }

    public int getEmpId() {
        return empId;
    }

    public void setEmpId(int empId) {
        this.empId = empId;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public double getSalary() {
        return salary;
    }

    public void setSalary(double salary) {
        this.salary = salary;
    }

    public String getSsn() {
        return ssn;
    }

    public void setSsn(String ssn) {
        this.ssn = ssn;
    }

}
```


### 步驟二: 建立EmployeeTest類別

1. 建立Employee物件, 放在com.example套件
1. 呼叫setter方法設定該物件的成員變數值
1. 列印該物件的成員變數

```java
package com.example;

import com.example.domain.Employee;

public class EmployeeTest {

    public static void main(String[] args) {
        Employee emp = new Employee();
        emp.setEmpId(101);
        emp.setName("Jane Smith");
        emp.setSsn("012-34-5678");
        emp.setSalary(120_345.27);
        System.out.println("Employee id:         " + emp.getEmpId());
        System.out.println("Employee name:       " + emp.getName());
        System.out.println("Employee Soc Sec #:  " + emp.getSsn());
        System.out.println("Employee salary:     " + emp.getSalary());
    }
}

```
:sweat_smile:

