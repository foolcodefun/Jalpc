---
layout: post
title:  "書籍：Android Programming CH11 挑戰 (ConstrainLayout & ViewPager)"
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

另外，ConstrainLayout 是放在 Support Library 裡， 要使用 ConstrainLayout 請在 gradle 加上以下程式碼。

```
dependencies {
    implementation 'com.android.support.constraint:constraint-layout:1.0.2'
}
```

以往在排版時須用多種 Layout 組合完成，像是 LinearLayout 與 RelativeLayout 的組合，這兩個 Layout 的組合常常會造成巢狀結構，因此會有很大效的能損耗。而 ConstrainLayout 使 xml 扁平化，可以達到效能提升的效果。ConstrainLayout 的使用和 RelativeLayout 有點像，但功能更為強大，比如說他有 GuideLine 功能且能以比例調整位置，在排版上用 Layout Editor 會比手打程式碼來得快。基於上述原因，在此推坑各位讀者大大。不過在使用 ConstrainLayout 時請注意他在常跟寬的設定並沒有 match_parent，但是多了個 match_constraint，match_constraint 可以用 0dp 表示。 

<h2 id="2.2"></h2>

## 2.2 挑戰二：Adding First and Last Buttons

### 題目：

請加上兩個按鈕，使得 ViewPager 可以直接滑動到第一個或是最後一個 Crime。當 ViewPager 在第一個 Crime 時，移動到第一個的按鈕應設為 disable；當 ViewPager 在最後一個 Crime 時，移動到最後一個的按鈕應設為 disable。

### 解題想法：

這題的目的是希望讀者能更熟悉 ViewPager 裡的 OnPageChangeListener 介面以及 Button 的點擊事件。以下程式碼為解題的答案。

CrimePagerActivity.java

```java
......
public class CrimePagerActivity extends AppCompatActivity{

    private static final String EXTRA_CRIME_ID = "com.little.criminalintent.crime_id";

    ......
    private Button mFirstButton;
    private Button mLastButton;

    ......
    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
    
        ......
        mFirstButton = findViewById(R.id.first);
        mLastButton = findViewById(R.id.last);
        
        ......
        mCrimes = CrimeLab.get(this).getCrimes();
        FragmentManager fragmentManager = getSupportFragmentManager();
        mViewPager.addOnPageChangeListener(new ViewPager.OnPageChangeListener() {
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
        });
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
}

```
上述程式碼的 `goToFirst` 和 `goToLast` 分別是兩個按鈕的點擊事件，分別會讓畫面移到第一個 Crime 和最後一個 Crime。`setButtonsEnable` 用來設定兩個按鈕是否可以被點擊，`setCurrentItem` 會讓畫面移到要的 Crime 並設定好畫面中的按鈕是否可以被點擊。

# 參考資料

[Big Nerd Ranch](https://forums.bignerdranch.com/c/android-programming-the-big-nerd-ranch-guide)

[Android Studio Release Updates](https://androidstudio.googleblog.com/)

[Android sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
