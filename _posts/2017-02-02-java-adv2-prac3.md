---
layout:	post
title:	Java進階 - Practice 3
date:	2017-02-02
categories: java
---

延伸Employee類別, 建立新的子類別包括`Engineer`, `Admin`, `Manager`以及`Manager`的子類別`Director`. 然後定義測試的類別. 


## 3-1 建立子類別

參考p25的類別階層圖.

### 步驟一:  改寫Employee類別

1. 將`Employee`類別的成員變數做封裝(宣告為private)
1. 將預設建構子替代成接受四個參數的建構子, 分別接受`empId, name, ssn, salary`等參數.
1. 除了`setName()`之外, 移除其他的setter方法
1. 稱加`raiseSalary(double increase)`方法, 用來實作加薪的行為.
 
```java 
package com.example.domain;

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
}
```

### 步驟二:  Manager類別

在同樣的套件下建立`Employee`的子類別`Manager`
1. 新增private成員變數`deptName`
1. 建立建構子, 接受`empId, name, ssn, salary, deptName`等參數.
1. 新增`deptName`的getter方法
 
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
}
```
### 步驟三:  Engineer類別及Admin類別

建立`Employee`子類別`Engineer`及`Admin`.
1. 建立在`com.example.domain`套件
1. 不需要新增任何的成員變數或方法

```java 
package com.example.domain;

public class Engineer extends Employee {

    public Engineer(int empId, String name, String ssn, double salary) {
        super(empId, name, ssn, salary);
    }
}
```

```java
package com.example.domain;

public class Admin extends Employee {

    public Admin(int empId, String name, String ssn, double salary) {
        super(empId, name, ssn, salary);
    }
}
```

### 步驟四: Director類別
建立`Manager`的子類別`Director`

1. 在`com.example.domain`套件
1. 新增private成員變數`budget`
1. 建立建構子, 接受`empId, name, ssn, salary, deptName, budget`等參數.
1. 新增`budget`的getter方法


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
}
```


### 步驟五: EmployeeTest類別

更新`EmployeeTest`測試類別
1. 新增`printEmployee()`方法, 接受`Employee`型態的參數, 內容為列印出`Employee`物件相關的成員變數值.
1. main()中建立`Engineer, Manager, Admin, Director`等物件
1. 呼叫`printEmployee()`方法, 列印出每一個Employee物件的內容資訊
1. 呼叫`raiseSalary()`, `setName()`等方法
1. `printEmployee()`方法中列印`salary`變數時使用`NumberFormat類別`來作美化. 
例如: `NumberFormat.getCurrencyInstance().format(emp.getSalary())`

```java
package com.example;

import com.example.domain.Employee;
import com.example.domain.Engineer;
import com.example.domain.Manager;
import com.example.domain.Admin;
import com.example.domain.Director;
import java.text.NumberFormat;

public class EmployeeTest {

    public static void main(String[] args) {

        // Create the classes as per the practice
        Engineer eng = new Engineer(101, "Jane Smith", "012-34-5678", 120_345.27);

        Manager mgr = new Manager(207, "Barbara Johnson", "054-12-2367", 109_501.36, "US Marketing");

        Admin adm = new Admin(304, "Bill Munroe", "108-23-6509", 75_002.34);

        Director dir = new Director(12, "Susan Wheeler", "099-45-2340", 120_567.36, "Global Marketing", 1_000_000.00);

        // Print information about the objects you created
        printEmployee(eng);
        printEmployee(adm);
        printEmployee(mgr);
        printEmployee(dir);

        System.out.println("\nTesting raiseSalary and setName on Manager:");
        mgr.setName("Barbara Johnson-Smythe");
        mgr.raiseSalary(10_000.00);
        printEmployee(mgr);

    }

    public static void printEmployee(Employee emp) {
        System.out.println(); // Print a blank line as a separator
        // Print out the data in this Employee object
        System.out.println("Employee id:         " + emp.getEmpId());
        System.out.println("Employee name:       " + emp.getName());
        System.out.println("Employee Soc Sec #:  " + emp.getSsn());
        System.out.println("Employee salary:     " + NumberFormat.getCurrencyInstance().format((double) emp.getSalary()));
    }
}
```


