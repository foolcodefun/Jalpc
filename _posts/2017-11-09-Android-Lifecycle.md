---
layout: post
title:  "Android The Activity Lifecycle"
date: 2017-11-09
desc: "Android Lifecycle"
keywords: "Android, Lifecycle"
categories: [Android]
tags: [Android,Lifecycle]
icon: icon-html
---
# 目錄


* ### [一、前言簡介](#1)
* ### [二、Activity 的四個狀態](#2)
> ### [nonexistent](#2)
> ### [stopped](#2)
> ### [paused](#2)
> ### [running](#2)
* ### [三、使用者操作行為與生命週期的關係](#3)
> ### [1. 開啟 App](#3.1)
> ### [2. 按返回鍵](#3.2)
> ### [3. 按 Home 鍵](#3.3)
> ### [4. 按 Recents 鍵](#3.4)
> ### [5. 旋轉螢幕](#3.5)

<h2 id="1"></h2>
* ### [四、螢幕旋轉時資料遺失的解決方法](#4)
> ### [1. onSaveInstanceState 方法](#4.1)
> ### [2. 禁止螢幕旋轉](#4.2)


# 一、前言簡介
認識 Activity 生命週期 (Lifecycle) 可以幫助開發者掌握 Activity 的狀態，本章節將會對 Activity 生命週期的四個狀態 (running、paused、stopped 和 nonexistent) 、七個生命週期方法 (onCreate()、onStart()、onResume()、onPause()、onStop()、onRestart() 和 onDestroy()) 以及使用者操作行為與生命週期的關係做詳細說明。

<img src="{{ site.img_path }}/20171110/lifecycle.png" width="100%" style="min-width:450px;max-width:600px;"/>

<h2 id="2"></h2>

