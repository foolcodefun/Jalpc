---
layout: post
title:  "書籍：Android Programming CH10 挑戰"
date:   2018-03-23
desc: "Preference"
keywords: "Android"
categories: [Android]
tags: [Android,The Big Nerd Ranch Guide]
icon: icon-html
---

* ### [一、前言簡介](#1)
* ### [二、Chapter 10 Challenge](#2)
> ### [2.1 挑戰一：Efficient RecyclerView Reloading](#2.1)
> ### [2.2 挑戰二：Improving CrimeLab Performance](#2.2)
* ### [考資料](#3)

<h2 id="1"></h2>

# 一、前言簡介
本篇文章是 Android Programming 書中第十章的 Challenge，此次的 Challenge 與 RecyclerView 的重載以及 Java Collection 的效率有關。

<h2 id="1"></h2>
# 二、Chapter 10 Challenge

## 2.1 挑戰一：Efficient RecyclerView Reloading
每次載入RecyclerView 時最多只會有一個 Crime 的資料有變化，因此在原本的程式碼中使用 `notifyDataSetChanged()` 方法將 RecyclerView 中所有 Crime 資料更新是效率較差的做法。比較有效率的方法是找出哪一個 Crime 資料更動，並用 `notifyItemChanged(int position)` 更新 RecyclerView 的 Crime 資料。

### 解題想法

CrimeListFragment.java
```java
```

## 2.2 挑戰二：Improving CrimeLab Performance
此挑戰中的 CrimeLab 用 ArrayList 儲存了許多 Crime，`getCrime(UUID)` 方法可以從 CrimeLab 尋找特定的 Crime，但是效率上較差，因此挑戰中希望讀者看以尋找更有效率的方法並且不改變 RecyclerView 中顯示 Crime 的順序。

### 解題想法：

CrimeLab.java
```java
```

CrimeListFragment.java
```java
```

# 參考資料

<img src="{{ site.img_path }}/20180316/chapter9challenge.png" width="100%" style="max-width:350px;"/>