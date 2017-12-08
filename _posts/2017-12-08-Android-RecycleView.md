---
layout: post
title:  "Android RecyclerView"
date: 2017-12-08
desc: "Android RecyclerView"
keywords: "Android, RecyclerView"
categories: [Android]
tags: [Android,RecyclerView]
icon: icon-html
---

* ### [一、前言簡介](#1)
* ### [二、RecyclerView 的使用架構與基本使用方法](#2)
> ### [2.1 LayoutManager](#2.1)
> ### [2.1 RecyclerView.ViewHolder and RecyclerView.Adapter](#2.2)
* ### [三、簡易範例](#3)
> ### [3.1 程式架構](#3.1)
> ### [3.2 程式碼以及說明](#3.2)
* ### [參考資料](#4)

<h2 id="1"></h2>

# 一、前言簡介

Android 5.0 推出的 RecyclerView 功能和 ListView 非常像，在[官方網站中](https://developer.android.com/training/material/lists-cards.html)提到 RecyclerView 是功能更進階且實用上更彈性的 ListView。RecyclerView 和 ListView 在使用上都要搭配 Adapter 作為資料與畫面的橋樑，不同的是 RecyclerView 的使用必須搭配 ViewHolder 來管理畫面 (ListView 也可設計 ViewHolder 來管以畫面，但沒有強制規定)，如此一來會有更好的畫面資源管理，使用者操作程式介面時也會比使用 ListView 時流暢。另外，RecyclerView 還提供了常見項目操作的預設動畫，像是新增或刪除的動畫。本篇文章會先簡單介紹 RecyclerView 的使用方法再帶入一個範例進行說明。

<img src="{{ site.img_path }}/20171208/pic.png" width="100%" style="max-width:300px;"/>


<h2 id="2"></h2>

# 二、RecyclerView 的使用架構與基本使用方法

使用 RecyclerView 前要先了解 RecyclerView 的基本架構，如下圖。LayoutManager 用來管理版面配置，Adapter 是資料與 RecyclerView 之間的橋樑。此外，在使用 RecyclerView.Adapter 時必須實作 RecyclerView.ViewHolder。

<img src="{{ site.img_path }}/20171208/Structure.png" width="100%" style="max-width:600px;"/>

下面的程式碼是 [官方網站](https://developer.android.com/training/material/lists-cards.html)提供的 RecyclerView 的基本使用方法，
首先要先到版面配置一個 RecyclerView 元件如下。

```xml
<android.support.v7.widget.RecyclerView
    android:id="@+id/my_recycler_view"
    android:scrollbars="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"/>
```

接著在下面程式碼中可以發現 LayoutManager 和 Adapter 是必要的。那 ViewHolder 呢？ViewHolder 的使用會在 RecyclerView.Adapter 的客製化中用到，2.2 會談到 ViewHolder 與 Adapter 。

```java
public class MyActivity extends Activity {
    private RecyclerView mRecyclerView;
    private RecyclerView.Adapter mAdapter;
    private RecyclerView.LayoutManager mLayoutManager;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.my_activity);
        mRecyclerView = (RecyclerView) findViewById(R.id.my_recycler_view);

        // use this setting to improve performance if you know that changes
        // in content do not change the layout size of the RecyclerView
        mRecyclerView.setHasFixedSize(true);

        // use a linear layout manager
        mLayoutManager = new LinearLayoutManager(this);
        mRecyclerView.setLayoutManager(mLayoutManager);

        // specify an adapter (see also next example)
        mAdapter = new MyAdapter(myDataset);
        mRecyclerView.setAdapter(mAdapter);
    }
    ...
}

```

<h2 id="2.1"></h2>

## 2.1 LayoutManager

官方提供了三種總類的 LayoutManager，分別是 LinearLayoutManager、GridLayoutManager 和 StaggeredGridLayoutManager。

> LinearLayoutManager 此類別會將項目 (item) 顯示在垂直或水平的清單中。
>
> GridLayoutManager 此類別會將項目 (item) 顯示於網格中。
>
> StaggeredGridLayoutManager 此類別會將項目 (item) 顯示於交錯網格中。

<h2 id="2.2"></h2>

## 2.2 RecyclerView.ViewHolder and RecyclerView.Adapter

這個小節將會解釋[官方範例](https://developer.android.com/training/material/lists-cards.html?hl=zh-tw)。在以下程式碼會看到要繼承 RecyclerView.Adapter 必須在泛型內放入一個繼承 RecyclerView.ViewHolder 的物件，此範例將 ViewHolder 宣告為 MyAdapter 的內部類別，而 ViewHolder 通常用以設定畫面元件。另外，繼承 RecyclerView.Adapter 還需 Override 三個方法，分別是 `onCreateViewHolder`、`onBindViewHolder` 和 `getItemCount` 方法。

> onCreateViewHolder：初始化畫面元件。
>
> onBindViewHolder：設定畫面元件內容。
>
> getItemCount：取得資料 item 總數。

```java
public class MyAdapter extends RecyclerView.Adapter<MyAdapter.ViewHolder> {
    private String[] mDataset;

    // Provide a reference to the views for each data item
    // Complex data items may need more than one view per item, and
    // you provide access to all the views for a data item in a view holder
    public static class ViewHolder extends RecyclerView.ViewHolder {
        // each data item is just a string in this case
        public TextView mTextView;
        public ViewHolder(TextView v) {
            super(v);
            mTextView = v;
        }
    }

    // Provide a suitable constructor (depends on the kind of dataset)
    public MyAdapter(String[] myDataset) {
        mDataset = myDataset;
    }

    // Create new views (invoked by the layout manager)
    @Override
    public MyAdapter.ViewHolder onCreateViewHolder(ViewGroup parent,
                                                   int viewType) {
        // create a new view
        View v = LayoutInflater.from(parent.getContext())
                               .inflate(R.layout.my_text_view, parent, false);
        // set the view's size, margins, paddings and layout parameters
        ...
        ViewHolder vh = new ViewHolder(v);
        return vh;
    }

    // Replace the contents of a view (invoked by the layout manager)
    @Override
    public void onBindViewHolder(ViewHolder holder, int position) {
        // - get element from your dataset at this position
        // - replace the contents of the view with that element
        holder.mTextView.setText(mDataset[position]);

    }

    // Return the size of your dataset (invoked by the layout manager)
    @Override
    public int getItemCount() {
        return mDataset.length;
    }
}

```

<h2 id="3"></h2>

# 三、簡易範例

本章節提供一完整範例供讀者參考。

## 3.1 程式碼以及說明

Article 類別用來儲存文章資料，之後 Adapter 會將資料顯示於 RecyclerView 上。

Article.java

```java
package com.little.testrecyclerveiw;

public class Article {
    private String title;
    private String subtitle;

    public Article() {
        title = "This is title.";
        subtitle = "This is subtitle.";
    }

    public Article(String title, String subtitle) {
        this.title = title;
        this.subtitle = subtitle;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public String getSubtitle() {
        return subtitle;
    }

    public void setSubtitle(String subtitle) {
        this.subtitle = subtitle;
    }
}

```

這個是清單的每一欄的版面配置，在 ArticleAdapter 中會利用 LayoutInflater 轉成 View。

item.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="wrap_content">

    <TextView
        android:id="@+id/title"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginStart="8dp"
        android:layout_marginTop="16dp"
        android:text="Title"
        android:textAppearance="@style/TextAppearance.AppCompat.Large"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"/>

    <TextView
        android:id="@+id/subtitle"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="8dp"
        android:text="Subtitle"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintStart_toStartOf="@+id/title"
        app:layout_constraintTop_toBottomOf="@+id/title"/>
</android.support.constraint.ConstraintLayout>
```

ArticleAdapter 繼承 RecyclerView.Adapter，是本篇文章的主角，使用說明在[第二章](#2)。

ArticleAdapter.java

```java
package com.little.testrecyclerveiw;

import android.support.v7.widget.RecyclerView;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.TextView;

import java.util.List;

/**
 * Created by sarah on 08/12/2017.
 */

public class ArticleAdapter extends RecyclerView.Adapter<ArticleAdapter.ViewHolder> {

    private List<Article> articles;

    public ArticleAdapter(List<Article> articles) {
        this.articles = articles;
    }

    @Override
    public ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View itemView = LayoutInflater.from(parent.getContext())
                .inflate(R.layout.item,parent,false);
        ViewHolder viewHolder = new ViewHolder(itemView);
        return viewHolder;
    }

    @Override
    public void onBindViewHolder(ViewHolder holder, int position) {
        Article article = articles.get(position);
        holder.bind(article);
    }

    @Override
    public int getItemCount() {
        return articles.size();
    }

    public static class ViewHolder extends RecyclerView.ViewHolder{

        private final TextView subtitle;
        private final TextView title;

        public ViewHolder(View itemView) {
            super(itemView);
            title = (TextView)itemView.findViewById(R.id.title);
            subtitle = (TextView)itemView.findViewById(R.id.subtitle);
        }

        public void bind(Article article){
            title.setText(article.getTitle());
            subtitle.setText(article.getSubtitle());
        }
    }
}

```

這個主畫面配置。

activity_main.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.little.testrecyclerveiw.MainActivity">

    <android.support.v7.widget.RecyclerView
        android:id="@+id/recycler"
        android:layout_width="0dp"
        android:layout_height="0dp"
        android:layout_marginBottom="8dp"
        android:layout_marginEnd="8dp"
        android:layout_marginStart="8dp"
        android:layout_marginTop="8dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"/>
</android.support.constraint.ConstraintLayout>

```

程式一開始先放入一百個 Article 近 List 中，之後再交給 ArticleAdapter 將資料顯示到 RecyclerView 中。

MainActivity.java

```java
package com.little.testrecyclerveiw;

import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.support.v7.widget.LinearLayoutManager;
import android.support.v7.widget.RecyclerView;

import java.util.ArrayList;
import java.util.List;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        List<Article> articles = new ArrayList<>();

        for (int i = 0;i<100;i++){
            Article article =new Article();
            article.setTitle("This is title #"+ i);
            article.setSubtitle("This is subtitle #"+ i);
            articles.add(article);
        }

        RecyclerView recyclerView = (RecyclerView)findViewById(R.id.recycler);
        recyclerView.setLayoutManager(new LinearLayoutManager(this));

        ArticleAdapter adapter = new ArticleAdapter(articles);
        recyclerView.setAdapter(adapter);
    }
}

```

# 3.2 執行結果

<img src="{{ site.img_path }}/20171208/ExecutiveResult.png" width="100%" style="max-width:300px;"/>

# 參考資料
### 1. [Android 官網](https://developer.android.com/training/material/lists-cards.html)
### 2. [Android Programing The Big Nerd Ranch Guide Chapter 8](https://www.bignerdranch.com/books/android-programming/)

