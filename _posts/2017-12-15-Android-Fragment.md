---
layout: post
title:  "Android Fragment 入門"
date: 2017-12-13
desc: "Android Fragmetn"
keywords: "Android, Fragment"
categories: [Android]
tags: [Android,Fragment]
icon: icon-html
---

* ### [一、前言簡介](#1)
* ### [二、Fragment 的基本方法](#2)
> ### [2.1 在 layout 加入 fragment](#2.1)
> ### [2.1 在 activity 程式碼中加入 fragment](#2.2)
* ### [三、Fragment 的生命週期](#3)
* ### [四、Fragment 的進階使用](#4)
> ### [4.1 程式架構](#4.1)
> ### [4.2 程式碼以及說明](#4.2)
* ### [參考資料](#5)

<h2 id="1"></h2>

# 一、前言簡介

Fragment 是 Android 3.0 (API 11) 推出的一個重大功能，主要功能可使 App 畫面在不同大小的螢幕上顯示時有不一樣的表現。以下圖為例，在小螢幕手機上所顯示的 A 畫面與 B 畫面是分開來的，但是在平板 (較大的螢幕) 上可使 A 畫面與 B 畫面同時顯示。Fragment 的重要性在於更彈性的設計使用介面，而 Fragment 的使用需由嵌入 Activity。

畫圖！！！！

<h2 id="2"></h2>

# 二、Fragment 的基本使用方法
hosting a UI fragment in an activity(THNRG p137)

<h2 id="2.1"></h2>

## 2.1 在 layout 加入 fragment
1. add the fragment to thw activity's layout (GreenBean 第一冊裡有)
使用上簡單 但缺乏彈性

<h2 id="2.2"></h2>

## 2.2 利用 fragment 建立動態 UI 
在 activity 程式碼中加入 fragment
2. add the fragment in the activity's code
使用上複雜 但十分靈活

<h2 id="3"></h2>

# 三、Fragment 的生命週期

<h2 id="4"></h2>

# 四、Fragment 的進階使用

<h2 id="4.1"></h2>

## 4.1 程式架構

<h2 id="4.2"></h2>

## 4.2 程式碼以及說明

<h2 id="5"></h2>

# 參考資料

### [Android 官方網站](https://developer.android.com/training/basics/fragments/index.html)
