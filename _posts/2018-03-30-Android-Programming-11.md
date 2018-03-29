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
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <TextView
        android:id="@+id/textView2"
        style="?android:attr/listSeparatorTextViewStyle"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginEnd="16dp"
        android:layout_marginStart="16dp"
        android:layout_marginTop="24dp"
        android:text="@string/crime_title_label"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"/>

    <EditText
        android:id="@+id/crime_title"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginTop="8dp"
        android:ems="10"
        android:hint="@string/crime_title_hint"
        android:inputType="textPersonName"
        app:layout_constraintEnd_toEndOf="@+id/textView2"
        app:layout_constraintStart_toStartOf="@+id/textView2"
        app:layout_constraintTop_toBottomOf="@+id/textView2"/>

    <TextView
        android:id="@+id/textView3"
        style="?android:attr/listSeparatorTextViewStyle"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginTop="8dp"
        android:text="@string/crime_details_label"
        app:layout_constraintEnd_toEndOf="@+id/textView2"
        app:layout_constraintStart_toStartOf="@+id/crime_title"
        app:layout_constraintTop_toBottomOf="@+id/crime_title"/>

    <Button
        android:id="@+id/crime_date"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginTop="8dp"
        app:layout_constraintEnd_toEndOf="@+id/textView3"
        app:layout_constraintStart_toStartOf="@+id/textView3"
        app:layout_constraintTop_toBottomOf="@+id/textView3"/>

    <CheckBox
        android:id="@+id/crime_solved"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginTop="8dp"
        android:text="@string/crime_solved_label"
        app:layout_constraintEnd_toEndOf="@+id/crime_date"
        app:layout_constraintStart_toStartOf="@+id/crime_date"
        app:layout_constraintTop_toBottomOf="@+id/crime_date"/>
</android.support.constraint.ConstraintLayout>
```


<h2 id="2.2"></h2>

## 2.2 挑戰二：Adding First and Last Buttons

### 題目：

請加上兩個按鈕，使得 ViewPager 可以直接滑動到第一個或是最後一個 Crime。當 ViewPager 在第一個 Crime 時，移動到第一個的按鈕應設為 disable；當 ViewPager 在最後一個 Crime 時，移動到最後一個的按鈕應設為 disable。

### 解題想法：

這題的目的是希望讀者能更熟悉 ViewPager 裡的 OnPageChangeListener 介面以及 Button 的點擊事件 (其實這也是一個介面)。以下程式碼為解題的答案。

CrimePagerActivity.java

```java
package com.little.criminalintent;

import android.content.Context;
import android.content.Intent;
import android.os.Bundle;
import android.support.annotation.Nullable;
import android.support.v4.app.Fragment;
import android.support.v4.app.FragmentManager;
import android.support.v4.app.FragmentStatePagerAdapter;
import android.support.v4.view.ViewPager;
import android.support.v7.app.AppCompatActivity;
import android.view.View;
import android.widget.Button;

import java.util.List;
import java.util.UUID;

/**
 * Created by sarah on 28/03/2018.
 */

public class CrimePagerActivity extends AppCompatActivity implements ViewPager.OnPageChangeListener{

    private static final String EXTRA_CRIME_ID = "com.little.criminalintent.crime_id";

    private ViewPager mViewPager;
    private List<Crime> mCrimes;
    private Button mFirstButton;
    private Button mLastButton;

    public CrimePagerActivity() {
    }

    public static Intent newIntent(Context packageContext, UUID crimeId) {
        Intent intent = new Intent(packageContext, CrimePagerActivity.class);
        intent.putExtra(EXTRA_CRIME_ID, crimeId);
        return intent;
    }

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_crime_pager);

        UUID crimeId = (UUID) getIntent().getSerializableExtra(EXTRA_CRIME_ID);

        mViewPager = findViewById(R.id.crime_view_pager);
        mFirstButton = findViewById(R.id.first);
        mLastButton = findViewById(R.id.last);

        mCrimes = CrimeLab.get(this).getCrimes();
        FragmentManager fragmentManager = getSupportFragmentManager();
        mViewPager.addOnPageChangeListener(this);
        mViewPager.setAdapter(new FragmentStatePagerAdapter(fragmentManager) {
            @Override
            public Fragment getItem(int position) {
                Crime crime = mCrimes.get(position);
                return CrimeFragment.newInstance(crime.getId());
            }

            @Override
            public int getCount() {
                return mCrimes.size();
            }
        });

        int position = 0;
        for (int i = 0; i < mCrimes.size(); i++) {
            if (mCrimes.get(i).getId().equals(crimeId)) {
                position = i;
                break;
            }
        }
        setCurrentItem(position);
    }

    public void setCurrentItem(int position) {
        int size = mCrimes.size();

        mViewPager.setCurrentItem(position);
        setButtonsEnable(position>0,position<size-1);
    }

    public void setButtonsEnable(boolean enableFirst, boolean enableLast) {
        mFirstButton.setEnabled(enableFirst);
        mLastButton.setEnabled(enableLast);
    }

    public void goToFirst(View view) {
        int position = 0;
        setCurrentItem(position);
    }

    public void goToLast(View view) {
        int position = mCrimes.size() - 1;
        setCurrentItem(position);
    }

    @Override
    public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {

    }

    @Override
    public void onPageSelected(int position) {
        setButtonsEnable(position>0,position<mCrimes.size()-1);
    }

    @Override
    public void onPageScrollStateChanged(int state) {

    }
}
```

# 參考資料

[Big Nerd Ranch](https://forums.bignerdranch.com/c/android-programming-the-big-nerd-ranch-guide)