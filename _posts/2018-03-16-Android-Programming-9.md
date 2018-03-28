---
layout: post
title:  "書籍：Android Programming CH9 挑戰 (Date)"
date:   2018-03-16
desc: "Preference"
keywords: "Android"
categories: [Android]
tags: [Android,The Big Nerd Ranch Guide]
icon: icon-html
---

* ### [一、前言簡介](#1)
* ### [二、Chapter 9 Challenge](#2)

<h2 id="1"></h2>

# 一、前言簡介
本篇文章是 Android Programming 書中第九章的 Challenge，此次的 Challenge  與日期格式有關，而日期格式的基本使用可參考 [Java Calendar and Date 的基本使用](https://foolcodefun.github.io/blog/java/2018/01/05/Java-Calendar-and-Date.html)。

<h2 id="1"></h2>
# 二、Chapter 9 Challenge

第九章的題目挑戰要求將日期的顯示格式改為類似 "Monday, Jul 15, 2018" 的格式，藉由 [JAVA 的文件](https://docs.oracle.com/javase/7/docs/api/java/text/SimpleDateFormat.html)可找到年月日相對應的代號，在對程式進行相對應的修改即可。

請修改 CrimeListFragment.java 中 CrimeHolder 內部類別的 `bind(crime)` 方法，如下。
```java
public void bind(Crime crime) {
    mCrime = crime;
    mTitleTextView.setText(mCrime.getTitle());

    DateFormat dateFormat = new SimpleDateFormat("EEEE, MMM dd, yyy");
    String date = dateFormat.format(crime.getDate());
    mDateTextView.setText(date);
            
    mSolveImageView.setVisibility(crime.isSolved()?View.VISIBLE:View.INVISIBLE);
}
```

<img src="{{ site.img_path }}/20180316/chapter9challenge.png" width="100%" style="max-width:350px;"/>