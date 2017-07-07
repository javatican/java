---
layout:	post
title:	Java進階 - Practice 6
date:	2017-02-05
categories: java
---

練習使用Java界面及設計模式(design patterns).

## 6-1  建立界面及實作界面

請參考原廠教材的p67的類別階層圖. 建立相關的界面與類別.

### 步驟一: 檢視Animal抽象類別及Spider實作類別

1. Animal抽象類別包含:
  - int legs(幾隻腳)
  - 建構子 
  - walk()方法
  - 抽象方法 eat()
1. Spider類別繼承Animal類別, 包含:
  - 建構子
  - 覆蓋eat()方法(提供抽象方法的實作內容)

```java
package com.example;

public abstract class Animal {
    
    protected int legs;
    
    public Animal(int legs) {
        this.legs = legs;
    }
    
    public void walk() {
        System.out.println("This animal walks on " + legs + " legs.");
    }
    
    public abstract void eat();
    
}
```

```java
package com.example;

public class Spider extends Animal {

    public Spider() {
        super(8);
    }

    @Override
    public void eat() {
        System.out.println("The spider eats a fly.");
    }

}
```

### 步驟二: 建立Pet interface

1. 包含三個抽象方法:
  - public String getName();
  - public void setName(String name);
  - public void play();

```java
package com.example;

interface Pet {

    public String getName();

    public void setName(String name);

    public void play();
    
}
```

### 步驟三: 建立Fish類別

1. 繼承Animal父類別, 實作Pet界面
1. 定義name屬性
1. 定義預設建構子
1. 覆蓋(實作)Pet界面中的getName(), setName(String), play()抽象方法, 
1. 覆蓋(實作)Animal抽象類別的抽象方法eat(), 並覆蓋walk()方法

```java
package com.example;

public class Fish extends Animal implements Pet {

    private String name;

    public Fish() {
        super(0); //no legs
    }

    @Override
    public void eat() {
        System.out.println("Fish eat pond scum.");
    }

    @Override
    public String getName() {
        return name;
    }

    @Override
    public void setName(String name) {
        this.name = name;
    }

    @Override
    public void play() {
        System.out.println("Just keep swimming.");
    }

    @Override
    public void walk() {
        super.walk();
        System.out.println("Fish, of course, can't walk; they swim.");
    }
}
```

### 步驟四: 建立Cat類別
1. 繼承Animal父類別, 實作Pet界面
1. 定義name屬性
1. 定義預設建構子, 及接受name參數的建構子
1. 覆蓋(實作)Pet界面中的getName(), setName(String), play()抽象方法, 
1. 覆蓋(實作)Animal抽象類別的抽象方法eat() 

```java
package com.example;

public class Cat extends Animal implements Pet {

    private String name;

    public Cat() {
        this("Fluffy"); //call another constructor
    }

    public Cat(String name) {
        super(4); //four legs
        this.name = name;
    }

    @Override
    public void eat() {
        System.out.println("Cats like to eat spiders and fish.");
    }

    @Override
    public String getName() {
        return name;
    }

    @Override
    public void setName(String name) {
        this.name = name;
    }

    @Override
    public void play() {
        System.out.println(name + " likes to play with string.");
    }

}
```

### 步驟五: 更新PetMain類別
1. 測試虛擬方法呼叫
1. 實作playWithAnimal(Animal a)方法
  - 利用instanceof運算子, 若Animal物件實際型態是Pet物件, 則呼叫其play(), 否則顯示”危險”訊息

```java
package com.example;

public class PetMain {

    public static void main(String[] args) {
        Animal a;
        //test a spider with a spider reference
        Spider s = new Spider();
        s.eat();
        s.walk();
        //test a spider with an animal reference
        a = new Spider();
        a.eat();
        a.walk();

        Pet p;

        Cat c = new Cat("Tom");
        c.eat();
        c.walk();
        c.play();
        a = new Cat();
        a.eat();
        a.walk();
        p = new Cat();
        p.setName("Mr. Whiskers");
        p.play();

        Fish f = new Fish();
        f.setName("Guppy");
        f.eat();
        f.walk();
        f.play();
        a = new Fish();
        a.eat();
        a.walk();

        playWithAnimal(s);
        playWithAnimal(c);
        playWithAnimal(f);
    }

    public static void playWithAnimal(Animal a) {
        if (a instanceof Pet) {
            Pet p = (Pet) a;
            p.play();
        } else {
            System.out.println("Danger! Wild Animal");
        }
    }
}
```

## 6-2  使用DAO Pattern

撰寫一個關於人力資源系統中的管理員工的應用. 可以針對員工(Employee)資料進行建立, 讀取, 更新, 刪除, 列表等動作.
Employee物件儲存在一個陣列中(因此是在記憶體中, 沒有做永久儲存). 因此這個練習需要**將儲存物件的邏輯移動到Employee類別之外**. 
在以後的練習中可以改變儲存的方式, 例如寫到資料庫中, 而且**這種改變儲存方式的動作不需要更動到Employee類別本身**. 

### 步驟一: 檢視Employee類別

這是尚未使用DAO Pattern之前的Employee類別, 其中包含:

1. Employee[]陣列employeeArray, **長度10**, 用來儲存Employee物件(因此陣列索引值不得超過9)
1. Employee類別的`商業邏輯`, 包括`getXXX()`方法, `toString()`等. 
1. Employee類別的`儲存邏輯`, 包括`save()`, `delete()`, `findById()`, `getAllEmployees()`等方法
1. 這些儲存邏輯中, 都使用Employee id作為employeeArray的索引值.

```java
package com.example.model;

import java.text.NumberFormat;
import java.text.SimpleDateFormat;
import java.util.ArrayList;
import java.util.Date;
import java.util.List;

public class Employee {

    private int id;
    private String firstName;
    private String lastName;
    private Date birthDate;
    private float salary;
    // not thread-safe
    private static Employee[] employeeArray = new Employee[10];

    public Employee() {
    }

    public Employee(int id, String firstName, String lastName, Date birthDate, float salary) {
        this.id = id;
        this.firstName = firstName;
        this.lastName = lastName;
        this.birthDate = birthDate;
        this.salary = salary;
    }

    public int getId() {
        return id;
    }

    public String getFirstName() {
        return firstName;
    }

    public String getLastName() {
        return lastName;
    }

    public Date getBirthDate() {
        return birthDate;
    }

    public float getSalary() {
        return salary;
    }

    @Override
    public String toString() {
        return "Employee ID:   " + getId() + "\n"
                + "Employee Name: " + getFirstName() + " " + getLastName() + "\n"
                + "Birth Date:    " + new SimpleDateFormat("MMM d, yyyy").format(getBirthDate()) + "\n"
                + "Salary:        " + NumberFormat.getCurrencyInstance().format((double) getSalary());
    }

    // Save our Employee record
    public void save() {
        employeeArray[id] = this;
    }

    // Delete our employee record
    public void delete() {
        employeeArray[id] = null;
    }

    // Find an Employee record using this ID
    public static Employee findById(int id) {
        return employeeArray[id];
    }

    // Return an array of all of the Employee records
    // We are using a collection List object to store the results
    // This makes it easier to just add to the collection
    public static Employee[] getAllEmployees() {
        List<Employee> emps = new ArrayList<>();
        // Iterate through the memory array and find Employee objects
        for (Employee e : employeeArray) {
            if (e != null) {
                emps.add(e);
            }
        }
        return emps.toArray(new Employee[0]);
    }
}
```

### 步驟二: 建立EmployeeDAO界面
1. 建立在com.example.dao套件下
1. 包含以下抽象方法:
  - public void add(Employee e);
  - public void update(Employee e);
  - public void delete(int id);
  - public Employee findById(int id);
  - public Employee[] getAllEmployees();

```java
package com.example.dao;

import com.example.model.Employee;

public interface EmployeeDAO {

    public void add(Employee emp);

    public void update(Employee emp);

    public void delete(int id);

    public Employee findById(int id);

    public Employee[] getAllEmployees();

}
```

### 步驟三: 建立實作EmployeeDAO界面的類別EmployeeDAOMemoryImpl
1. 將原本在Employee類別內的employeeArray陣列及`儲存邏輯`, 移到EmployeeDAOMemoryImpl中
1. 預設等級的建構子
1. 將儲存邏輯方法, 改寫成實作EmployeeDAO中的抽象方法
  - save()改寫成add(Employee)
  - 複製save()並改寫成為update(Employee)
  - delete()改寫成為delete(int id)
  - findById()改成為non-static
  - getAllEmployees()改成為non-static

```java
package com.example.dao;

import com.example.model.Employee;
import java.util.ArrayList;
import java.util.List;

public class EmployeeDAOMemoryImpl implements EmployeeDAO {

    // not thread-safe
    private static Employee[] employeeArray = new Employee[10];

    // package level access
    EmployeeDAOMemoryImpl() {
    }

    // Add an Employee record
    public void add(Employee emp) {
        employeeArray[emp.getId()] = emp;
    }

    // Update an employee record
    public void update(Employee emp) {
        employeeArray[emp.getId()] = emp;
    }

    // Delete an employee record that has this ID
    public void delete(int id) {
        employeeArray[id] = null;
    }

    // Find an Employee record using this ID
    public Employee findById(int id) {
        return employeeArray[id];
    }

    // Return an array of all of the Employee records
    // We are using a collection List object to store the results
    // This makes it easier to just add to the collection
    public Employee[] getAllEmployees() {
        List<Employee> emps = new ArrayList<>();
        // Iterate through the memory array and find Employee objects
        for (Employee e : employeeArray) {
            if (e != null) {
                emps.add(e);
            }
        }
        return emps.toArray(new Employee[0]);
    }
}
```

### 步驟四: 新增EmployeeDAOFactory類別
1. 新增createEmployeeDAO()方法, 回傳一個增EmployeeDAOMemoryImpl物件

```java
package com.example.dao;

public class EmployeeDAOFactory {

    public EmployeeDAO createEmployeeDAO() {
        return new EmployeeDAOMemoryImpl();
    }
}
```

### 步驟五:  更新EmployeeTestInteractive類別
1. 使用EmployeeDAOFactory物件,  取得EmployeeDAO物件
1. 改使用EmployeeDAO物件來進行所有的儲存邏輯.

```java
package com.example.test;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

import com.example.model.Employee;
import com.example.dao.EmployeeDAOFactory;
import com.example.dao.EmployeeDAO;
import java.util.Date;
import java.text.NumberFormat;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Locale;

public class EmployeeTestInteractive {

    public static void main(String[] args) throws Exception {
        EmployeeDAOFactory factory = new EmployeeDAOFactory();

        boolean timeToQuit = false;

        EmployeeDAO dao = factory.createEmployeeDAO();
        BufferedReader in = new BufferedReader(new InputStreamReader(System.in));
        do {
            timeToQuit = executeMenu(in, dao);
        } while (!timeToQuit);
    }

    public static boolean executeMenu(BufferedReader in, EmployeeDAO dao) throws IOException {
        Employee emp;
        String action;
        int id;

        System.out.println("\n\n[C]reate | [R]ead | [U]pdate | [D]elete | [L]ist | [Q]uit: ");
        action = in.readLine();
        if ((action.length() == 0) || action.toUpperCase().charAt(0) == 'Q') {
            return true;
        }

        switch (action.toUpperCase().charAt(0)) {
            // Create a new employee record
            case 'C':
                emp = inputEmployee(in);
                dao.add(emp);
                System.out.println("Successfully added Employee Record: " + emp.getId());
                System.out.println("\n\nCreated " + emp);
                break;

            // Display an employee record
            case 'R':
                System.out.println("Enter int value for employee id: ");
                id = new Integer(in.readLine().trim());

                // Find this Employee record
                emp = dao.findById(id);
                if (emp != null) {
                    System.out.println(emp + "\n");
                } else {
                    System.out.println("\n\nEmployee " + id + " not found");
                    break;
                }

                break;

            // Update an existing employee record    
            case 'U':
                System.out.println("Enter int value for employee id: ");
                id = new Integer(in.readLine().trim());
                // Find this Employee record
                emp = null;
                emp = dao.findById(id);
                if (emp == null) {
                    System.out.println("\n\nEmployee " + id + " not found");
                    break;
                }
                // Go through the record to allow changes

                emp = inputEmployee(in, emp);
                dao.update(emp);
                System.out.println("Successfully updated Employee Record: " + emp.getId());
                break;

            // Delete an employee record
            case 'D':
                System.out.println("Enter int value for employee id: ");
                id = new Integer(in.readLine().trim());

                dao.delete(id);
                System.out.println("Deleted Employee " + id);
                break;

            // Display a list (Read the records) of Employees
            case 'L':
                Employee[] allEmps = dao.getAllEmployees();
                for (Employee employee : allEmps) {
                    System.out.println(employee + "\n");
                }
                break;
        }

        return false;
    }

    public static Employee inputEmployee(BufferedReader in) throws IOException {
        return inputEmployee(in, null, true);
    }

    public static Employee inputEmployee(BufferedReader in, Employee empDefaults) throws IOException {
        return inputEmployee(in, empDefaults, false);
    }

    public static Employee inputEmployee(BufferedReader in, Employee empDefaults, boolean newEmployee) throws IOException {
        SimpleDateFormat df = new SimpleDateFormat("MMM d, yyyy", Locale.US);
        NumberFormat nf = NumberFormat.getCurrencyInstance();
        int id = -1;
        String firstName;
        String lastName;
        Date birthDate = null;
        Employee emp;
        float salary;

        if (newEmployee) {
            do {
                System.out.println("Enter int value for employee id: ");
                try {
                    id = new Integer(in.readLine().trim());
                    if (id < 0) {
                        System.out.println("Please retry with a valid positive integer id");
                    }
                } catch (NumberFormatException e) {
                    System.out.println("Please retry with a valid positive integer id");
                }
            } while (id < 0);
        } else {
            id = empDefaults.getId();
            System.out.println("Modify the fields of Employee record: " + id
                    + ". Press return to accept current value.");
        }

        String prompt = "Enter value for employee first name" + ((empDefaults == null) ? "" : " [" + empDefaults.getFirstName() + "]");

        do {
            System.out.println(prompt + " : ");
            firstName = in.readLine().trim();
            if (firstName.equals("") && empDefaults != null) {
                firstName = empDefaults.getFirstName();
            }
            if (firstName.length() < 1) {
                System.out.println("Please retry with a valid first name");
            }
        } while (firstName.length() < 1);

        prompt = "Enter value for employee last name" + ((empDefaults == null) ? "" : " [" + empDefaults.getLastName() + "]");
        do {
            System.out.println(prompt + " : ");
            lastName = in.readLine().trim();
            if (lastName.equals("") && empDefaults != null) {
                lastName = empDefaults.getLastName();
            }
            if (lastName.length() < 1) {
                System.out.println("Please retry with a valid last name");
            }
        } while (lastName.length() < 1);

        prompt = "Enter value for employee birth date (" + df.toLocalizedPattern() + ")"
                + ((empDefaults == null) ? "" : " [" + df.format(empDefaults.getBirthDate()) + "]");
        do {
            System.out.println(prompt + " : ");
            String dateStr = in.readLine().trim();
            if (dateStr.equals("") && empDefaults != null) {
                birthDate = empDefaults.getBirthDate();
            } else {
                birthDate = null;
                try {
                    birthDate = df.parse(dateStr);
                } catch (ParseException e) {
                    System.out.println("Please retry with a valid birth date: " + e.getMessage());
                }
            }
        } while (birthDate == null);

        prompt = "Enter float value for employee salary"
                + ((empDefaults == null) ? "" : " [" + nf.format((double) empDefaults.getSalary()) + "]");
        do {
            System.out.println(prompt + " : ");
            salary = 0;
            try {
                String amt = in.readLine().trim();
                if (!amt.equals("")) {
                    salary = Float.parseFloat(amt);
                }
                if (salary == 0 && empDefaults != null) {
                    salary = empDefaults.getSalary();
                }
                if (salary < 0) {
                    System.out.println("Please retry with a positive salary");
                    salary = 0;
                }
            } catch (NumberFormatException e) {
                System.out.println("Please retry with a valid float salary: " + e.getMessage());
            }
        } while (salary == 0);

        // Create an Employee object
        emp = new Employee(id, firstName, lastName, birthDate, salary);
        return emp;
    }
}
```

## 6-3  使用組合式物件
 
目的:
1. 延伸自6-1, 原本在Cat類別中必須實作setName()與getName(), 現在將setName(), getName() 移到Nameable界面, 並提供Nameable實作類別NameableImpl. Cat類別改用delegate的模式, 讓NameableImpl物件來進行實際的setName()與getName()的行為. 
1. 同理, 將原本Animal中的walk()方法, 移到Ambulatory界面, 並提供實作的AmbulatoryImpl類別. Cat類別改用delegate的模式, 讓AmbulatoryImpl物件來進行實際的walk()的行為.

### 步驟一: 建立Nameable界面
1. 包含setName(String)及getName()兩個抽象方法

