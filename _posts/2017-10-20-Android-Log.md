---
layout: post
title:  "Android Log"
date: 2017-10-17
desc: "Layout 基本介紹"
keywords: "Android, Log"
categories: [Android]
tags: [Android,Log]
icon: icon-html
---

# 前言簡介

在 android 開發中除錯是非常重樣的一環，android.util.Log 類別可有效追蹤程式運行的狀態並有效分析錯誤，是除錯的好幫手。本篇文章將介紹 Log 類別的使用方法、使用時機及查看日誌訊息的方法。

# log 使用方法

日誌記錄的方法是 `Log.v()`、`Log.d()`、`Log.i()`、`Log.w()` 或 `Log.e()`，這五種方法分別代表VERBOSE 、DEBUG、INFO、WARN 和 ERROR 五種重要程度由低到高排列的訊息。在官方文件中強調 VERBOSE 日誌不應編譯進應用程式中但可在開發時使用，DEBUG 日誌可被編譯近應用程式中但在軟題運行時應刪除，其他種類的日誌則可永遠保留。

```java
private static final String TAG = "MyActivity";
Log.v(TAG, "Message");
```

## 記錄日誌方法的使用時機

> Log.e: 通常在一個錯誤被捕捉到的時候使用。 
>
> Log.w: 用以記錄一些使用上有些怪異的事件上。
>
> Log.i: 使用時機通常是在某個事件成功執行時，像是連線成功的訊息。
>
> Log.d: 在除錯時使用，通常用來查看某個物件的值。
>
> Log.v: 因為一些因素得把所有細節訊息都記錄在日誌上時。


# logcat 使用方法

logcat 是用以查看日誌輸出訊息的地方，下圖為 logcat。

<img src="{{ site.img_path }}/20171020/logcat.png" width="40%"/>
