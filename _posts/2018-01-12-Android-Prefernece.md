---
layout: post
title:  "Android 偏好設定 (Preference)"
date:   2018-01-10
desc: "Preference"
keywords: "Android,Preference"
categories: [Android]
tags: [Android,Preference]
icon: icon-html
---

* ### [一、前言簡介](#1)
* ### [二、PreferenceFragment](#2)
> ### [2.1 設定畫面：XML 與 PreferenceScreen](#2.1)
> * ### [(1) 各式清單](#2.1.1)
> * ### [(2) 設定群組](#2.1.2)
>
> ### [2.2 取得設定改變後的值：OnSharedPreferenceChangeListener](#2.2)
* ### [三、簡易範例](#3)
* ### [參考資料](#4)

<h2 id="1"></h2>

# 一、前言簡介

使用者操作一個 APP 時可能會需要設定一些功能，像是是否要開啟通知功能或與雲端同步的頻率等等。這些設定 (Settings) 功能是透過 [Android Preference API](https://developer.android.com/reference/android/preference/Preference.html?hl=zh-tw) 實現的，本篇文章將會介紹 Preference 的基本使用方法，下圖是設定頁面的樣子。

<img src="{{ site.img_path }}/20180110/setting.png" width="100%" style="max-width:300px;"/>

設定須通過 Activity 或 Fragment 顯示。在 Android 3.0 之前 (API10 以期以前) 應該用 PreferenceActivity；Android 3.0 之後可以用 Activity 與 PreferenceFragment 的搭配來完成。Android 3.0 之後若是有多個設定群組，需要用大畫面顯示兩個版面則可用 PreferenceActivity 與 PreferenceFragment 來完成。

<h2 id="2"></h2>

# 二、PreferenceFragment

<h2 id="2.1"></h2>

## 2.1 設定畫面：XML 與 PreferenceScreen

<h2 id="2.1.1"></h2>

### (1) 各式清單

<h2 id="2.1.2"></h2>

### (2) 設定群組

<h2 id="2.2"></h2>

### 2.2 取得設定改變後的值：OnSharedPreferenceChangeListener

<h2 id="3"></h2>

# 三、簡易範例

Android 3.0 之後若是有多個設定群組，需要用大畫面顯示兩個版面則可用 PreferenceActivity 與 PreferenceFragment 來完成

上述當作範例

# 參考資料

[官方網站：設定](https://developer.android.com/guide/topics/ui/settings.html?hl=zh-tw)