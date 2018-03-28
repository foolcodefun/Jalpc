---
layout: post
title:  "書籍：Android Programming CH11 挑戰 (ViewPager)"
date:   2018-03-30
desc: "Preference"
keywords: "Android"
categories: [Android]
tags: [Android,The Big Nerd Ranch Guide]
icon: icon-html
---

* ### [一、前言簡介](#1)
* ### [二、Chapter 11 Challenge](#2)
> ### [2.1 挑戰一：Restoring CrimeFragment's Margin](#2.1)
> ### [2.2 挑戰二：Adding First and Last Buttons](#2.2)
* ### [參考資料](#3)

<h2 id="1"></h2>

# 一、前言簡介
本篇文章是 Android Programming 書中第十一章的 Challenge，此次的 Challenge 與 與 ViewPager 的基礎應用有關。

<h2 id="2"></h2>
# 二、Chapter 11 Challenge

<h2 id="2.1"></h2>

## 2.1 挑戰一：Restoring CrimeFragment's Margin

### 題目

當 CrimeFragment 套用到 ViewPager 上時，fragment_crime.xml 裡的 margin 不再運作，這是因為 ViewPager 不支持 margin 屬性。請讀者修改 fragment_crime.xml 使得畫面與原本一樣。

### 解題想法

看到此題的第一個想法是利用 padding 屬性來解決，後來又想到 ConstrainLayout 或許也能解決此問題。以下程式碼是以 ConstrainLayout 為基礎完成的。

fragment_crime.xml

```xml
```


<h2 id="2.2"></h2>

## 2.2 挑戰二：Adding First and Last Buttons

### 題目：

請加上兩個按鈕，使得 ViewPager 可以直接滑動到第一個或是最後一個 Crime。當 ViewPager 在第一個 Crime 時，移動到第一個的按鈕應設為 disable；當 ViewPager 在最後一個 Crime 時，移動到最後一個的按鈕應設為 disable。

### 解題想法：

這題的目的是希望讀者能更熟悉 ViewPager 裡的 OnPageChangeListener 介面以及 Button 的點擊事件 (其實這也是一個介面)。以下程式碼為解題的答案。

CrimePagerActivity.java
```java
```

# 參考資料

[Big Nerd Ranch](https://forums.bignerdranch.com/c/android-programming-the-big-nerd-ranch-guide)