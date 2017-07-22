---
layout:	post
title:	Java進階 - Practice 8
date:	2017-02-07
categories: java
---

練習使用Regular expressions及String.split()方法來改變字串物件.

## 8-1 解析逗號分隔的文字, 轉換資料產生Shirt物件   
 
### 步驟一: 更新StringSplitTest類別的main()方法

1. 解析shirts 字串陣列中的每一個元素, 代表一筆shirt資料
  - 利用String類別的split()方法, 以逗號(,)作為分隔符號, 分解產生id, description, color, size等資訊.
  - 呼叫Shirt建構子, 產生Shirt物件. 
1. 儲存在shirtList 物件中, 然後列印出來

```java
package com.example.strings;

import java.util.ArrayList;
import java.util.List;

public class StringSplitTest {

    public static void main(String[] args) {
        String[] shirts = new String[11];
        List<Shirt> shirtList = new ArrayList<Shirt>(11);

        shirts[0] = "S001,Black Polo Shirt,Black,XL";
        shirts[1] = "S002,Black Polo Shirt,Black,L";
        shirts[2] = "S003,Blue Polo Shirt,Blue,XL";
        shirts[3] = "S004,Blue Polo Shirt,Blue,M";
        shirts[4] = "S005,Tan Polo Shirt,Tan,XL";
        shirts[5] = "S006,Black T-Shirt,Black,XL";
        shirts[6] = "S007,White T-Shirt,White,XL";
        shirts[7] = "S008,White T-Shirt,White,L";
        shirts[8] = "S009,Green T-Shirt,Green,S";
        shirts[9] = "S010,Orange T-Shirt,Orange,S";
        shirts[10] = "S011,Maroon Polo Shirt,Maroon,S";

        // Parse text
        for (String curLine : shirts) {
            String[] e = curLine.split(",");
            shirtList.add(new Shirt(e[0], e[1], e[2], e[3]));
        }

        // Print out the shirts
        System.out.println("=== Shirt List ===");
        for (Shirt shirt : shirtList) {
            System.out.println(shirt.toString());
        }
    }
}
```

Shirt類別如下:

```java
package com.example.strings;

public class Shirt {

    private String id = "";
    private String description = "";
    private String color = "";
    private String size = "";

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

        return sb.toString();
    }
}
```

## 8-2 使用regular expression來搜尋一個文字檔的內容

讀取一個文字檔(gettys.html), 利用regular expressions搜尋內容(例如`<h4>`), 然後列印出行號及文字內容. 

### 步驟一: 檢視文字檔案內容及所在的目錄
1. 文字檔名為`gettys.html`
1. 儲存在project的`根目錄`裡(包含`src目錄`的目錄即為`根目錄`)
1. 在Project的Files view裡面可以看到這個檔案. 

```html
<html>
    <title>Gettysburg Address</title>
    <style type="text/css">
        body{font-family:sans-serif;}
        h2,h4{text-align:center;}
    </style>
<body>
<h2>Gettysburg Address</h2>
<h4>Abraham Lincoln</h4>
<h4>Thursday, November 19, 1863</h4>

<div class="paragraph">
<p class="line">Four score and seven years ago our fathers brought forth on this continent a new nation, conceived in liberty, and dedicated to the proposition that all men are created equal.</p>
<p class="line">Now we are engaged in a great civil war, testing whether that nation, or any nation, so conceived and so dedicated, can long endure.</p>
<p class="line">We are met on a great battle-field of that war.</p>
<p class="line">We have come to dedicate a portion of that field, as a final resting place for those who here gave their lives that that nation might live.</p> 
<p class="line">It is altogether fitting and proper that we should do this.</p>
</div>

<div class="paragraph">
<p class="line">But, in a larger sense, we can not dedicate, we can not consecrate, we can not hallow this ground.</p> 
<p class="line">The brave men, living and dead, who struggled here, have consecrated it, far above our poor power to add or detract.</p>
<p class="line">The world will little note, nor long remember what we say here, but it can never forget what they did here.</p> 
<p class="line">It is for us the living, rather, to be dedicated here to the unfinished work which they who fought here have thus far so nobly advanced.</p>
<p class="line">It is rather for us to be here dedicated to the great task remaining before us‚ that from these honored dead we take increased devotion to that cause for which they gave the last full measure of devotion‚ that we here highly resolve that these dead shall not have died in vain‚ that this nation, under God, shall have a new birth of freedom‚ and that government of the people, by the people, for the people, shall not perish from the earth.</p>
</div>
</body>
</html>
```

### 步驟二: 更新FindText類別
1. 建立型態為`Pattern`的成員變數`pattern`及`Matcher`型態的成員變數`m`
1. 在`execute()`方法內, 利用`Pattern.compile()`來產生`Pattern`物件, 指定給成員變數`pattern`, 使用以下四種pattern:
  - `<h4>`標籤
  - to 英文字
  - 4個空白字元開頭
  - `<p`或`<d`開頭
  - html結束的標籤(`</XXX>`), 例如`</div>`
1. 在讀取文字每一行文字的迴圈中, 使用pattern.matcher()方法, 產生Matcher物件
  - 如果有找到符合的內容字串, 列印出行號及內容文字.

```java
package com.example.strings;

import java.io.BufferedReader;
import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.IOException;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class FindText {

    private String fileName = "gettys.html";
    private Pattern pattern;
    private Matcher m;

    public static void main(String[] args) {
        FindText find = new FindText();
        find.execute();
    }

    public void execute() {
        // pattern = Pattern.compile("<h4>");        
        // pattern = Pattern.compile("\\bto\\b");
        // pattern = Pattern.compile("class=\"line\"");
        // pattern = Pattern.compile("\\{.*?\\}");
        // pattern = Pattern.compile("^<[p|d]");
        pattern = Pattern.compile("^</.*?>$");

        try (BufferedReader reader = new BufferedReader(new FileReader(fileName))) {
            String line = "";
            int c = 1;
            while ((line = reader.readLine()) != null) {
                m = pattern.matcher(line);
                if (m.find()) {
                    System.out.println(" " + c + "  " + line);
                }
                c++;
            }

        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```
### 步驟三: 更改程式, 可以從命令列讀取兩個參數, 第一個是文字檔名, 第二個是代表regular expression的字串  

```java
package com.example.strings;

import java.io.BufferedReader;
import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.IOException;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class FindTextCmdLine {

    private String fileName = "gettys.html";
    private Pattern pattern;
    private Matcher m;

    public static void main(String[] args) {
        FindTextCmdLine find = new FindTextCmdLine();
        find.execute(args[0], args[1]);
    }

    public void execute(String fileName, String regex) {
        this.fileName = fileName;

        pattern = Pattern.compile(regex);


        try (BufferedReader reader = new BufferedReader(new FileReader(fileName))) {
            String line = "";
            int c = 1;
            while ((line = reader.readLine()) != null) {
                m = pattern.matcher(line);
                if (m.find()) {
                    System.out.println(" " + c + "  " + line);
                }
                c++;
            }

        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## 8-3 利用regular expressions轉換html文件

利用regular expressions將html文件(gettys.html)中的`<p>`標籤轉換成`<span>`標籤. 並且將class屬性值由line轉換成sentence. 將改變的結果列印出來

### 步驟一: 更新SearchReplace類別

1. 建立Pattern物件, 例如`Pattern.compile("(<" + targetTag + ".*?>)(.*?)(</" + targetTag + ".*?>)")`, 準備將文字拆解成三個部份:
  - 開始標籤
  - 內容
  - 結束標籤
1. 在迴圈的每一個循環中
  - 讀取一行文字
  - 建立Matcher物件, 比對這一行文字.
  - 如果有找到吻合的內容, 取代開始與結束的標籤名稱以及class屬性.
1. 建立一個`replaceTag()`方法, 用來取代一個標籤名稱
1. 建立一個`replaceAttribute()`方法, 用來取代一個標籤屬性的值

```java
package com.example.strings;

import java.io.BufferedReader;
import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.IOException;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class SearchReplace {

    private String fileName = "gettys.html";

    public static void main(String[] args) {
        SearchReplace sr = new SearchReplace();
        sr.run();
    }

    public void run() {
        try (BufferedReader reader = new BufferedReader(new FileReader(fileName))) {
            String targetTag = "p";
            String replaceTag = "span";
            String attribute = "class";
            String value = "sentence";
            String line = "";
            int c = 1;
            Pattern pattern1 = Pattern.compile(
		"(<" + targetTag + ".*?>)(.*?)(</" + targetTag + ".*?>)");

            while ((line = reader.readLine()) != null) {
                Matcher m = pattern1.matcher(line);
                if (m.find()) {
                    String newStart = replaceTag(m.group(1), targetTag, replaceTag);
                    newStart = replaceAttribute(newStart, attribute, value);
                    String newEnd = replaceTag(m.group(3), targetTag, replaceTag);

                    String newLine = newStart + m.group(2) + newEnd;
                    System.out.printf("%3d %s\n", c, newLine);
                }
                c++;
            }

        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public String replaceTag(String tag, String targetTag, String replaceTag) {
        Pattern p = Pattern.compile(targetTag);  // targetTag is regex
        Matcher m = p.matcher(tag);  // tag is text to replace
        if (m.find()) {
            return m.replaceFirst(replaceTag); // swap target with replace
        }
        return targetTag;
    }

    public String replaceAttribute(String tag, String attribute, String value) {
        Pattern p = Pattern.compile(attribute + "=" + "\".*?\"");  // targetTag is regex
        Matcher m = p.matcher(tag);  // tag is text to replace
        if (m.find()) {
            return m.replaceFirst(attribute + "=" + "\"" + value + "\"");
        }
        return tag;
    }
}
```

:sweat_smile:

