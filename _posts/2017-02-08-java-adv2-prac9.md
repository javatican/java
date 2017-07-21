---
layout:	post
title:	Java進階 - Practice 9
date:	2017-02-08
categories: java
---
 
練習使用try-catch 敘述, 繼承Exception類別, 使用throw及throws.

## 9-1 捕捉checked及unchecked例外事件
 
在讀取檔案內容的程式中, 加上適當的例外事件處理的程式碼

### 步驟一: 更新ExceptionMain類別的main()方法
1. 從命令列參數args[0] 讀入檔案名稱. 如果程式在執行時沒有提供該參數, 則程式會丟出
`java.lang.ArrayIndexOutOfBoundsException`例外事件物件.
1. 將println()的呼叫包在try-catch裡面, 所捕捉的例外事件型態即是`java.lang.ArrayIndexOutOfBoundsException`,
並且在catch區塊中印出`"No file specified, quitting!"`訊息, 然後讓程式結束執行(呼叫`System.exit(1)`).
1. 執行程式, 測試沒有傳入命令列參數時的情況. 
1. 測試程式, 提供一個檔名(DeclarationOfIndependence.txt)當作參數. 
1. 利用BufferedReader物件串接FileReader物件, 讀取上述的檔案. 
  - 利用try-with-resources敘述, 將上述BufferedReader物件作為自動關閉的資源 
  - 在一個迴圈中, 每次循環讀取一行文字, 然後列印出文字.
  - 增加兩個catch區塊, 一個捕捉FileNotFoundException, 一個捕捉IOException

```java
package com.example;

import java.io.BufferedReader;
import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.IOException;

public class ExceptionMain {

    public static void main(String[] args) {
        try {
            System.out.println("Reading from file:" + args[0]);
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println("No file specified, quitting!");
            System.exit(1);
        }

        try (BufferedReader b = new BufferedReader(new FileReader(args[0]));) {
            String s = null;
            while ((s = b.readLine()) != null) {
                System.out.println(s);
            }
        } catch (FileNotFoundException e) {
            System.out.println("File not found:" + args[0]);
            System.exit(1);
        } catch (IOException e) {
            System.out.println("Error reading file:" + e.getMessage());
            System.exit(1);
        }
    }
}
```


## 9-2 繼承Exception
 
練習使用客製化Exception類別與自動關閉資源. 
之前練習的DAO Pattern, 其中DAO實作類別EmployeeDAOMemoryImpl, 並沒有針對可能丟出的ArrayIndexOutOfBoundsException去做處理.
而DAO實作類別在未來可能會替換使用其他的類別, 例如使用到資料庫做儲存方式. 
為了方便實作類別的替換, EmployeeDAO界面必須使用統一的例外事件型態, 因此這裡採用自訂的例外事件型別DAOException. 
所有實作類別中可能丟出的例外事件, 都包裹成DAOException
 

### 步驟一: 建立DAOException
1. 繼承Exception類別
1. 定義四個建構子. 參數特徵須與Exception類別的四個public建構子相同. 
  - 在每一個建構子中, 利用super()呼叫父類別中相同參數的建構子

```java
package com.example.dao;

public class DAOException extends Exception {

    public DAOException() {
        super();
    }

    public DAOException(String message) {
        super(message);
    }

    public DAOException(Throwable cause) {
        super(cause);
    }

    public DAOException(String message, Throwable cause) {
        super(message, cause);
    }
}
```

### 步驟二: 更新EmployeeDAO
1. 所有界面中的抽象方法都宣告throws DAOException 
1. 繼承AutoCloseable界面, 作為一種可以自動關閉的資源. 

```java
package com.example.dao;

import com.example.model.Employee;

public interface EmployeeDAO extends AutoCloseable {

    public void add(Employee emp) throws DAOException;

    public void update(Employee emp) throws DAOException;

    public void delete(int id) throws DAOException;

    public Employee findById(int id) throws DAOException;

    public Employee[] getAllEmployees() throws DAOException;
    
}
```

### 步驟三: 更新EmployeeDAOMemoryImpl實作類別
1. add()方法: 
  - 宣告throws DAOException
  - 新增if判斷式, 確認新增動作不會覆蓋了已經存在的Employee物件. 若會覆蓋, 則丟出DAOException, 並且包含恰當的錯誤訊息.
  - 使用try-catch區塊, 捕捉ArrayIndexOutOfBoundsException. 在catch區塊中丟出DAOException, 並且包含恰當的錯誤訊息.
2. update()方法: 
  - 宣告throws DAOException
  - 新增if判斷式, 確認所要update的Employee物件已經存在. 若不存在, 則丟出DAOException, 並且包含恰當的錯誤訊息.
  - 使用try-catch區塊, 捕捉ArrayIndexOutOfBoundsException. 在catch區塊中丟出DAOException, 並且包含恰當的錯誤訊息.
3. delete()方法: 
  - 宣告throws DAOException
  - 新增if判斷式, 確認所要delete的Employee物件已經存在. 若不存在, 則丟出DAOException, 並且包含恰當的錯誤訊息.
  - 使用try-catch區塊, 捕捉ArrayIndexOutOfBoundsException. 在catch區塊中丟出DAOException, 並且包含恰當的錯誤訊息.
4. findById()方法: 
  - 宣告throws DAOException
  - 使用try-catch區塊, 捕捉ArrayIndexOutOfBoundsException. 在catch區塊中丟出DAOException, 並且包含恰當的錯誤訊息.
5. 新增close(), 覆蓋AutoCloseable界面的抽象方法. 
  - 目前的實作類別`EmployeeDAOMemoryImpl`並未包含任何必須做關閉的資源, 因此僅輸出一些訊息即可, 
  - 這個方法主要會用在其他的實作類別. 例如使用資料庫的實作類別, 可以自動關閉資料庫的連結物件.

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
    public void add(Employee emp) throws DAOException {
        if(employeeArray[emp.getId()] != null) {
            throw new DAOException("Error adding employee in DAO, employee id already exists " + emp.getId());
        }
        try {
            employeeArray[emp.getId()] = emp;
        } catch (ArrayIndexOutOfBoundsException e) {
            throw new DAOException("Error adding employee in DAO, id must be less than " + employeeArray.length);
        }
    }

    // Update an employee record
    public void update(Employee emp) throws DAOException {
        if(employeeArray[emp.getId()] == null) {
            throw new DAOException("Error updating employee in DAO, no such employee " + emp.getId());
        }
        try {
            employeeArray[emp.getId()] = emp;
        } catch (ArrayIndexOutOfBoundsException e) {
            throw new DAOException("Error updating employee in DAO, id must be less than " + employeeArray.length);
        }
    }

    // Delete an employee record that has this ID
    public void delete(int id) throws DAOException {
        if(employeeArray[id] == null) {
            throw new DAOException("Error deleting employee in DAO, no such employee " + id);
        }
        try {
            employeeArray[id] = null;
        } catch (ArrayIndexOutOfBoundsException e) {
            throw new DAOException("Error deleting employee in DAO, id must be less than " + employeeArray.length);
        }
    }

    // Find an Employee record using this ID
    public Employee findById(int id) throws DAOException {
        try {
            return employeeArray[id];
        } catch (ArrayIndexOutOfBoundsException e) {
            throw new DAOException("Error finding employee in DAO", e);
        }
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

    @Override
    public void close() {
        System.out.println("No database connection to close just yet");
    }
}
```

### 步驟四: 更新EmployeeTestInteractive類別
1. 更新executeMenu()方法, 宣告除了IOException, 還會丟出DAOException 
1. 移除main()方法 throws Exception的宣告
1. main()方法中使用try-with-resources敘述
  - 將do-while迴圈放入try區塊
  - 宣告EmployeeDAO物件與BufferedReader物件成為自動關閉資源
  - 加上catch 區塊, 捕捉IOException
  - 加上第二個catch區塊, 捕捉Exception
  - 在呼叫executeMenu()的地方加上一個巢狀的try-catch區塊, 捕捉DAOException. 可以避免萬一丟出DAOException, 會造成中斷do-while迴圈進入下面的catch區塊

```java
package com.example.test;

import com.example.dao.DAOException;
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

public class EmployeeTestInteractive {

    public static void main(String[] args) {
        EmployeeDAOFactory factory = new EmployeeDAOFactory();

        boolean timeToQuit = false;
        try (EmployeeDAO dao = factory.createEmployeeDAO();
                BufferedReader in = new BufferedReader(new InputStreamReader(System.in))) {
            do {
                try {
                    timeToQuit = executeMenu(in, dao);
                } catch (DAOException e) {
                    System.out.println("Error " + e.getClass().getName());
                    System.out.println("Message: " + e.getMessage());
                }
            } while (!timeToQuit);
        } catch (IOException e) {
            System.out.println("Error " + e.getClass().getName() + " , quiting.");
            System.out.println("Message: " + e.getMessage());
        } catch (Exception e) {
            System.out.println("Error closing resource " + e.getClass().getName());
            System.out.println("Message: " + e.getMessage());
        }
    }

    public static boolean executeMenu(BufferedReader in, EmployeeDAO dao) throws IOException, DAOException {
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

                // Find this Employee record                 
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
        SimpleDateFormat df = new SimpleDateFormat("MMM d, yyyy");
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
                    birthDate = new Date(df.parse(dateStr).getTime());
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
其他類別
```java
package com.example.model;

import java.text.NumberFormat;
import java.text.SimpleDateFormat;
import java.util.Date;

public class Employee {

    private int id;
    private String firstName;
    private String lastName;
    private Date birthDate;
    private float salary;

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
}
```

```java
package com.example.dao;

public class EmployeeDAOFactory {

    public EmployeeDAO createEmployeeDAO() {
        return new EmployeeDAOMemoryImpl();
    }
}
```

:sweat_smile:

