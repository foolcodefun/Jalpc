---
layout: post
title:  "Android Programming CH7 & CH8 文章解析"
date:   2018-03-09
desc: "Preference"
keywords: "Android"
categories: [Android]
tags: [Android,The Big Nerd Ranch Guide]
icon: icon-html
---

* ### [一、前言簡介](#1)
* ### [二、Chapter 8 Challenge](#2)
* ### [參考資料](#3)

<h2 id="1"></h2>

# 一、前言簡介

在學習 android 的路上遇見了一本書叫做 Android Programming，其出版社為 The Big Nerd Ranch Guide。這本書在大部份章節的最後都會加上 Challenge 讓讀者挑戰，於是筆者決定每完成一個挑戰就將程式碼放在部落格中。

# 二、Challenge 8 Challenge

第八章的內容主要是在講 Fragment 的使用與整合、Singletan 模式的介紹以及 RecyclerView 的基本使用。此章節的挑戰是 RecyclerView 的延伸應用，希望在一個 RecyclerView 在不一樣程度的 Crime 以不一樣的 row_item 展示，挑戰中還提到了要 implement CrimeAdapter 類別中的  `getItenViewType(int)` 方法，並在 `onCreateViewHolder(ViewGroup, int)` 方法中回傳不一樣的 ViewHolder。