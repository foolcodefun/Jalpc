---
layout: post
title:  "Android Layout 基本介紹"
date: 2017-10-06
desc: "Layout 基本介紹"
keywords: "Android, ListView, Layout"
categories: [Android]
tags: [Android,Layout]
icon: icon-html
---

# 一、前言

在 android app 開發中，使用者介面 ( Ueser Interface, UI ) 設計是最重要的課題之一。android 手機是一個開放性的平台，手機的螢幕有非常多種尺寸，而一個好的 UI 設計必須在不同螢幕尺寸和解析度下看起來有相同的畫面。android 的介面相關元件皆在 android.view 或 android.widget 的套件中，撰寫畫面的程式為 XML，位於 res\layout 目錄下。下圖是一張畫面類別的繼承圖，其中 Layout 是 UI 設計中基本的區域，本篇文章介紹幾種常見的 Layout 供讀者參考。

<img src="{{ site.img_path }}/20171006/UMLView.png" width="100%">


# 二、Layout 屬性

本章節介紹幾個常見的排版屬性。


### 單位

進行版面排版時常需要用到安排元間的長寬或彼此之間的距離，以下是常見到的單位

* px：螢幕實際像素。
* dp：也是 dip，每英吋螢幕，160dp 為一英吋。
* sp：和dp相似，根據裝置設定的字型大小自動調整。
* in：英吋。
* mm：公厘。
* pt：一個點。

### 畫面元素必備的長寬設定

* android:layout_width：元件的寬度。
* android:layout_height：元件的高度。

例如：android:layout_width= "200dp"，android:layout_width= "matchparent"，android:layout_width= "wrapcontent"。

### 與其他元件的間格

* android:layout_marginLeft：與左方元件的左側的間距。
* android:layout_marginRight：與右方元件的右側的間距。
* android:layout_marginTop：與上方元件的上方的間距。
* android:layout_marginBottom：與下方元件的下方的間距。
* android:layout_margin：上、下、左、右為同樣的間距。

### 自身使用空間與顯示內容的間格

* android:paddingLeft：內容左側與使用空間邊界的間距。
* android:paddingRight：內容右側與使用空間邊界的間距。
* android:paddingTop：內容上方與使用空間邊界的間距。
* android:paddingBottom：內容下方與使用空間邊界的間距。
* android:padding：內容上、下、左、右與使用空間邊界為同樣的間距。

### 內容位置設定 

* android:layout_gravity

其設定值可為如下：
* top、bottom、left、right：其內容對齊上、下、左、右。
* fillvertical、fillhorizontal：其內容佔滿垂直或水平空間。
* fill：其內容佔滿垂直與水平空間。
* centervertical、centerhorizontal：其內容對齊垂直或水平的中央。
* center：其內容對齊垂直與水平的中央。

例如：android:gravity＝"bottom"，android:gravity＝"bottom|right"

### 佔用空間比例

此屬性會按照剩餘的空間依照權重比例分配。

* android:layout_weight：比例設定

# 三、Layout 種類

本章節將介紹五種常見的 Layout，分別是LinearLayout、TableLayout、RelativeLayout、ConstraintLayout 和 FrameLayout，並介紹其基本用法。

> LinearLayout：此種版面限制其子類別以單一方向進行版面配置，其方向可為水平 ( 由左到右 ) 或是垂直 ( 由上到下 )。

垂直方線的 LinearLayout 透過 `android:orientation="vertical"` 設定
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_margin="5dp"
        android:gravity="center"
        android:background="#C1FFC1"
        android:layout_weight="1"
        android:text="This is a vertical lineralayout " />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_margin="5dp"
        android:gravity="center"
        android:background="#C1FFC1"
        android:layout_weight="1"
        android:text="This is a vertical lineralayout " />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_margin="5dp"
        android:gravity="center"
        android:background="#C1FFC1"
        android:layout_weight="1"
        android:text="This is a vertical lineralayout " />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_margin="5dp"
        android:gravity="center"
        android:background="#C1FFC1"
        android:layout_weight="1"
        android:text="This is a vertical lineralayout " />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_margin="5dp"
        android:gravity="center"
        android:background="#C1FFC1"
        android:layout_weight="1"
        android:text="This is a vertical lineralayout " />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_margin="5dp"
        android:gravity="center"
        android:background="#C1FFC1"
        android:layout_weight="1"
        android:text="This is a vertical lineralayout " />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_margin="5dp"
        android:gravity="center"
        android:background="#C1FFC1"
        android:layout_weight="1"
        android:text="This is a vertical lineralayout " />

