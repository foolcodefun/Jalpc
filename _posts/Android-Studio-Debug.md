---
layout: post
title:  "Android Studio Debug"
date: 2017-12-19
desc: "Android Debug"
keywords: "Android, Debug"
categories: [Android]
tags: [Android,Debug]
icon: icon-html
---
# 目錄

* ### [一、前言簡介](#1)
* ### [二、透過 Logcat 追查例外事件的訊息](#2)
> ### [2.1 Logcat 的基本操作](#2.1)
> * ### [2.1.1 Logcat 視窗操作說明](#2.1.1)
> * ### [2.1.2 Logcat 中顯示例外事件以及 Stack Trace](#2.1.2)
>
> ### [2.2 Log 日誌](#2.2) 
> * ### [2.2.1 Log 的基本使用](#2.2.1)
> * ### [2.2.2 Logging Stack Traces](#2.2.2)
* ### [三、使用 Layout Inspector Debug Layout](#3)
* ### [四、斷點](#4)
> ### [4.1 斷點管理區](#4.1)
> ### [4.2 斷點分類](#4.2)
> ### [4.3 查看變數值](#4.3)
* ### [五、Android Lint](#5)
* ### [六、R 檔](#6)
* ### [七、查看記憶體分配](#7)
* ### [八、參考資料](#8)

<h2 id="1"></h2>

# 一、簡介前言

在開發 App 的過程中，難免會有 Bug 產生，這些 Bug 可能會造成程式執行失敗或執行結果不如預期，因此 Debug 是開發人員的必備技能之一。Android Studio 是 Android 開發的主要 IDE，本篇文章會利用[此專案](https://github.com/foolcodefun/-Android-TestListView)介紹如何使用 Android Studio 的 Logcat、Breakpoint (斷點)、Dugger、Lint 和 Monitors 等工具及方法來進行 Debug，讀者可匯入[此專案](https://github.com/foolcodefun/-Android-TestListView)並照著以下的說明嘗試 Android Studio 的 Debug 功能及方法。

在進入 Debug 說明前，先來介紹上段所說的[專案](https://github.com/foolcodefun/-Android-TestListView)功能，讀者先將此專案從 Github 中下載，並透過 File -> Open 選擇要開啟的專案接著按下 ok 按鈕即可開啟[專案](https://github.com/foolcodefun/-Android-TestListView)，其說明圖片可參考圖1.1和圖1.2。這個[專案](https://github.com/foolcodefun/-Android-TestListView)其實就是 [Android ListView]() 介紹的客製化 ListView 的內容，其執行結果如圖1.3所示。

**圖1.1 File -> Open**

<img src="{{ site.img_path }}/20171124/openProject.png" width="100%" style="max-width:400px;"/>

**圖1.2 開啟專案**

<img src="{{ site.img_path }}/20171124/fileopen.png" width="100%" style="max-width:350px;"/>

**圖1.3 執行結果**

<img src="{{ site.img_path }}/20171124/execution.png" width="100%" style="max-width:350px;"/>

<h2 id="2"></h2>

# 二、Logcat 的使用

Logcat 是一個可以在 App 執行時顯示系統訊息的地方，本章將介紹 Logcat 的基本使用方法並搭配 Log 類別進行說明。

<h2 id="2.1"></h2>

## 2.1 Logcat 的基本操作

這一小節中會說明 Logcat 視窗的基本操作方法以及展示一個例外事件和 Stack Trace 在 Logcat 顯示的樣子。

<h2 id="2.1"></h2>

# 2.1.1 Logcat 視窗操作說明

在 Android Studio 的最下方可以找到 Android Monitor，點進去之後會發現有兩個分頁，請點選 Logcat 分頁。Logcat 會在 App 運作時即時顯示 App 運行的情形，如圖4.1所示。以下會以列點的方式說明 Logcat 的操作方法。

**圖2.1 操作視窗**

<img src="{{ site.img_path }}/20171124/logcatOperation.png" width="100%" style="max-width:800px;"/>

> (1) Clear logcat：此按鈕可以用來清除 Logcat 中展示的所有訊息。
>
> (2) Scroll to the end：按下此按鈕，頁面會來到最下方，可用來查看最新訊息。
>
> (3) 
>
> (4)
>
> (5) Use soft wraps：此按鈕可將 Logcat 中的內容調整成符合視窗的寬度，讀者不需再橫向的移動視窗。
>
> (6) Print：列印出 Logcat 訊息，也可用來存成 PDF 檔。
>
> (7) Restart：當 Logcat 的訊息輸出變得卡卡的可以按此按鈕，如此一來他會重新開始並展示之前的所有訊息。
>
> (8) Logcat header：點開後會看到圖2.2中的視窗，使用者可藉由此工具調整 Logcat 上輸出的訊息格式。
>
> (9) Screen capture：手機螢幕的截圖。
>
> (10) Screen record：對手機螢幕錄影，時常最長三分鐘。

**圖2.2 Logcat header**

<img src="{{ site.img_path }}/20171124/logcatConfigure.png" width="100%" style="max-width:800px;"/>

# 2.1.2 Logcat 中顯示例外事件以及 Stack Trace

Strack Trace

## 2.2 Log 日誌



### 2.2.1 Log 的基本使用

Log 的基本使用方法可參閱 [Android Log](https://foolcodefun.github.io/blog/android/2017/10/17/Android-Log.html)

### 2.2.2 Logging Stack Traces 

# 三、使用 Layout Inspector Debug Layout

<img src="{{ site.img_path }}/20171124/startLayoutInspector.png" width="100%" style="max-width:400px;"/>

<img src="{{ site.img_path }}/20171124/layoutInspector.png" width="100%" style="max-width:900px;"/>

# 四、斷點

在偵錯模式下，設斷點是個很好的 Debug 方法，圖3.1紅框內的第一個按鈕是用來開啟偵錯模式的，第二個按鈕可以將 Debug 模式套用在正在執行的 App 上。

<img src="{{ site.img_path }}/20171124/startDebug.png" width="100%" style="max-width:700px;"/>

## 3.1 斷點管理區



## 3.2 斷點分類

### 條件斷點
### 日誌斷點
### 異常斷點
### 方法斷點
### 屬性斷點

## 3.3 查看變數值

# 五、Android Lint

# 六、R 檔

# 七、查看記憶體分配

# 八、參考資料

[Android Programing The Big Nerd Ranch Guide](https://www.bignerdranch.com/books/android-programming/)
[Android Studio Debug调试详解](http://www.jianshu.com/p/9fbf316582e3)
[你所不知道的Android Studio调试技巧](http://www.jianshu.com/p/011eb88f4e0d)
[AndroidStudio高级代码调试功能](http://www.jianshu.com/p/30aa9f25fa52)
[Android 官方網站](https://developer.android.com/studio/debug/index.html)