```java
package com.example;

public interface Nameable {
    
    public void setName(String name);
    
    public String getName();
    
}
```

### 步驟二: 建立NameableImpl實作類別
1. 定義name屬性
1. 覆蓋getName()
1. 覆蓋setName(String name), **僅接受小於20字元的字串參數**

```java
package com.example;

public class NameableImpl implements Nameable {

    private String name;

    @Override
    public void setName(String name) {
        if (name.length() < 20) {
            this.name = name;
        } else {
            System.out.println("Name too long");
        }
    }

    @Override
    public String getName() {
        return name;
    }

}
```

### 步驟三: 更新Pet界面
1. 繼承Nameable界面
1. 移除setName(), getName()方法

```java
package com.example;

interface Pet extends Nameable {

    public void play();
    
}
```
### 步驟四: 更新Animal類別
1. 移除legs成員變數
1. 移除建構子
1. 刪除Animal類別中的walk()方法

```java
package com.example;

public abstract class Animal {
       
    public abstract void eat();
    
}
```
### 步驟五: 新增Ambulatory界面, 
1. 包含walk()抽象方法

```java
package com.example;

public interface Ambulatory {
    
    public void walk();
    
}
```

### 步驟六: 新增AmbulatoryImpl實作類別
1. 實作Ambulatory界面
1. 新增legs成員變數
1. 定義AmbulatoryImpl(int legs)建構子
1. 覆蓋(實作)walk()方法

```java
package com.example;

public class AmbulatoryImpl implements Ambulatory {

    private int legs;
    
    public AmbulatoryImpl(int legs) {
        this.legs = legs;
    }

    public void walk() {
        System.out.println("This animal walks on " + legs + " legs.");
    }
}
```

### 步驟七: 更新Fish類別, 使用Composition
1. 刪除name成員變數
1. 新增Nameable型態的成員變數(參考到一個NameableImpl物件)
1. 更新getName(), setName()方法, 改成使用派遣(delegate)方式, 交給Nameable物件處理
1. 移除walk()方法

```java
package com.example;

public class Fish extends Animal implements Pet {

    private Nameable nameable = new NameableImpl();

    @Override
    public void eat() {
        System.out.println("Fish eat pond scum.");
    }

    @Override
    public void setName(String name) {
        nameable.setName(name);
    }

    @Override
    public String getName() {
        return nameable.getName();
    }

    @Override
    public void play() {
        System.out.println("Just keep swimming.");
    }

}
```
### 步驟八: 更新Cat類別, 使用Composition

1. 實作Ambulatory界面
1. 刪除name成員變數
1. 新增Nameable型態的成員變數(參考到一個NameableImpl物件)
1. 新增Ambulatory型態的成員變數(參考到一個AmbulatoryImpl物件)
1. 更新getName(), setName()方法, 改成使用派遣(delegate)方式, 交給Nameable物件處理
1. 更新walk()方法, 改成使用派遣(delegate)方式, 交給Ambulatory物件處理

```java
package com.example;

public class Cat extends Animal implements Pet, Ambulatory {
    
    private Nameable nameable = new NameableImpl();
    private Ambulatory ambulatory;
    
    public Cat() {
        this("Fluffy");
    }
    
    public Cat(String name) {
        ambulatory = new AmbulatoryImpl(4);
        setName(name);
    }

    @Override
    public void eat() {
        System.out.println("Cats like to eat spiders and fish.");
    }

    @Override
    public String getName() {
        return nameable.getName();
    }

    @Override
    public void setName(String name) {
        nameable.setName(name);
    }

    @Override
    public void play() {
        System.out.println(getName() + " likes to play with string.");
    }

    @Override
    public void walk() {
        ambulatory.walk();
    }
    
}
```

### 步驟九: 更新Spider類別, 使用Composition

1. 實作Ambulatory界面 
1. 新增Ambulatory型態的成員變數(參考到一個AmbulatoryImpl物件)
1. 更新walk()方法, 改成使用派遣(delegate)方式, 交給Ambulatory物件處理

```java
package com.example;

public class Spider extends Animal implements Ambulatory {
    
    private Ambulatory ambulatory;
    
    public Spider() {
        ambulatory = new AmbulatoryImpl(8);
    }

    @Override
    public void eat() {
        System.out.println("The spider eats a fly.");
    }

    @Override
    public void walk() {
        ambulatory.walk();
    }
    
}
```

:sweat_smile:

