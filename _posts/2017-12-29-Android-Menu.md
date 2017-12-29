---
layout: post
title:  "Android Menu"
date: 2017-12-22
desc: "Android Menu"
keywords: "Android, Menu"
categories: [Android]
tags: [Android,Menu]
icon: icon-html
---

* ### [一、前言簡介](#1)
* ### [二、Activity 中與 Menu 相關的兩個方法](#2)
> ### [2.1 onCreateOptionsMenu](#2.1)
> * ### [(1) Menu XML](#2.1.1)
> * ### [(2) onCreateOptionsMenu 的使用](#2.1.2)
>
> ### [2.2 onOptionsItemSelected](#2.2)
> * ### [(1) Menu XML](#2.2.1)
> * ### [(2) onOptionsItemSelected 的使用](#2.2.2)
* ### [三、完整程式碼](#3)

<h2 id="1"></h2>

# 一、前言

Menu 通常位於螢幕的右上角，用以做設定或搜尋等等功能。

<h2 id="2"></h2>

# 二、Activity 中與 Menu 相關的兩個方法

若 Activity 畫面上需要 Menu 則覆寫 `onCreateOptionsMenu` 和 `onOptionsItemSelected` 兩個方法。`onCreateOptionsMenu` 與 Menu 的產生有關，而當使用者點擊了 Menu 上的 item 則需以 `onOptionsItemSelected` 處理。本章節將會介紹一些 Menu 相關屬性，若讀者想知道更多屬性內容可到[官方網站](https://developer.android.com/guide/topics/resources/menu-resource.html?hl=zh-tw)查詢。

<h2 id="2.1"></h2>

## 2.1 onCreateOptionsMenu

`onCreateOptionsMenu` 是與選單畫面產生相關的方法，使用此方法時須先在 res/menu 資料夾中定義好選單的樣子，再到 `onCreateOptionsMenu` 中利用 `MenuInflater.inflate()` 將 menu 的 xml 檔轉為程式化物件。

<h2 id="2.1.1"></h2>

### (1) Menu XML

此章節將說明 xml 中 Mune 中的 item 有哪些重要屬性以及程式碼對應到的執行畫面。

下面的程式碼位於 res/menu/menu_main.xml，item 中的 title 屬性是必要的。

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:app="http://schemas.android.com/apk/res-auto">

    <item
        android:title="Setting"/>
    <item
        android:title="Delete"/>

</menu>
```

上述 xml 檔中對應到的畫面如下

<img src="{{ site.img_path }}/20171229/menu1_1.png" width="100%" style="max-width:300px;"/>
<img src="{{ site.img_path }}/20171229/menu1_2.png" width="100%" style="max-width:300px;"/>

res/menu/menu_main.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:app="http://schemas.android.com/apk/res-auto">

    <item
        android:title="Setting"
        android:icon="@drawable/arrow_left"/>
    <item
        android:title="Delete"
        android:icon="@drawable/arrow_right"/>

</menu>
```

上述 xml 檔中對應到的畫面如下

<img src="{{ site.img_path }}/20171229/menu2_1.png" width="100%" style="max-width:300px;"/>
<img src="{{ site.img_path }}/20171229/menu2_2.png" width="100%" style="max-width:300px;"/>

res/menu/menu_main.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:app="http://schemas.android.com/apk/res-auto">

    <item
        android:title="Setting"
        android:icon="@drawable/arrow_left"
        app:showAsAction="ifRoom"/>
    <item
        android:title="Delete"
        android:icon="@drawable/arrow_right"/>

</menu>
```

上述 xml 檔中對應到的畫面如下

<img src="{{ site.img_path }}/20171229/menu3_1.png" width="100%" style="max-width:300px;"/>
<img src="{{ site.img_path }}/20171229/menu3_2.png" width="100%" style="max-width:300px;"/>

res/menu/menu_main.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:app="http://schemas.android.com/apk/res-auto">

    <item
        android:title="Setting"
        android:icon="@drawable/arrow_left"
        app:showAsAction="ifRoom"/>
    <item
        android:title="Delete"
        android:icon="@drawable/arrow_right"
        app:showAsAction="ifRoom"/>
</menu>
```

上述 xml 檔中對應到的畫面如下

<img src="{{ site.img_path }}/20171229/menu4.png" width="100%" style="max-width:300px;"/>

<h2 id="2.1.2"></h2>

### (2) onCreateOptionsMenu 的使用

定義好 res/menu/menu_main.xml 後要在 MainActivity.java 覆寫 `onCreateOptionsMenu`

```java
    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        getMenuInflater().inflate(R.menu.menu_main, menu);
        return true;
    }
```

MenuInflater.inflate() 用來將 xml 檔轉為程式化物件，在 Activity 中可用 `getMenuInflater().inflate()`，便可以將 menu_main.xml 佈局轉為程式化物件。

<h2 id="2.2"></h2>

## 2.2 onOptionsItemSelected

當 Menu 上的 item 被點擊後，會呼叫 `onOptionsItemSelected`，`onOptionsItemSelected` 可取得

<h2 id="2.2.1"></h2>

### (1) Menu XML

請在佈局檔為每個 item 加上 id。如此一來，當某個 item 被按到時，該 item 會傳入 `onOptionsItemSelected`。

res/menu/menu_main.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:app="http://schemas.android.com/apk/res-auto">

    <item
        android:id="@+id/main_setting"
        android:title="Setting"/>
    <item
        android:id="@+id/main_delete"
        android:title="Delete"/>

</menu>
```

<h2 id="2.2.2"></h2>

### (2) onOptionsItemSelected 的使用

利用 `item.getItemId()` 來找到對應的 id 並執行相關工作。

```java
    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()) {
            case R.id.main_setting:
                setAction("Setting");
                return true;
            case R.id.main_delete:
                setAction("Delete");
                return true;
            default:
                return super.onOptionsItemSelected(item);
        }
    }

    private void setAction(String string) {
        new AlertDialog.Builder(this)
                .setMessage(string + " Clicked")
                .setPositiveButton("OK", null)
                .show();
    }
```

<h2 id="3"></h2>

# 三、完整程式碼

res/menu/menu_main.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:app="http://schemas.android.com/apk/res-auto">

    <item
        android:id="@+id/main_setting"
        android:title="Setting"/>
    <item
        android:id="@+id/main_delete"
        android:title="Delete"/>

</menu>
```

MainActivity.java

```java
package com.little.testmenu;

import android.os.Bundle;
import android.support.v7.app.AlertDialog;
import android.support.v7.app.AppCompatActivity;
import android.view.Menu;
import android.view.MenuItem;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }

    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        getMenuInflater().inflate(R.menu.menu_main, menu);
        return true;
    }

    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()) {
            case R.id.main_setting:
                setAction("Setting");
                return true;
            case R.id.main_delete:
                setAction("Delete");
                return true;
            default:
                return super.onOptionsItemSelected(item);
        }
    }

    private void setAction(String string) {
        new AlertDialog.Builder(this)
                .setMessage(string + " Clicked")
                .setPositiveButton("OK", null)
                .show();
    }
}
```