</LinearLayout>

```

程式執行結果

<img src="{{ site.img_path }}/20171006/LinearVertical.png" width="35%">


水平方向的 LinearLayout 透過 `android:orientation="horizontal"` 設定
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:layout_margin="5dp"
        android:gravity="center"
        android:background="#C1FFC1"
        android:layout_weight="1"
        android:text="This is a horizontal lineralayout " />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:layout_margin="5dp"
        android:gravity="center"
        android:background="#C1FFC1"
        android:layout_weight="1"
        android:text="This is a horizontal lineralayout " />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:layout_margin="5dp"
        android:gravity="center"
        android:background="#C1FFC1"
        android:layout_weight="1"
        android:text="This is a horizontal lineralayout " />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:layout_margin="5dp"
        android:gravity="center"
        android:background="#C1FFC1"
        android:layout_weight="1"
        android:text="This is a horizontal lineralayout " />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:layout_margin="5dp"
        android:gravity="center"
        android:background="#C1FFC1"
        android:layout_weight="1"
        android:text="This is a horizontal lineralayout " />
</LinearLayout>
```

程式執行結果

<img src="{{ site.img_path }}/20171006/LinearHorizontal.png" width="35%">

> TableLayout：在表格畫面的實現上可利用此版面，此版面需使用 TableRow 將畫面切個成表格。

以下為排版程式碼，程式碼的每一個 TableRow 都有不一樣的排版嘗試，讀者可觀察程式碼與執行結果間的關係。

```xml
<?xml version="1.0" encoding="utf-8"?>
<TableLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">
    <TableRow
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_margin="5dp">
        <TextView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:background="#FFB7DD"
            android:text="Row 1"
            android:layout_weight="1"
            android:gravity="center" />
    </TableRow>
    <TableRow
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <TextView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:background="#FFB7DD"
            android:text="Row 2"
            android:layout_margin="5dp"
            android:gravity="center"
            android:layout_weight="1" />
    </TableRow>
    <TableRow
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <TextView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:background="#FFB7DD"
            android:text="Row 3"
            android:gravity="center" />
    </TableRow>
    <TableRow
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <TextView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:background="#FFB7DD"
            android:text="Row 4"
            android:layout_margin="5dp"
            android:gravity="center" />
    </TableRow>
    <TableRow
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_margin="5dp">
        <TextView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:background="#FFB7DD"
            android:text="Row 5 colum 1"
            android:gravity="center" />
        <TextView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:background="#FFB7DD"
            android:text="Row 5 colum 2"
            android:gravity="center" />
        <TextView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:background="#FFB7DD"
            android:text="Row 5 colum 3"
            android:gravity="center" />
    </TableRow>
    <TableRow
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <TextView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:background="#FFB7DD"
            android:text="Row 6 colum 1"
            android:gravity="center" />
        <TextView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:background="#FFB7DD"
            android:text="Row 6 colum 2"
            android:gravity="center" />
        <TextView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:background="#FFB7DD"
            android:text="Row 6 colum 3"
            android:gravity="center" />
    </TableRow>
    <TableRow
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <TextView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:background="#FFB7DD"
            android:text="Row 7 colum 1"
            android:layout_margin="5dp"
            android:gravity="center" />
        <TextView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:background="#FFB7DD"
            android:text="Row 7 colum 2"
            android:layout_margin="5dp"
            android:gravity="center" />
        <TextView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:background="#FFB7DD"
            android:text="Row 7 colum 3"
            android:layout_margin="5dp"
            android:gravity="center" />
    </TableRow>
    <TableRow
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <TextView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:background="#FFB7DD"
            android:text="Row 8 colum 1"
            android:layout_weight="1"
            android:layout_margin="5dp"
            android:gravity="center" />
        <TextView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:background="#FFB7DD"
            android:text="Row 8 colum 2"
            android:layout_margin="5dp"
            android:layout_weight="1"
            android:gravity="center" />
        <TextView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:background="#FFB7DD"
            android:text="Row 8 colum 3"
            android:layout_margin="5dp"
            android:layout_weight="1"
            android:gravity="center" />
    </TableRow>
    <TableRow
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <TextView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:background="#FFB7DD"
            android:text="Row 9 colum 1"
            android:layout_weight="1"
            android:layout_margin="5dp"
            android:gravity="center" />
        <TextView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:background="#FFB7DD"
            android:text="Row 9 colum 2"
            android:layout_margin="5dp"
            android:layout_weight="1"
            android:gravity="center" />
        <TextView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:background="#FFB7DD"
            android:text="Row 9 colum 3"
            android:layout_margin="5dp"
            android:layout_weight="1"
            android:gravity="center" />
        <TextView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:background="#FFB7DD"
            android:text="Row 9 colum 3"
            android:layout_margin="5dp"
            android:layout_weight="1"
            android:gravity="center" />
    </TableRow>
</TableLayout>
```

