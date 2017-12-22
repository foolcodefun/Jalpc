---
layout: post
title:  "Android JSON"
date: 2017-12-22
desc: "Android JSON"
keywords: "Android, JSON"
categories: [Android]
tags: [Android,JSON]
icon: icon-html
---

* ### [一、前言簡介](#1)
* ### [二、JSON](#2)
> ### [2.1 JSON Object](#2.1)
> ### [2.1 JSON Array](#2.2)
* ### [三、簡易範例](#3)
> ### [3.1 Read JSON 的基本使用方法](#3.1)
> ### [3.2 Write JSON 的基本使用方法](#3.2)
> ### [3.3 範例](#3.3)

<h2 id="1"></h2>

# 一、前言簡介

Android App 常需要讀取檔案或是 server 上的資料，這些資料大多是 JSON 格式，在 Android 框架下使用 org.json 類別庫可以用來解析這些 JSON 資料。JSON，全名  JavaScript Object Nationanl 是一種資料格式，屬於 JavaScript 的子集，因其易讀性、便利性和陣列結構，JSON 格式被廣泛的應用於各種語言之中，有一些非關聯性的資料庫也以 JSON 為儲存資料的方式。本篇文章會解釋 JSON 資料格式以及在 Android 框架下解析 JSON 資料的方法。

<h2 id="2"></h2>

# 二、JSON