上圖為 Activity 的生命週期圖，資料來源為 [Android 官方網站](https://developer.android.com/guide/components/activities/activity-lifecycle.html)

# 二、Activity 的四個狀態
本段文章將介紹 Activity 的四個工作狀態，並說明是否佔用記憶體、可被看見以及位於前景。

<h2 id="3"></h2>

> nonexistent：在此狀態 Activity 並不存在、無法被看見且不佔用記憶體容量。
>
> stopped：此狀態 Activity 已被其他 Activity 覆蓋，它無法被看見但還是佔用著記憶體。
>
> paused：當 AlertDialog 或電話來時，Activity 被遮住或呈半透明狀態時使用者不能對其進行操作，但可能被看見且佔有手機記憶體。
>
> running：此狀態是可被看見的且使用者正聚焦於此 Activity，意為著 Activity 位於前景的狀態，此時佔有部分記憶體。


# 三、使用者操作行為與生命週期的關係

此段文章會覆寫一些生命週期都方法並以 Log 記錄其執行情形，執行情形包括開啟 App、按返回鍵、按 Home 鍵、按 Recents 鍵和旋轉螢幕。下圖是手機按鍵示意圖。

<img src="{{ site.img_path }}/20171110/keyboard.png" width="100%" style="min-width:300px;max-width:350px;"/>

MainActivity.java 程式碼如下。

```java
package com.example.sarah.testlifecycle;

import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.TextView;

public class MainActivity extends AppCompatActivity {

    private static final String TAG = "MainActivity";


    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Log.d(TAG,"onCreate");
    }

    @Override
    protected void onStart() {
        super.onStart();
        Log.d(TAG,"onStart");
    }

    @Override
    protected void onResume() {
        super.onResume();
        Log.d(TAG,"onResume");
    }

    @Override
    protected void onPause() {
        super.onPause();
        Log.d(TAG,"onPause");
    }

    @Override
    protected void onStop() {
        super.onStop();
        Log.d(TAG,"onStop");
    }

    @Override
    protected void onRestart() {
        super.onRestart();
        Log.d(TAG,"onRestart");
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        Log.d(TAG,"onDestroy");
    }
}

```

寫好上述程式後，再去設定 Logcat 的 filter，如下圖。

<img src="{{ site.img_path }}/20171110/logcat.png" width="100%" style="min-width:600px;max-width:1000px;"/>

<h2 id="3.1"></h2>

<img src="{{ site.img_path }}/20171110/logcatfilter.png" width="100%" style="min-width:300px;max-width:450px;"/>

## 1. 開啟 App

開啟 App 後 Logcat 會跑出以下訊息，此時 Activity 處於 running 的狀態。

<h2 id="3.2"></h2>

```
11-08 20:43:57.723 2655-2655/com.example.sarah.testlifecycle D/MainActivity: onCreate
11-08 20:43:57.732 2655-2655/com.example.sarah.testlifecycle D/MainActivity: onStart
11-08 20:43:57.736 2655-2655/com.example.sarah.testlifecycle D/MainActivity: onResume
```

## 2. 按返回鍵

當 App 開啟後再按下返回鍵會跑出以下訊息，此時是 nonexistent 的狀態，因為返回鍵代表著使用者不在需要執行此 Activity。

<h2 id="3.3"></h2>

```
11-08 20:53:53.229 11843-11843/com.example.sarah.testlifecycle D/MainActivity: onPause
11-08 20:53:53.647 11843-11843/com.example.sarah.testlifecycle D/MainActivity: onStop
11-08 20:53:53.648 11843-11843/com.example.sarah.testlifecycle D/MainActivity: onDestroy
```

## 3. 按 Home 鍵

當 App 已經開啟後再按下 Home 鍵會跑出以下訊息，按下 Home 鍵代表著使用者暫時去看別的東西但可能還會回來，此時 Activity 處於 stopped的狀態。

<h2 id="3.4"></h2>

```
11-08 20:48:31.850 2655-2655/com.example.sarah.testlifecycle D/MainActivity: onPause
11-08 20:48:31.875 2655-2655/com.example.sarah.testlifecycle D/MainActivity: onStop
```


## 4. 按 Recents 鍵

當 App 已經開啟後再按下 Recents 鍵會跑出以下訊息，此時處在 stopped 狀態。

```
11-08 20:49:38.165 2655-2655/com.example.sarah.testlifecycle D/MainActivity: onPause
11-08 20:49:38.187 2655-2655/com.example.sarah.testlifecycle D/MainActivity: onStop
```
當 App執行時按下 Home 後按下 Recents 再打開 App 會跑出下列訊息，程式並未執行 `oncreate` 而是執行 `onRestart`，這是因為在按下 Home 鍵後程式並未執行onDestroy。

```
11-08 21:45:34.733 3196-3196/com.example.sarah.testlifecycle D/MainActivity: onRestart
11-08 21:45:34.734 3196-3196/com.example.sarah.testlifecycle D/MainActivity: onStart
11-08 21:45:34.735 3196-3196/com.example.sarah.testlifecycle D/MainActivity: onResume
```

當 App 執行時按下 Recents 後再打開 App 夜會跟上面是一樣的情形。
```
11-08 21:46:24.355 3196-3196/com.example.sarah.testlifecycle D/MainActivity: onRestart
11-08 21:46:24.356 3196-3196/com.example.sarah.testlifecycle D/MainActivity: onStart
11-08 21:46:24.357 3196-3196/com.example.sarah.testlifecycle D/MainActivity: onResume
```

當 App 按下 返回鍵之後按下 Recents 再打開 App 會跑出以下訊息，此時就會執行 `onCreate` 方法。

<h2 id="3.5"></h2>

```
11-08 21:33:25.849 2607-2607/com.example.sarah.testlifecycle D/MainActivity: onCreate
11-08 21:33:25.851 2607-2607/com.example.sarah.testlifecycle D/MainActivity: onStart
11-08 21:33:25.855 2607-2607/com.example.sarah.testlifecycle D/MainActivity: onResume
```

## 5. 旋轉螢幕

旋轉螢幕時，程式會將原本的 Activity 狀態全部摧毀再重新打開，如下。這會產生資料遺失的問題，下一章節將會詳細介紹問題以及解決的方法。

<h2 id="4"></h2>

```
11-08 20:51:47.647 2655-2655/com.example.sarah.testlifecycle D/MainActivity: onPause
11-08 20:51:47.651 2655-2655/com.example.sarah.testlifecycle D/MainActivity: onStop
11-08 20:51:47.651 2655-2655/com.example.sarah.testlifecycle D/MainActivity: onDestroy
11-08 20:51:47.698 2655-2655/com.example.sarah.testlifecycle D/MainActivity: onCreate
11-08 20:51:47.700 2655-2655/com.example.sarah.testlifecycle D/MainActivity: onStart
11-08 20:51:47.703 2655-2655/com.example.sarah.testlifecycle D/MainActivity: onResume
```

# 四、螢幕旋轉時資料遺失的解決方法

當螢幕選轉時，生命週會完全再進入一個新的生命週期，這會使原本執行時的資料消失，如以下兩張圖。

按了十三次 Plus One 按鈕後如下圖。

<img src="{{ site.img_path }}/20171110/beforeRotate.png" width="100%" style="min-width:300px;max-width:350px;"/>

接著旋轉螢幕，數字又從零開始。

<img src="{{ site.img_path }}/20171110/afterRotate.png" width="100%" style="min-width:500px;max-width:600px;"/>

其程式碼如下。

activity_main.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.example.sarah.testlifecycle.MainActivity">

    <TextView
        android:id="@+id/num"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="0"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <Button
        android:id="@+id/plusone"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Plus one"
        android:onClick="plusOne"
        app:layout_constraintBottom_toBottomOf="parent"
        android:layout_marginBottom="8dp"
        android:layout_marginTop="8dp"
        app:layout_constraintTop_toBottomOf="@+id/num"
        android:layout_marginLeft="8dp"
        app:layout_constraintLeft_toLeftOf="parent"
        android:layout_marginRight="8dp"
        app:layout_constraintRight_toRightOf="parent" />

</android.support.constraint.ConstraintLayout>
```

MainActivity.java

```java
package com.example.sarah.testlifecycle;

import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.TextView;

public class MainActivity extends AppCompatActivity {

    private static final String TAG = "MainActivity";
    private int num ;
    private TextView textnum;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Log.d(TAG,"onCreate");

        textnum = (TextView)findViewById(R.id.num);
    }

    @Override
    protected void onStart() {
        super.onStart();
        Log.d(TAG,"onStart");
    }

    @Override
    protected void onResume() {
        super.onResume();
        Log.d(TAG,"onResume");
    }

    @Override
    protected void onPause() {
        super.onPause();
        Log.d(TAG,"onPause");
    }

    @Override
    protected void onStop() {
        super.onStop();
        Log.d(TAG,"onStop");
    }

    @Override
    protected void onRestart() {
        super.onRestart();
        Log.d(TAG,"onRestart");
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        Log.d(TAG,"onDestroy");
    }

    public void plusOne(View v){
        num++;
        textnum.setText(""+num);
    }
}
```

<h2 id="4.1"></h2>

要解決上述問題有兩種方法，一是利用 onSaveInstanceState 方法記錄下數值，另一個是禁止螢幕旋轉，以下是兩種方法詳細的介紹。

## 1. onSaveInstanceState 方法

利用 onSaveInstanceState 方法和 Bundle 儲存當下的狀態可以解決資料遺失的問題。其程式修改的部分有 `onCreate` 方法、 新增 `onSaveInstanceState`方法還有全域變數，如以下程式。

```java
package com.example.sarah.testlifecycle;

import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.TextView;

public class MainActivity extends AppCompatActivity {

    private static final String TAG = "MainActivity";
    private static final String INDEX = "index";
    private int num ;
    private TextView textnum;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Log.d(TAG,"onCreate");

        textnum = (TextView)findViewById(R.id.num);
        if(savedInstanceState!=null)
            num=savedInstanceState.getInt(INDEX,0);
            textnum.setText(""+num);
    }

    @Override
    protected void onStart() {
        super.onStart();
        Log.d(TAG,"onStart");
    }

    @Override
    protected void onResume() {
        super.onResume();
        Log.d(TAG,"onResume");
    }

    @Override
    protected void onPause() {
        super.onPause();
        Log.d(TAG,"onPause");
    }

    @Override
    protected void onStop() {
        super.onStop();
        Log.d(TAG,"onStop");
    }

    @Override
    protected void onRestart() {
        super.onRestart();
        Log.d(TAG,"onRestart");
    }

    @Override
    protected void onSaveInstanceState(Bundle outState) {
        super.onSaveInstanceState(outState);
        Log.d(TAG,"onSaveInstanceState");
        outState.putInt(INDEX,num);
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        Log.d(TAG,"onDestroy");
    }

    public void plusOne(View v){
        num++;
        textnum.setText(""+num);
    }
}

