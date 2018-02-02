---
layout: post
title:  "Android 初識 Service 與 IntentService"
date:   2018-01-11
desc: "Broadcast"
keywords: "Android,Broadcast"
categories: [Android]
tags: [Android,Broadcast]
icon: icon-html
---

* ### [一、前言簡介](#1)
* ### [二、Service](#2)
> ### [2.1 Service 的基本概念 ](#2.1)
> * ### [(1) Service 的重要方法](#2.1.1)
> * ### [(2) Service 的生命週期](#2.1.2)
>
> ### [2.2 開始與停止 Service ](#2.2)
* ### [三、IntentService](#3)
> ### [3.1 IntentService 的基本概念 ](#3.1)
* ### [四、Service 與 IntentService 的範例與比較](#2)
* ### [參考資料](#4)

<h2 id="1"></h2>

# 一、前言簡介

本篇文章介紹 Service 和 IntentService 的基本概念以及基本的使用方法。Service 是 Android 的四大元件之一，主要功能是處理一些不需要使用者介面的功能，也就是說 Service 在背景運作。Service 雖然在背景運作，但卻是在 main thread 運作，想要執行一些耗時工作就得建立一個新的 thread，否則會導致 ANR 錯誤。IntentService 是 Service 的子類別並且被廣泛的使用，此類別被換醒時便會建立新的 thread，因此在處理耗時工作時不需要設計新的 thread。Service 可被 Service、Broadcast 和 Activity 喚醒，其生命週期與 Activity 是截然不同的，以下是詳細的說明。

<h2 id="2"></h2>

# 二、Service

<h2 id="2.1"></h2>

## 2.1 Service 的基本概念 

<h2 id="2.1.1"></h2>

### (1) Service 的重要方法

<h2 id="2.1.2"></h2>

### (2) Service 的生命週期

<h2 id="2.2"></h2>

## 2.2 開始與停止 Service

<h2 id="3"></h2>

# 三、IntentService

<h2 id="3.1"></h2>

## 3.1 IntentService 的基本概念

# 四、Service 與 IntentService 的範例與比較

<h2 id="4"></h2>

# 參考資料 

[Android 官網 IntentService](https://developer.android.com/reference/android/app/IntentService.html)
[Android 官網 Service](https://developer.android.com/guide/components/services.html?hl=zh-tw)
[簡書平台：Android Service和IntentService知识点详细总结](https://www.jianshu.com/p/476d3ed50db1)
[Vogella：Android Services - Tutorial](http://www.vogella.com/tutorials/AndroidServices/article.html)
[SwA：Android Service Tutorial](https://www.survivingwithandroid.com/2014/01/android-service-tutorial-2.html)