程式執行結果

<img src="{{ site.img_path }}/20171006/Table.png" width="35%">

> RelativeLayout：不規則的介面和使用此種版面，此版面可以設定元件與元件間的相對位置。使用時須先命名元件的 id，這樣子元件才能認得彼此。

```xml
<RelativeLayout
    android:id="@+id/RLayout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_margin="5dp"
    xmlns:android="http://schemas.android.com/apk/res/android" >
    <TextView
        android:id="@+id/TexView1"
        android:layout_width="200dp"
        android:layout_height="wrap_content"
        android:text="TextView1"
        android:background="#DDDDDD"
        android:gravity="center"/>
    <TextView
        android:id="@+id/TexView2"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="TextView2"
        android:background="#DDDDDD"
        android:gravity="center"
        android:layout_marginLeft="5dp"
        android:layout_toRightOf="@id/TexView1"/>
    <TextView
        android:id="@+id/TexView3"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="TextView3"
        android:background="#DDDDDD"
        android:gravity="center"
        android:layout_marginTop="5dp"
        android:layout_below="@id/TexView2"/>
</RelativeLayout>
```

程式執行結果

<img src="{{ site.img_path }}/20171006/Relative.png" width="35%">


> ConstraintLayout：此版面有效的提高畫面的效率並解決巢狀結構的問題。其使用可藉由可視化介面拉出且需至少設定好一個水平和一個垂直方向的限制。

可視化編緝頁面

<img src="{{ site.img_path }}/20171006/Constrainxml.png" width="35%">

程式執行結果

<img src="{{ site.img_path }}/20171006/Constrain.png" width="35%">

> FrameLayout：此版面用以固定一個區域給單一元件使用，若要加入多個元件可以 android:layout_gravity 指定其位置。

```xml
<FrameLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    xmlns:android="http://schemas.android.com/apk/res/android" >


    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="1"
        android:layout_gravity="left|top"
        android:gravity="center"
        android:background="#FFFFBB"
        android:layout_margin="5dp"
        android:textSize="40dp"/>
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="2"
        android:layout_gravity="center|top"
        android:gravity="center"
        android:background="#FFFFBB"
        android:layout_margin="5dp"
        android:textSize="40dp"/>
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="3"
        android:layout_gravity="right|top"
        android:gravity="center"
        android:background="#FFFFBB"
        android:layout_margin="5dp"
        android:textSize="40dp"/>
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="4"
        android:layout_gravity="left|center"
        android:gravity="center"
        android:background="#FFFFBB"
        android:layout_margin="5dp"
        android:textSize="40dp"/>
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="5"
        android:layout_gravity="center"
        android:gravity="center"
        android:background="#FFFFBB"
        android:layout_margin="5dp"
        android:textSize="40dp"/>
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="6"
        android:layout_gravity="right|center"
        android:gravity="center"
        android:background="#FFFFBB"
        android:layout_margin="5dp"
        android:textSize="40dp"/>
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="7"
        android:layout_gravity="left|bottom"
        android:gravity="center"
        android:background="#FFFFBB"
        android:layout_margin="5dp"
        android:textSize="40dp"/>
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="8"
        android:layout_gravity="center|bottom"
        android:gravity="center"
        android:background="#FFFFBB"
        android:layout_margin="5dp"
        android:textSize="40dp"/>
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="9"
        android:layout_gravity="right|bottom"
        android:gravity="center"
        android:background="#FFFFBB"
        android:layout_margin="5dp"
        android:textSize="40dp"/>
</FrameLayout>
```

程式執行結果

<img src="{{ site.img_path }}/20171006/Frame.png" width="35%">

