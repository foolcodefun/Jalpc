---
layout: post
title:  "Android Layout 基本介紹以及客製化"
date: 2017-10-06
desc: "Layout 基本介紹"
keywords: "Android, ListView, Layout"
categories: [Android]
tags: [Android,Layout]
icon: icon-html
---

# 一、前言

在 android app 開發中，使用者介面 ( Ueser Interface, UI ) 設計是最重要的課題之一。android 手機是一個開放性的平台，手機的螢幕有非常多種尺寸，而一個好的 UI 設計必須在不同螢幕尺寸和解析度下看起來有相同的畫面。android 的介面相關元件皆在 android.view 或 android.widget 的套件中。在 android app 應用程式畫面的程式為 XML，位於「res\layout」目錄下。下圖是一張畫面類別的繼承圖，其中 Layout 是 UI 設計中基本的區域，本篇文章介紹幾種常見的 Layout 供讀者參考。

<img src="{{ site.img_path }}/20170929/ViewLayout.png" width="30%">

# 二、Layout 種類 (畫圖講概念)


>1. LinearLayout：此種版面限制其子類別以單一方向進行版面配置，其方向可為水平 ( 由左到右 ) 或是垂直 ( 由上到下 )。

畫圖截圖加上程式碼

>2. AbsoluteLayout：此版面缺乏彈性，必須使用 android:layout_x 定 x 軸位置而 y 軸則由 android:layout_y 定義。

畫圖截圖加上程式碼

>3. TableLayout：在表格畫面的實現上可利用此版面，此版面需使用 TableRow 將畫面切個成表格。

畫圖截圖加上程式碼

>4. RelativeLayout：不規則的介面和使用此種版面，此版面可可以設定元件與元件間的相對位置。

畫圖截圖加上程式碼

>5. ConstraintLayout：

畫圖截圖加上程式碼



簡單的介紹以及程式範例參考 https://www.tutorialspoint.com/android/android_ui_design.htm
http://www.viralandroid.com/2015/11/android-user-interface-ui-design-tutorial.html

# 三、Layout 屬性

其他屬性參考 https://www.tutorialspoint.com/android/android_ui_design.htm
https://www.tutorialspoint.com/android/android_user_interface_layouts.htm

## 畫面元素必須有長寬設定
android:layout_width：設定畫面元件的寬度
android:layout_height：設定畫面元件的高度
「matchparent」或「wrapcontent」
「android:layout_width= "120sp"」

單位
px：螢幕畫素。
dp：每英吋畫面，160dp為一英吋。
sp：和dp一樣，不過會根據裝置設定的字型大小自動調整。
in：英吋。
mm：公厘。

## 與其他元件的間格
android:marginTop：設定上方的間隔。
android:marginBottom：設定下方的間隔。
android:marginLeft：設定左側的間隔。
android:marginRight：設定右側的間隔。
android:margin：設定上、下、左、右為同樣的間隔。

## 自身使用空間與顯示內容的間格
android:paddingTop：設定內容上方的間隔。
android:paddingBottom：設定內容下方的間隔。
android:paddingLeft：設定內容左側的間隔。
android:paddingRight：設定內容右側的間隔。
android:padding：設定內容上、下、左、右為同樣的間隔。

## 內容位置設定 android:gravity
top、bottom、left、right：設定畫面元件的內容對齊上、下、左、右。
centervertical、centerhorizontal：設定畫面元件的內容對齊垂直或水平的中央。
center：設定畫面元件的內容對齊垂直與水平的中央。
fillvertical、fillhorizontal：設定畫面元件的內容佔滿垂直或水平空間。
fill：設定畫面元件的內容佔滿垂直與水平空間。
bottom|right」，多個設定值之間使用「｜」隔開

## 文字設定
如果是可以顯示文字內容的畫面元件，例如文字（TextView）、按鈕（Button）或輸入（EditText）元件，可以為它們加入文字內容與樣式設定：

android:text：設定畫面元件的文字內容。
android:textSize：設定文字的大小。
android:textAppearance：使用系統的預設值設定文字的大小，設定的格式為「?android:attr/設定值」，有textAppearanceLarge（大）、textAppearanceMedium（中）和textAppearanceSmall（小）三種設定值。
android:textColor：設定文字的顏色。
android:background：設定背景顏色。

# 範例
計算機之類的