要理解 JSON 格式可以先記住兩點：1. Object 在大括號之中 2. Array 在中括號之中。讀者可藉由 [JSON 格式化網站](https://jsoneditoronline.org/) 理解 JSON 格式。

<h2 id="2.1"></h2>

## 2.1 JSON Object

Object 存在與大括號之中，以下範例的大括號中有兩個 JSON Object，分別是 `"name":"Sarah"` 和 `"age":25`，可以看出每個 JSON Object 都是鍵值對 (key and value pair)。

```JSON
{  
    "name":"Sarah", "age":25
}
```

下圖是來自[官方網站](https://www.json.org/)的截圖，重此圖可以知道 key 的資料型態必須是 String，而 Object 與 Object 要用逗號隔開。

<img src="{{ site.img_path }}/20171222/Object.png" width="100%" style="max-width:600px;"/>

下面[這張圖](https://www.json.org/)則說明了 value 的資料型態可以是哪些。

<img src="{{ site.img_path }}/20171222/Value.png" width="100%" style="max-width:600px;"/>

<h2 id="2.2"></h2>

## 2.2 JSON Array

Array 存在於中括號中，以下範例的 Array 中有七個值，所以這個 Array 的長度是七。

```JSON
["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"]  
```

下面[這張圖](https://www.json.org/)則說明了 value 之間要以逗號隔開。

<img src="{{ site.img_path }}/20171222/Array.png" width="100%" style="max-width:600px;"/>

<h2 id="3"></h2>

# 三、簡易範例

本章節會先介紹讀與寫 JSON 的基本使用方法，再以一個範例作為結尾。

<h2 id="3.1"></h2>

## 3.1 Read JSON

把字串丟到 JSONObject 中就可以讀取 JSON Object。

```java
try {
    JSONObject json = new JSONObject(jsonString);
    } catch (Exception e) {
        e.printStackTrace();
    }
```

<h2 id="3.2"></h2>

## 3.2 Write JSON

把鍵值對 put 入 JSONObject 再利用 toString() 方法就可以輸出 JSON 格式資料。

```java
    JSONObject object = new JSONObject();
    try {
        object.put("name", "Sarah");
        object.put("age", new Integer(25));
    } catch (JSONException e) {
        e.printStackTrace();
    }
    System.out.println(object);
```

<h2 id="3.3"></h2>

## 3.3 範例

此程式範例想要解析的 JSON 格式如下，取自於 [JSON 格式化網站](https://jsoneditoronline.org/) 。

```JSON
{
  "array": [
    1,
    2,
    3
  ],
  "boolean": true,
  "null": null,
  "number": 123,
  "object": {
    "a": "b",
    "c": "d",
    "e": "f"
  },
  "string": "Hello World"
}
```

在撰寫程式碼前請各為大大先在 Android 專案的 res 資料夾中新增一個 raw 資料夾，並在此資料夾中新增一個 data.json 檔案如下。再將上述資料複製到 data.json 檔案中。

<img src="{{ site.img_path }}/20171222/data.png" width="100%" style="max-width:400px;"/>

在撰寫程式碼時須先將 data.json 資料讀入，在丟給 JSONObjec，之後再進行 JSON 資料分析。

##步驟一 讀 data.json 檔案

首先用串流的方式讀取資源檔，再把資料全部轉成字串。

```java
        InputStream in = getResources().openRawResource(R.raw.data);
        BufferedReader rd = new BufferedReader(new InputStreamReader(in));
        String line;
        StringBuilder sb = new StringBuilder();
        try {
            while ((line = rd.readLine()) != null) {
                sb.append(line);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
```


##步驟二 JSON 資料分析

首先把字串資料丟入 JSONObject 中，資後自依照資料型態取出資料，程式碼如下。

```java
        JSONObject obj = new JSONObject(sb.toString());
            
        JSONArray array = obj.getJSONArray("array");
        boolean bool = obj.getBoolean("boolean");
        String snull = obj.getString("null");
        int num = obj.getInt("number");
        JSONObject item = obj.getJSONObject("object");
        String s = obj.getString("string");

```

## 完整程式碼以及執行結果

以下為完整程式碼以及執行結果

res/raw/data.json

```JSON
{
  "array": [
    1,
    2,
    3
  ],
  "boolean": true,
  "null": null,
  "number": 123,
  "object": {
    "a": "b",
    "c": "d",
    "e": "f"
  },
  "string": "Hello World"
}
```

activity_main.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.little.testjson.MainActivity">

    <TextView
        android:id="@+id/array"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="32dp"
        android:text="array:"
        android:textAppearance="@style/TextAppearance.AppCompat.Large"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"/>

    <TextView
        android:id="@+id/bool"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="16dp"
        android:text="boolean:"
        android:textAppearance="@style/TextAppearance.AppCompat.Large"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/array"/>

    <TextView
        android:id="@+id/snull"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="16dp"
        android:text="null:"
        android:textAppearance="@style/TextAppearance.AppCompat.Large"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/bool"/>

    <TextView
        android:id="@+id/number"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="16dp"
        android:text="number:"
        android:textAppearance="@style/TextAppearance.AppCompat.Large"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/snull"/>

    <TextView
        android:id="@+id/object"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="16dp"
        android:text="object:"
        android:textAppearance="@style/TextAppearance.AppCompat.Large"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/number"/>

    <TextView
        android:id="@+id/string"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="16dp"
        android:text="string:"
        android:textAppearance="@style/TextAppearance.AppCompat.Large"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/object"/>

</android.support.constraint.ConstraintLayout>

```

Data.java，此類別是專門設計用來傳輸同樣 JSON 格式的資料，可以增加程式的可讀性。

```java
package com.little.testjson;

import org.json.JSONArray;
import org.json.JSONException;
import org.json.JSONObject;

/**
 * Created by sarah on 22/12/2017.
 */

public class Data {

    JSONArray array;
    boolean bool;
    String snull;
    int num ;
    JSONObject item ;
    String s ;

    private Data() {
    }

    public Data(JSONObject obj) throws JSONException {
        this(obj.getJSONArray("array"),
                obj.getBoolean("boolean"),
                obj.getString("null"),
                obj.getInt("number"),
                obj.getJSONObject("object"),
                obj.getString("string"));
    }

    public Data(JSONArray array, boolean bool, String snull, int num, JSONObject item, String s) {
        this.array = array;
        this.bool = bool;
        this.snull = snull;
        this.num = num;
        this.item = item;
        this.s = s;
    }

    public JSONArray getArray() {
        return array;
    }

    public void setArray(JSONArray array) {
        this.array = array;
    }

    public boolean isBool() {
        return bool;
    }

    public void setBool(boolean bool) {
        this.bool = bool;
    }

    public String getSnull() {
        return snull;
    }

    public void setSnull(String snull) {
        this.snull = snull;
    }

    public int getNum() {
        return num;
    }

    public void setNum(int num) {
        this.num = num;
    }

    public JSONObject getItem() {
        return item;
    }

    public void setItem(JSONObject item) {
        this.item = item;
    }

    public String getS() {
        return s;
    }

    public void setS(String s) {
        this.s = s;
    }
}


```

MainActivity.java

```java
package com.little.testjson;

import android.support.annotation.NonNull;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
import android.widget.TextView;

import org.json.JSONArray;
import org.json.JSONException;
import org.json.JSONObject;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.util.Date;

public class MainActivity extends AppCompatActivity {

    private static final String TAG = MainActivity.class.getSimpleName();
    private TextView mTvArray;
    private TextView mTvBool;
    private TextView mTvNull;
    private TextView mTvNumber;
    private TextView mTvObject;
    private TextView mTvString;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        findViews();
        StringBuilder sb = readJSONData(R.raw.data);
        Data data = getData(sb);
        setViewText(data.getArray(),
                    data.isBool(),
                    data.getSnull(),
                    data.getNum(),
                    data.getItem(),
                    data.getS());
    }

    @NonNull
    private Data getData(StringBuilder sb){
        JSONObject obj;
        Data data = null;
        try {
            obj = new JSONObject(sb.toString());
            data = new Data(obj);
        } catch (JSONException e) {
            e.printStackTrace();
        }
        return data;
    }

    @NonNull
    private StringBuilder readJSONData(int res){
        InputStream in = getResources().openRawResource(res);
        BufferedReader rd = new BufferedReader(new InputStreamReader(in));
        String line;
        StringBuilder sb = new StringBuilder();
        try {
            while ((line = rd.readLine()) != null) {
                sb.append(line);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        return sb;
    }

    private void setViewText(JSONArray array, boolean bool, String snull, int num, JSONObject item, String s) {
        mTvArray.setText(mTvArray.getText() + array.toString());
        mTvBool.setText(mTvBool.getText() + String.valueOf(bool));
        mTvNull.setText(mTvBool.getText() + String.valueOf(snull));
        mTvNumber.setText(mTvBool.getText() + String.valueOf(num));
        mTvObject.setText(mTvBool.getText() + item.toString());
        mTvString.setText(mTvBool.getText() + s);
    }

    private void findViews() {
        mTvArray = findViewById(R.id.array);
        mTvBool = findViewById(R.id.bool);
        mTvNull = findViewById(R.id.snull);
        mTvNumber = findViewById(R.id.number);
        mTvObject = findViewById(R.id.object);
        mTvString = findViewById(R.id.string);
    }
}

```

執行結果

<img src="{{ site.img_path }}/20171222/execute.png" width="100%" style="max-width:400px;"/>


