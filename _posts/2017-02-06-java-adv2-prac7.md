---
layout:	post
title:	Java進階 - Practice 7
date:	2017-02-06
categories: java
---

練習使用Java generics(泛型)及collections.

## 7-1  使用HashMaps

請利用HashMaps來計算每一個part number(零件號碼)出現的次數, 並且根據description(描述文字)排序列印


### 步驟一: 更新ProductCounter類別
1. 新增兩個Map成員變數
  - 第一個Map物件(productCountMap),紀錄part number出現次數(part number為key, 出現次數為value)
  - 第二個Map物件(productNames),紀錄description與part number的對應(description為key, part number為value), 並且根據description來排序(須使用TreeMap)
1. 新增建構子: 接受Map參數, 傳入的map物件指定給成員變數productNames
1. 建立processList(), 將傳入的part number清單, 計算出現次數, 寫入到productCountMap
1. 建立printReport(), 列印每一個part number的description及出現次數的對照清單.
1. 更新main()

```java
package com.example.generics;

import java.util.HashMap;
import java.util.Map;
import java.util.TreeMap;

public class ProductCounter {

    private Map<String, Long> productCountMap = new HashMap<>();
    private Map<String, String> productNames = new TreeMap<>();

    public static void main(String[] args) {

        // List of part data
        String[] parts = new String[]{"1S01", "1S01", "1S01", "1S01", "1S01", 
	"1S02", "1S02", "1S02", "1H01", "1H01", "1S02", "1S01", "1S01", "1H01", 
	"1H01", "1H01", "1S02", "1S02", "1M02", "1M02", "1M02"};

        // Create Product Name Part Number map
        Map<String, String> productNames = new TreeMap<>();
        productNames.put("Blue Polo Shirt", "1S01");
        productNames.put("Black Polo Shirt", "1S02");
        productNames.put("Red Ball Cap", "1H01");
        productNames.put("Duke Mug   ", "1M02");

        // Create Product Counter Object and process
        ProductCounter pc1 = new ProductCounter(productNames);
        pc1.processList(parts);
        pc1.printReport();
    }

    public ProductCounter(Map productNames) {
        this.productNames = productNames;
    }

    public void processList(String[] list) {
        long curVal = 0;
        for (String itemNumber : list) {
            if (productCountMap.containsKey(itemNumber)) {
                curVal = productCountMap.get(itemNumber);
                curVal++;
                productCountMap.put(itemNumber, new Long(curVal));
            } else {
                productCountMap.put(itemNumber, new Long(1));
            }
        }
    }

    public void printReport() {
        System.out.println("=== Product Report ===");
        for (String key : productNames.keySet()) {
            System.out.print("Name: " + key);
            System.out.println("\t\tCount: " + productCountMap.get(productNames.get(key)));
        }
    }
}
```

## 7-2 使用Deque

使用Deque物件(stack)來檢視程式敘述中的小括號是否有配對正確

