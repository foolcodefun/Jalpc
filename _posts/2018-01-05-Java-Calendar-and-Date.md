---
layout: post
title:  "Java Calender and Date 的基本使用"
date: 2018-01-05
desc: "Java Calender Date"
keywords: "Calender, Date"
categories: [Java]
tags: [Java,Calender,Date]
icon: icon-html
---

* ### [前言簡介](#1)
* ### [二、Date](#2)
* ### [三、DateFormat](3)
* ### [四、Calender](#4)

<h2 id="1"></h2>

# 一、前言

在 Java 開發中，日期處理是非常複雜的一塊，時間處理要考慮時間的度量以及年曆的種類。本篇文章將介紹 java.util.Date、java.text.DateFormat、java.util.Calendar 和 java.util.TimeZone 的基本概念以及使用。

<h2 id="2"></h2>

# 二、Date

以 java.util.Date 的時間是 epoch 毫秒數，也就是 1970 年１月１日 00:00:00 UTC 後經過的毫秒數。此類別封裝的時間數字不具閱讀意義，如下程式碼及其執行結果。


Date 基本使用程式碼：

```java
Date date = new Date();
System.out.print(date.getTime());
```

執行結果：

```
1515073083674
```

Date 的無引數建構子是以 System.currentTimeMillis() 取得現在時間的 epoch 毫秒數。而這一長串的數字對人類而言不可閱讀，若要轉換成我們看得懂時間則需要 DateFormat 的幫忙。

<h2 id="３"></h2>

# 三、DateFormat

java.text.DateFormat 是一個用來處理字串時間格式與 Date 格式轉換的類別。常用的類別為 SimpleDateFormat，使用者可以任意的規定其時間顯示的格式，SimpleDateFormat 會將 Date 轉為現在所在時區的時間。

利用 SimpleDateFormat 將 Date 轉乘累牘得懂的時間的程式碼： 

```java
Date date = new Date();
DateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
String sdate= dateFormat.format(date);
```

執行結果：

```
2018-01-04 22:11:11
```

若要將文字格式轉成 Date 則需用到 `parse()` 方法

```java
DateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
try {
    Date date = dateFormat.parse("2018-01-04 22:11:11");    
    Log.d(TAG,date.getTime()+"");
} catch (ParseException e) {
    e.printStackTrace();
}
```

執行結果：

```
1515075071000
```

# 四、Calendar

java.util.Calendar 的應用多在時間的加減以及比較。

取得現在時間

```java
Calendar now = Calendar.getInstance();
Log.d(TAG, "onSharedPreferenceChanged: "+now.get(Calendar.YEAR)+"-"
    +now.get(Calendar.MONTH)+"-"
    +now.get(Calendar.DAY_OF_MONTH)+" "
    +now.get(Calendar.HOUR)+":"
    +now.get(Calendar.MINUTE)+":"
    +now.get(Calendar.SECOND)
); 
```

```執行結果
2018-0-4 10:50:49
```

比照執行結果與電腦上的時會發現月份應該是１才對，這是因為 Calendar 對月份的列舉從零開始。

對時間做加減

```java
Calendar calendar = Calendar.getInstance();
calendor.add(Calendar.YEAR,1)//對時間加１年
calendor.add(Calendar.MONTH,-2)//對時間減去２個月
calendor.add(Calendar.DATE,3)//對時間加３天
```

比較時間前後則可用 `after()` 或 `before()` 方法。




