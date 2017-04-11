---
layout:	post
title:	Java基礎 - Practice 2
date:	2017-01-01
categories: java
---
## 2-1 使用命令列編譯並執行Java程式

1. 準備一個java原始檔, 例如: `CalcAverage.java`, 內容如下:

```java
package myjava;

public class CalcAverage {

    public static void main(String[] args) {
        int[] x = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
        int sum = 0;
        for (int i = 0; i < x.length; i++) {
            sum += x[i];
        }
        System.out.println("平均為:" + (float) sum / x.length);
    }
}
```

2. 將`CalcAverage.java`儲存到`c:\java_source\myjava`目錄下

3. 將`工作目錄`轉移到`c:\java_source`目錄下

```
cd c:\java_source 
```

4. 編譯(`javac`)原始檔, 產生`.class`檔(也就是`bytecode`檔)

```
javac myjava\CalcAverage.java

```

5. 檢視一下`c:\java_source\myjava`目錄下是否有產生`CalcAverage.class`

6. 執行(`java`)程式

```
java myjava.CalcAverage
```

備註: 
- 編譯時, 也可以將產生的.class檔放到不同的目錄, 例如`c:\java_class`目錄下, 可以使用`-d`的選項

```
javac -d c:\java_class myjava\CalcAverage.java

```

- 檢視一下`c:\java_class\myjava`目錄下是否有產生`CalcAverage.class`

- 執行程式, 必須使用`-cp`的選項, 告訴解譯器.class所在的路徑

```
java -cp c:\java_class myjava.CalcAverage
```

## 2.2 使用NetBeans IDE開發工具

1. 打開NetBeans IDE
1. 建立新專案(`New Project`)
1. 選擇`Java project with existing source`
1. 指定一個專案名稱, 例如`Practice2_2`
1. `3. Existing Sources`這一步驟, Source Packages Folder輸入選單中, 點擊`Add Folder`按鈕, 指定為`c:\java_source`
1. 如果有詢問是否要刪除package folder下的.class檔, 點擊`Delete`按鈕
1. 更新專案的屬性: 
  - 在專案列表中, 滑鼠點您的專案, 按下滑鼠右鍵, 彈出的選單視窗, 最下方的Properties選項
  - 將Source/Binary Format改選成JDK 7, 按下方的OK按鈕, 儲存設定.
1. 編譯: 滑鼠點`CalcAverage.java`, 按下滑鼠右鍵, 彈出的選單視窗, 選擇`Compile File`
1. 檢視`左側欄的Files項目`內的`Practice2_2>build>classes>myjava`目錄是否有產生的`CalcAverage.class`
1. 執行: 返回到`左側欄的Projects項目`, 滑鼠點`CalcAverage.java`, 按下滑鼠右鍵, 彈出的選單視窗, 選擇`Run File`

:sweat_smile:

