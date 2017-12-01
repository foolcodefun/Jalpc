---
layout: post
title:  "Android Activities 間傳送資料"
date: 2017-12-01
desc: "Android Passing Data Between Activities"
keywords: "Android, onActivityResult"
categories: [Android]
tags: [Android,onActivityResult]
icon: icon-html
---

# 目錄

* ### [一、前言簡介](#1)
* ### [二、Intent 與 Bundle 傳遞資料](#2)
> ### [1. 透過 Intent 傳遞資料給第二個 Activity](#2.1)
> ### [2. 透過 Bundle 傳遞資料給第二個 Activity法](#2.2)
* ### [三、在兩個 Activities 間互相傳送資料](#3)
> ### [1. 傳遞架構說明](#3.1)
> * ### [(1) startActivityForResult](#3.1.1)
> * ### [(2) setResult](#3.1.2)
> * ### [(1) onActivityResult](#3.1.1)
>
> ### [2. 範例程式碼](#3.2)
* ### [四、題外話：關於 Activity 的生命週期](#4)
* ### [參考資料](#5)

<h2 id="1"></h2>

# 一、前言簡介

App 中通常有多個 Activities，並且在 Activities 間常會有互相傳送資料的需要。本篇文章會先講解如何利用 Intent 或 Bundle 傳送資料，再進一步解說 Activities 間互相(雙向)傳送資料的方法。在開始講解前要請讀者按照以下的步驟準備好兩個 Activities。

### 步驟一、請讀者先開啟一個新專案並新增一個 Activity。

先新增一個 Activity。方法為 new -> Activity -> Empty Activity 如圖。

<img src="{{ site.img_path }}/20171201/newActivity.png" width="100%" style="max-width:850px;"/>

接著命名新的 Activity 為 CheatActivity，如圖。

<img src="{{ site.img_path }}/20171201/createsCheatActivity.png" width="100%" style="max-width:700px;"/>

此 project 會在 App 主畫面顯示一個題目，讓讀者選取答案，若使用者不知答案則可立用 cheat 按鈕的功能查看答案。以下將會分成 activity_main 和 activity_cheat 展示兩個 Layout 的程式碼和使用介面的樣子。


### 步驟二、activity_main.xml

activity_main.xml 程式碼

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center"
    tools:context="com.little.testpassdatabetweenactivities.MainActivity">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_margin="8dp"
        android:textAppearance="@style/TextAppearance.AppCompat.Large"
        android:text="1+1=3"/>

    <LinearLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="horizontal">

        <Button
            android:id="@+id/btn_true"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_margin="8dp"
            android:text="TRUE"/>

        <Button
            android:id="@+id/btn_false"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_margin="8dp"
            android:text="FALSE"/>
    </LinearLayout>

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_margin="8dp"
        android:onClick="cheat"
        android:text="CHEAT!"/>

</LinearLayout>
```

使用介面如下圖

<img src="{{ site.img_path }}/20171201/activity_main.png" width="100%" style="max-width:350px;"/>

### 步驟三、activity_cheat.xml

activity_cheat.xml 程式碼

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical"
    tools:context="com.little.testpassdatabetweenactivities.CheatActivity">

    <TextView
        android:id="@+id/answer"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_margin="8dp"
        android:text="Are you sure you want to cheat?"/>

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_margin="8dp"
        android:onClick="showAnswer"
        android:text="SURE"/>
</LinearLayout>
```

使用介面如下圖

<img src="{{ site.img_path }}/20171201/activity_cheat.png" width="100%" style="max-width:350px;"/>

嗯哼，相信各位讀者看到這個 App 介面時都已經知道 1+1=3 是 TRUE 還是 FALSE 了，但是在下懇求各位大大裝作不知道然後按下 CHEAT 按鈕，接著會發現什麼事都沒發生。對！這就是本篇文章要撰寫 MainActivity 傳送答案資料給 CheatActivit 的地方。我們希望在按下 activity_main 的 CHEAT! 按鈕後，畫面會從 activity_main.xml 換到 activity_cheat.xml 且答案會從 MainActivity.java 傳送到 CheatActivit.java 。另外在 activity_cheat 畫面時，當使用者按下 SURE 按鈕後，答案會顯示在界面上並回傳使用者作弊的情報給 MainActivity。

<h2 id="2"></h2>

# 二、Intent 與 Bundle 傳遞資料

請在 MainActivity.java 的 `cheat(View view)` 加上以下程式碼。以下程式碼會讓使用者在按下 CHEAT! 按鈕後從 activity_main 換頁到 activity_cheat。

```java
Intent intent = new Intent (this,SecondActivity.class);
startActivity(intent);
```

現在按下 CHEAT! 按鈕會從 activity_main 換頁 到 activity_cheat 的效果，若要傳送資料給 CheatActivity 則可將資料放進 Intent 或 Bundle 中。以下將透過兩個小節分別說明如何透過 Intent 和 Bundle 傳遞資料。

<h2 id="2.1"></h2>

## 1. 透過 Intent 傳遞資料給 CheatActivity

除了換頁功能，我們也希望順便把答案傳給 CheatActivity，我們可以利用 Intent 類別的 putExtra 來達到此目的。

請在 MainActivity.java 的 `cheat(View view)` 改成以下程式碼，程式碼中的 ANSWER 是一個全域變數，宣告為 `public static final boolean ANSWER = false;`。

```java
Intent intent = new Intent(this,CheatActivity.class);
intent.putExtra("ANSWER_IS_TRUE",ANSWER);
startActivity(intent);
```

接著在 CheatActivity.java 的 showAnswer 方法中用 `getIntent.getBooleanExtra` 取出資料。程式碼中的 `answerIsTrue` 是一個全域變數，宣告為 `private boolean answerIsTrue;`。

```java
answerIsTrue = getIntent().getBooleanExtra("ANSWER_IS_TRUE",false);
```

<h2 id="2.2"></h2>

## 2. 透過 Bundle 傳遞資料給第二個 Activity法

除了 Intent 也可利用 Bundle 在 Activities 傳送資料。Bundle 在傳送資料上有更大的彈性，此範例無法顯現以 Bundle 傳送資料的優勢，有興趣的讀者可研究研究。

請在 MainActivity.java 的 `cheat(View view)` 改成以下程式碼 

```java
Intent intent = new Intent(this,CheatActivity.class);
Bundle bag = new Bundle();
bag.putBoolean("ANSWER_IS_TRUE",ANSWER);
intent.putExtras(bag);
startActivity(intent);
```

CheatActivity.java 取出資料的方法改成下面的程式碼。

```java
Bundle bag = getIntent().getExtras();
answerIsTrue = bag.getBoolean("ANSWER_IS_TRUE");
```

<h2 id="3"></h2>

# 三、在兩個 Activities 間互相傳送資料

但是 CheatActivity 也要傳資料給 MainActivity，這樣 MainActivity 才知道使用者是否有作弊行為。
再換頁時若需要將 SecondActivity 的資料返還 MainActivity 則需要用 `startActivityForResult` 方法取代 `startActivity`。以下程式碼用 Intent 傳送資料作為說明。

<h2 id="3.1"></h2>

## 1. 傳遞架構說明

兩個 Activities 互相傳送資料的步驟分成三個，第一個是在 MainActivity.java 的 `startActivityForResult` 方法，第二個是在 CheatActivity.java 的 `setResult` 方法，而最後則是在當 App 回到 MainActivity.java 時 會自動呼叫 `onActivityResult` 方法，因此在此方法中撰寫取出資料的程式碼即可。

<h2 id="3.1.1"></h2>

### (1) startActivityForResult

在 MainActivity.java 中若是需要接收回傳的資料就不可以用 startActivity 要用 startActivityForResult。

將 MainActivity.java 中 `cheat(View)` 方法內的程式碼改成以下。

```java
Intent intent = new Intent(this,CheatActivity.class);
intent.putExtra("ANSWER_IS_TRUE",ANSWER);
startActivityForResult(intent,REQUEST_CODE_CHEAT);
```

<h2 id="3.1.2"></h2>

### (2) setResult

這個步驟則是 CheatActivity.java 回傳資料，這裡的想法是如果使用者按下 SURE 按鈕，代表使用者 cheat，所以要告訴 MainActivity 使用者已經 cheat 了。`showAnswer(View)` 方法內的程式碼如下。

```java
Intent intent= getIntent();
answerIsTrue = intent.getBooleanExtra("ANSWER_IS_TRUE",false);
intent.putExtra("ANSWER_IS_SHOWN",true);
setResult(RESULT_OK,intent);
```

<h2 id="3.1.3"></h2>

### (3) onActivityResult

`onActivityResult` 方法會在使用者從 CheatActivity.java 回到 MainActivity.java 時調用，請在 MainActivity.java 覆寫 `onActivityResult` 方法，程式碼如下。

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if(resultCode!=RESULT_OK)
        return;
    if (requestCode == REQUEST_CODE_CHEAT) {
        if (data == null)
            return;
        mIsCheater = data.getBooleanExtra("ANSWER_IS_SHOWN",false);
    }
}
```

<h2 id="3.2"></h2>

## 2. 完整範例程式碼

MainActivity.java

```java
package com.little.testpassdatabetweenactivities;

import android.content.Intent;
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

public class MainActivity extends AppCompatActivity {

    public static final boolean ANSWER_IS_TRUE = false;
    private static final int REQUEST_CODE_CHEAT = 0;

    private boolean mIsCheater;

    private Button btwTrue;
    private Button btwFalse;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        findViews();

        btwTrue.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                checkAnswer(true);
            }
        });

        btwFalse.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                checkAnswer(false);
            }
        });
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if(resultCode!=RESULT_OK)
            return;
        if (requestCode == REQUEST_CODE_CHEAT) {
            if (data == null)
                return;
            mIsCheater = data.getBooleanExtra("ANSWER_IS_SHOWN",false);
        }
    }

    private void findViews() {
        btwTrue = (Button)findViewById(R.id.btn_true);
        btwFalse = (Button)findViewById(R.id.btn_false);
    }

    private void checkAnswer (boolean userPressTrue){
        String message = "";
        if(mIsCheater)
            message = "Cheating is wrong";
        else{
            if (ANSWER_IS_TRUE == userPressTrue )
                message = "Correct";
            else
                message = "Incorrect";
        }
        Toast.makeText(this,message,Toast.LENGTH_LONG).show();
    }

    public void cheat(View view) {
        Intent intent = new Intent(this,CheatActivity.class);
        intent.putExtra("ANSWER_IS_TRUE", ANSWER_IS_TRUE);
        startActivityForResult(intent,REQUEST_CODE_CHEAT);
    }
}
```

CheatActivity.java

```java
package com.little.testpassdatabetweenactivities;

import android.content.Intent;
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.view.View;
import android.widget.TextView;

public class CheatActivity extends AppCompatActivity {

    TextView tvAnswer;
    private boolean answerIsTrue;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_cheat);

        tvAnswer = findViewById(R.id.tv_answer);
    }

    public void showAnswer(View view) {
        Intent intent= getIntent();
        answerIsTrue = intent.getBooleanExtra("ANSWER_IS_TRUE",false);
        intent.putExtra("ANSWER_IS_SHOWN",true);
        setResult(RESULT_OK,intent);
        tvAnswer.setText(answerIsTrue+"");
    }
}
```

Layout 和前言中的程式碼一樣。

執行結果如下，可以發現偷看過答案後的回覆並不會是你的答案正確與否，反而會說作弊是不對的ＸＤＤＤ

<img src="{{ site.img_path }}/20171201/exe.gif" width="100%" style="max-width:350px;"/>


<h2 id="4"></h2>

# 四、題外話：關於 Activity 的生命週期

完成上述程式碼後就已經可以掌握本章主題，相信冰雪聰明的讀者會發現一個問題，就是當我們旋轉螢幕後 App 就不知道我作弊了OMG！其實這是因為 Activity 生命週期的緣故，旋轉 App 後 Activity 會被摧毀並且重新投胎。讀者可以放心的參考以下程式碼或在下所寫的[生命週期的文章](https://foolcodefun.github.io/blog/android/2017/11/09/Android-Lifecycle.html)來解決這個問題。


MainActivity.java
```java
package com.little.testpassdatabetweenactivities;