### 步驟一: 更新processLine()
1. 讀入一行文字, 轉換成char 陣列
1. 利用迴圈, 處理這個char陣列, 
  - 讀到’(‘字元, 則將之放入到stack中, 
  - 讀到’)‘字元, 則自stack中移除一個’(‘ 
1. 當以下情況發生時, 回傳false, 代表小括號沒有配對正確.
  - 當處理完char陣列後, stack中仍有’(‘
  - 當要移除一個’(‘時, 但stack中是空的
1. 否則回傳true, 代表配對正確

```java 
package com.example.collections;

import java.util.ArrayDeque;
import java.util.Deque;

public class ParenMatcher {

    private Deque<Character> stack = new ArrayDeque<>();
    private char[] curLine;

    public boolean processLine(String line) {
        stack.clear();
        curLine = line.toCharArray();

        for (char c : curLine) {
            switch (c) {
                case '(':
                    stack.push(c);
                    break;
                case ')': {
                    if (stack.size() > 0) {
                        stack.pop();
                    } else {
                        return false;
                    }
                    break;
                }
            }
        }
        if (stack.size() > 0) {
            return false; // Missing match invalid expression
        } else {
            return true; //  
        }
    }

    public void processArray(String[] lines) {
        int count = 0;
        for (String line : lines) {
            if (this.processLine(line)) {
                System.out.println("Line " + count + " is valid");
            } else {
                System.out.println("Line " + count + " is invalid");
            }
            count++;
        }
    }

    public static void main(String[] args) {
        ParenMatcher pm = new ParenMatcher();
        String[] expressions = new String[4];

        expressions[0] = "if ((a == b) && (x != y));";
        expressions[1] = "if ((a == b) && (x != y)));";
        expressions[2] = "if ((firstName.equals(newFirstName) && (lastName.equals(newLastName));";
        expressions[3] = "if ((firstName.equals(newFirstName) && (lastName.equals(newLastName))));";

        pm.processArray(expressions);
    }
}
```
 
## 7-3 使用Comparators

計算shirts的inventory(庫存數量), 使用Comparator物件並根據description或inventory來排序

### 步驟一: 更新SortShirtByCount
1. 實作Comparator界面的類別, 根據庫存數來排序

```java
package com.example.generics;

import java.util.Comparator;

public class SortShirtByCount implements Comparator<Shirt> {

    public int compare(Shirt s1, Shirt s2) {
        Long c1 = new Long(s1.getCount());
        Long c2 = new Long(s2.getCount());

        return c1.compareTo(c2);
    }
}
```

### 步驟二: 更新SortShirtByDesc
1. 實作Comparator界面的類別, 根據description來排序

```java
package com.example.generics;

import java.util.Comparator;

public class SortShirtByDesc implements Comparator<Shirt> {

    public int compare(Shirt s1, Shirt s2) {
        return s1.getDescription().compareTo(s2.getDescription());
    }
}
```
### 步驟三: 更新TestItemCounter類別
1. 利用迴圈, 更新polos Map物件中的shirt物件的庫存數
1. 列印shirts, 根據description排序
1. 列印shirts, 根據庫存數排序

```java
package com.example.generics;

import java.util.ArrayList;
import java.util.Collections;
import java.util.Comparator;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class TestItemCounter {

    public static void main(String[] args) {
        // Current Shirt type for a transaction
        Shirt currentShirt = null;

        // Create Items to Count
        Map<String, Shirt> polos = new HashMap<>(10);
        polos.put("P001", new Shirt("P001", "Blue Polo Shirt", "Blue", "L"));
        polos.put("P002", new Shirt("P002", "Black Polo Shirt", "Black", "M"));
        polos.put("P003", new Shirt("P003", "Maroon Polo Shirt", "Maroon", "XL"));
        polos.put("P004", new Shirt("P004", "Tan Polo Shirt", "Tan", "S"));

        // Transactions
        List<DukeTransaction> transactions = new ArrayList<>(20);
        transactions.add(DukeTransaction.createTransaction("P001", "Purchase", 30));
        transactions.add(DukeTransaction.createTransaction("P002", "Purchase", 20));
        transactions.add(DukeTransaction.createTransaction("P003", "Purchase", 20));
        transactions.add(DukeTransaction.createTransaction("P004", "Purchase", 20));
        transactions.add(DukeTransaction.createTransaction("P001", "Sale", 1));
        transactions.add(DukeTransaction.createTransaction("P001", "Sale", 2));
        transactions.add(DukeTransaction.createTransaction("P002", "Sale", 5));
        transactions.add(DukeTransaction.createTransaction("P002", "Sale", 1));
        transactions.add(DukeTransaction.createTransaction("P002", "Sale", 2));
        transactions.add(DukeTransaction.createTransaction("P002", "Sale", 2));
        transactions.add(DukeTransaction.createTransaction("P002", "Purchase", 5));
        transactions.add(DukeTransaction.createTransaction("P001", "Sale", 3));
        transactions.add(DukeTransaction.createTransaction("P003", "Sale", 2));
        transactions.add(DukeTransaction.createTransaction("P003", "Purchase", 5));
        transactions.add(DukeTransaction.createTransaction("P003", "Sale", 3));
        transactions.add(DukeTransaction.createTransaction("P004", "Sale", 2));
        transactions.add(DukeTransaction.createTransaction("P004", "Purchase", 5));
        transactions.add(DukeTransaction.createTransaction("P004", "Sale", 4));

        // Count the shirts
        for (DukeTransaction transaction : transactions) {
            if (polos.containsKey(transaction.getProductID())) {
                currentShirt = polos.get(transaction.getProductID());
            } else {
                System.out.println("Error: Invalid part number");
            }

            switch (transaction.getTransactionType()) {
                case "Purchase":
                    currentShirt.addItems(transaction.getCount());
                    break;

                case "Sale":
                    currentShirt.removeItems(transaction.getCount());
                    break;

                default:
                    System.out.println("Error: Invalid Transaction Type");
                    continue;
            }

        }

        // Convert to List
        List<Shirt> poloList = new ArrayList<>(polos.values());

        // Init Comparators
        Comparator<Shirt> sortDescription = new SortShirtByDesc();
        Comparator<Shirt> sortCount = new SortShirtByCount();

        // Print Results - Sort by Description
        Collections.sort(poloList, sortDescription);
        System.out.println("=== Inventory Report - Description ===");

        for (Shirt shirt : poloList) {
            System.out.println(shirt.toString());
        }

        // Print Results - Sort by Count
        Collections.sort(poloList, sortCount);
        System.out.println("=== Inventory Report - Count ===");

        for (Shirt shirt : poloList) {
            System.out.println(shirt.toString());
        }
    }
} 
```

其他原始檔如下: 

```java
package com.example.generics;

public interface InventoryCount {

    public long getCount();

    public void addItems(long count);

    public void removeItems(long count);
}

```

```java
package com.example.generics;

public class Shirt implements InventoryCount {

    private String id = "";
    private String description = "";
    private String color = "";
    private String size = "";
    private long count = 0;

    private Shirt() {
    }

    ;

    public Shirt(String id, String description, String color, String size) {
        this.id = id;
        this.description = description;
        this.color = color;
        this.size = size;
    }

    public String getId() {
        return this.id;
    }

    public String getDescription() {
        return description;
    }

    public String getColor() {
        return color;
    }

    public String getSize() {
        return size;
    }

    @Override
    public String toString() {
        StringBuilder sb = new StringBuilder(256);
        sb.append("Shirt ID: ").append(this.getId()).append("\n");
        sb.append("Description: ").append(this.getDescription()).append("\n");
        sb.append("Color: ").append(this.getColor()).append("\n");
        sb.append("Size: ").append(this.getSize()).append("\n");
        sb.append("Inventory: ").append(this.getCount()).append("\n");

        return sb.toString();
    }

    @Override
    public long getCount() {
        return count;
    }

    @Override
    public void addItems(long count) {
        this.count = this.count + count;
    }

    @Override
    public void removeItems(long count) {
        if ((this.count - count) > 0) {
            this.count = this.count - count;
        } else {
            System.out.println("Negative inventory number error.");
        }
    }
}
```


```java
package com.example.generics;

public class DukeTransaction {

    private String productID = "";
    private String transactionType = "";
    private long count = 0;

    private DukeTransaction() {
    }

    private DukeTransaction(String productID, String transactionType, long count) {
        this.productID = productID;
        this.transactionType = transactionType;
        this.count = count;
    }

    public static DukeTransaction createTransaction(String productID, String transactionType, long count) {
        DukeTransaction newTransaction = new DukeTransaction(productID, transactionType,
                count);
        return newTransaction;
    }

    public String getProductID() {
        return this.productID;
    }

    public String getTransactionType() {
        return this.transactionType;
    }

    public long getCount() {
        return this.count;
    }
}
```  
         
:sweat_smile:

