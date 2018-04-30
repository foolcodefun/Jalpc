---
layout: post
title:  "書籍：Android Programming CH12 挑戰 (DatePicker & DialogFragment)"
date:   2018-04-29
desc: "Preference"
keywords: "Android"
categories: [Android]
tags: [Android,The Big Nerd Ranch Guide]
icon: icon-html
---

* ### [一、前言簡介](#1)
* ### [二、Chapter 12 Challenge](#2)
> ### [2.1 挑戰一：More Dialogs](#2.1)
> ### [2.2 挑戰二：A Responsive DialogFragment](#2.2)
* ### [參考資料](#3)

<h2 id="1"></h2>

# 一、前言簡介
這章的內容在介紹了 DatePicker 和 DialogFragment 的結合應用。在此篇文章中提到，將 DatePicker 結合 DialogFragment 對於程式開發有更高的彈性，因為利用 DialogFragment 可以在開發時能更容易達到手機跟平板上不同 UI 呈現。像是在手機上 DatePicker 的呈現最好以 Activity 的方式，而在平板上以 Dialog 的方式呈現 DatePicker 會更好。

此章有兩個挑戰要做。第一個是 More Dialogs，此挑戰要求新增一個名為 TimePickerFragment 的 DialogFragment。第二個挑戰是 A Responsive DialogFragment，此挑戰要求在手機與平板上，DatePicker 呈現不同的樣式。

<h2 id="2"></h2>

# 二、Chapter 12 Challenge

<h2 id="2.1"></h2>

## 2.1 挑戰一：More Dialogs

### 題目

題目中要求讀者新增一個繼承 DialogFragment 的 TimePickerFragment 類別，這個類別可讓使用者透過 TimePicker 更改 Crime 的時間。請讀者新增一個 Button，此 Button 被點擊時會顯示 TimePicker。

### 解題想法

<h2 id="2.2"></h2>

## 2.2 挑戰二：A Responsive DialogFragment

### 題目：

此題要求讀者在手機上以 Activity 的方式呈現 DatePicker，在平板上以 Dialog 的方式呈現 DatePicker。整個過程分成三步驟：第一步驟，以 `onCreateView` 取代 `onCreateDialog` 方法，如此一來 DatePicker 可以 Dialog 呈現，或是可以將它整合到 Activity。第二步驟，新增一個 SingleFragmentActivity 的子類別，此類別會運用 DatePickerFragment 呈現 DatePicker，在這個做法中得利用 `startActivityForResult` 方法傳送資料，並利用 `sendResult` 將資料傳回。最後一個步驟是將 DatePicker 以 Activity 呈現於手機上，而 DatePicker 在平板上則以 Dialog 呈現。

### 解題想法：

# 參考資料

[Big Nerd Ranch](https://forums.bignerdranch.com/c/android-programming-the-big-nerd-ranch-guide)