```

按了十三次 Plus One 按鈕後如下圖。

<img src="{{ site.img_path }}/20171110/beforeState.png" width="100%" style="min-width:300px;max-width:350px;"/>

接著旋轉螢幕，數字依然是 13。

<img src="{{ site.img_path }}/20171110/afterState.png" width="100%" style="min-width:500px;max-width:600px;"/>

旋轉後的 Log 訊息如下。

```
11-09 15:53:02.523 2675-2675/com.example.sarah.testlifecycle D/MainActivity: onPause
11-09 15:53:02.524 2675-2675/com.example.sarah.testlifecycle D/MainActivity: onSaveInstanceState
11-09 15:53:02.529 2675-2675/com.example.sarah.testlifecycle D/MainActivity: onStop
11-09 15:53:02.529 2675-2675/com.example.sarah.testlifecycle D/MainActivity: onDestroy
11-09 15:53:02.599 2675-2675/com.example.sarah.testlifecycle D/MainActivity: onCreate
11-09 15:53:02.601 2675-2675/com.example.sarah.testlifecycle D/MainActivity: onStart
11-09 15:53:02.604 2675-2675/com.example.sarah.testlifecycle D/MainActivity: onResume
```

如果想在旋轉後的螢幕配置不一樣的版面，操作方法如下。

(1) 創建 layout-land 資料夾

<img src="{{ site.img_path }}/20171110/newdectory.png" width="100%" style="min-width:600px;max-width:700px;"/>

(2) 輸入以下訊息並按下 >> 按鈕

<img src="{{ site.img_path }}/20171110/orintation.png" width="100%" style="min-width:600px;max-width:700px;"/>

(3) 選擇 landscape 在按下 ok 按鈕，layout-land 資料夾創建完成

<img src="{{ site.img_path }}/20171110/landscape.png" width="100%" style="min-width:600px;max-width:700px;"/>

(4) 將 res/layout 資料夾的 activity_main.xml 複製到 res/layout-land 中，若找不到此資料夾請選擇 project 模式。

<img src="{{ site.img_path }}/20171110/layoutland.png" width="100%" style="min-width:250px;max-width:350px;"/>

可在 Terminal 中輸入指令複製檔案，輸入指令如下

```
cp app/src/main/res/layout/activity_main.xml app/src/main/res/layout-land
```

回到 android view 可看到新的 xml 檔案

<img src="{{ site.img_path }}/20171110/androidview.png" width="100%" style="min-width:250px;max-width:350px;"/>

(5) 更改 res/layout-land/activity_main.xml 的程式碼，此程式碼會用在手機在水平狀態下的版面配置

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.example.sarah.testlifecycle.MainActivity">

    <TextView
        android:id="@+id/num"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="0"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <Button
        android:id="@+id/plusone"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Plus one"
        android:onClick="plusOne"
        app:layout_constraintBottom_toBottomOf="parent"
        android:layout_marginBottom="8dp"
        android:layout_marginTop="8dp"
        app:layout_constraintTop_toBottomOf="@+id/num"
        android:layout_marginLeft="8dp"
        app:layout_constraintLeft_toLeftOf="parent"
        android:layout_marginRight="8dp"
        app:layout_constraintRight_toRightOf="parent" />

</android.support.constraint.ConstraintLayout>
```

(6) 新的水平螢幕版面配置如下

<h2 id="4.2"></h2>

<img src="{{ site.img_path }}/20171110/screenland.png" width="100%" style="min-width:500px;max-width:600px;"/>
 
## 2. 禁止螢幕旋轉

手機旋轉時，禁止螢幕畫面旋轉，因此 Activity 不會刷新，就不會產生資料遺失的可能性。只要在 AndroidManifest.xml 中加入`android:screenOrientation="portrait"`便可禁止螢幕旋轉，完整程式碼如下。

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.sarah.testlifecycle">

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".MainActivity"
            android:screenOrientation="portrait">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```

雖然可透過禁止螢幕旋轉來達到目的，但有的時候反而會影響到使用者對 App 的使用體驗，所以並不建議使用此方法。

# 參考資料 

[Android Programing The Big Nerd Ranch Guide](https://www.bignerdranch.com/books/android-programming/)

本篇結束，歡迎各位讀者留言交流，感謝。