import android.content.Intent;
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

public class MainActivity extends AppCompatActivity {

    public static final boolean ANSWER_IS_TRUE = false;
    private static final int REQUEST_CODE_CHEAT = 0;

    private boolean mIsCheater;

    private Button btwTrue;
    private Button btwFalse;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        if(savedInstanceState!=null){
            mIsCheater=savedInstanceState.getBoolean(CheatActivity.ANSWER_IS_SHOWN);
        }

        findViews();

        btwTrue.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                checkAnswer(true);
            }
        });

        btwFalse.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                checkAnswer(false);
            }
        });
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if(resultCode!=RESULT_OK)
            return;
        if (requestCode == REQUEST_CODE_CHEAT) {
            if (data == null)
                return;
            mIsCheater = data.getBooleanExtra(CheatActivity.ANSWER_IS_SHOWN,false);
        }
    }

    @Override
    protected void onSaveInstanceState(Bundle outState) {
        super.onSaveInstanceState(outState);
        outState.putBoolean(CheatActivity.ANSWER_IS_SHOWN,mIsCheater);
    }

    private void findViews() {
        btwTrue = (Button)findViewById(R.id.btn_true);
        btwFalse = (Button)findViewById(R.id.btn_false);
    }

    private void checkAnswer (boolean userPressTrue){
        String message = "";
        if(mIsCheater)
            message = "Cheating is wrong";
        else{
            if (ANSWER_IS_TRUE == userPressTrue )
                message = "Correct";
            else
                message = "Incorrect";
        }
        Toast.makeText(this,message,Toast.LENGTH_LONG).show();
    }

    public void cheat(View view) {
        Intent intent = new Intent(this,CheatActivity.class);
        intent.putExtra("ANSWER_IS_TRUE", ANSWER_IS_TRUE);
        startActivityForResult(intent,REQUEST_CODE_CHEAT);
    }
}
```

CheatActivity.java
```java
package com.little.testpassdatabetweenactivities;

import android.content.Intent;
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.view.View;
import android.widget.TextView;

public class CheatActivity extends AppCompatActivity {

    public static final String ANSWER_IS_SHOWN ="ANSWER_IS_SHOWN";

    TextView tvAnswer;
    private boolean answerIsTrue;
    private boolean isAnswerShown;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_cheat);

        tvAnswer = findViewById(R.id.tv_answer);

        if(savedInstanceState!=null) {
            isAnswerShown = savedInstanceState.getBoolean(ANSWER_IS_SHOWN);
            setAnswerResult(isAnswerShown);
        }


    }

    @Override
    protected void onSaveInstanceState(Bundle outState) {
        super.onSaveInstanceState(outState);
        outState.putBoolean(ANSWER_IS_SHOWN, isAnswerShown);
    }

    public void showAnswer(View view) {
        Intent intent= getIntent();
        answerIsTrue = intent.getBooleanExtra("ANSWER_IS_TRUE",false);
        isAnswerShown = true;
        setAnswerResult(isAnswerShown);
        tvAnswer.setText(answerIsTrue+"");
    }
    
    private void setAnswerResult(boolean answerIsShown){
        Intent intent = new Intent();
        intent.putExtra(ANSWER_IS_SHOWN,true);
        setResult(RESULT_OK,intent);
    }
}
```

至於 layout 程式碼內容就跟一開始前言中的程式碼一樣。

<h2 id="5"></h2>

# 參考資料
本篇文章參考 [Android Programing The Big Nerd Ranch Guide](https://www.bignerdranch.com/books/android-programming/) 的 Geoquiz 範例和 [Android 官網](https://developer.android.com/reference/android/app/Activity.html#setResult(int))。

本文結束，感謝各位讀者閱讀，歡迎各位讀者大大打屁聊天留言。