## 3-2 更新Manager類別

更新`Manager`類別, 增加`staff`成員變數, 代表一個Manager所管理的旗下員工. 新增方法來加入Employee及移除Employee, 以及一個方法來列印staff資訊.
1. 新增private成員變數`staff`, 型態為Employee的陣列.
1. 新增private `employeeCount`變數用來紀錄旗下員工的數目.
1. 建構子中初始化`staff`成員變數, 內容為包含**20個元素的Employee陣列物件**
1. 新增`int findEmployee(Employee e)`方法, 在`staff`成員變數中尋找該`Employee`物件是否存在. 若存在回傳所在的索引值, 否則回傳-1. 
1. 新增`boolean addEmployee(Employee e)`方法, 將傳入的`Employee`物件, append到`staff`這個陣列後面. 
必須先確認`Employee`物件是否已經存在(可以利用`findEmployee()`方法)再做新增, 新增成功回傳true, 否則回傳false.
1. 新增`boolean removeEmployee(Employee e)`方法, 自`staff`陣列中移除符合的`Employee`物件. 
要注意的是當移除元素時, 必須將這個移除元素後面的所有元素向前挪移一位. 這有多種寫法. 
這裡採用的作法是**建立一個新的陣列, 然後將移除之後剩下的元素複製到這個新的陣列中.**
1. 新增`printStaffDetails()`方法, 列印出`staff`內容.

```java
package com.example.domain;

public class Manager extends Employee {

    private String deptName;
    private Employee[] staff;
    private int employeeCount = 0;

    public Manager(int empId, String name, String ssn, double salary, String deptName) {
        super(empId, name, ssn, salary);
        this.deptName = deptName;
        // Construct an array of up to 20 employees
        this.staff = new Employee[20];
    }

    public String getDeptName() {
        return deptName;
    }

    // Add an employee to the list of employees this manager manages
    // Return true if the add was successful, return false if the employee exists already
    // Later you can use Exceptions instead
    public boolean addEmployee(Employee e) {
        if (findEmployee(e) != -1) {
            return false;
        }
        staff[employeeCount] = e;
        employeeCount++;
        return true;
    }

    // Find an employee in the array - return the index of the employee
    // in the array if they exist or -1 if they don't.
    public int findEmployee(Employee e) {
        int result = -1;
        for (int i = 0; i < employeeCount; i++) {
            if (e.equals(staff[i])) {
                result = i;
            }
        }
        return result;
    }

    // Remove an employee - this requires going through the array and removing
    // the one element that is this employee and at the same time resorting the array.
    // Return true if successful and false if Employee is not in the array.
    public boolean removeEmployee(Employee e) {
        boolean empPartOfStaff = false;
        Employee[] newStaff = new Employee[20];
        int newEmpCount = 0;
        for (int i = 0; i < employeeCount; i++) {
            if (staff[i].getEmpId() == e.getEmpId()) {
                empPartOfStaff = true;
            } else {
                newStaff[newEmpCount] = staff[i];
                newEmpCount++;
            }
        }
        // Was the employee found in the staff array?
        // If yes, use the updated staff array
        if (empPartOfStaff) {
            staff = newStaff;
            employeeCount = newEmpCount;
        }
        return empPartOfStaff;
    }

    // Print out the Staff
    public void printStaffDetails() {
        System.out.println("Staff of " + this.getName() + ":");
        for (int i = 0; i < employeeCount; i++) {
            System.out.println("Name: " + staff[i].getName() + " Employee id: " + staff[i].getEmpId());
        }
    }
}
```

:sweat_smile:

