---
layout: post
title:  "Android 推播入門 (Broadcast)"
date:   2018-01-11
desc: "Broadcast"
keywords: "Android,Broadcast"
categories: [Android]
tags: [Android,Broadcast]
icon: icon-html
---

* ### [一、前言簡介](#1)
* ### [二、Broadcast 和 BroadcastReceiver 的基本使用](#2)
> ### [2.1 Menifest 註冊](#2.1)
> ### [2.2 動態註冊](#2.2)
* ### [三、簡易範例](#3)
* ### [參考資料](#4)

<h2 id="1"></h2>

# 前言簡介

廣播是什麼？回想一下日常生活中什麼時候得用到廣播，像是小孩走失時工作會用廣播的方式找到小孩的家人，或是學校要進行集合的時候會用廣播告訴個班級的同學，而 Broadcast 也是差不多的概念。當手機中的特定事件發生時，系統會傳送 Broadcast 告訴手機裡的 APP 某事件發生了，而手機裡的 APP 是利用 BroadcastReceiver 來接收這些廣播訊息。比如說當手機開機動作完成時會傳送 `ACTION_BOOT_COMPLETED` 訊息，手機中的 APP 在 Menifest 有 `android.permission.RECEIVE_BOOT_COMPLETED` 權限且 APP 的 BroadcastReceiver 有註冊要接收 `android.intent.action.BOOT_COMPLETED` 這個動作 (action) 的話就會收到手機系統傳來"手機以開啟完成"廣播。

若手機傳送廣播時，大部分的 APP 都接收到訊息並且被啟用可能會導致手機資源耗盡，導致手機閃退或是當機，因此官方在 Broadcast 的使用上做了些調整。在 Android 7.0 (API24) 和以上的版本不允許 `CONNECTIVITY_ACTION` 在 Menifest 中註冊，而 `ACTION_NEW_PICTURE` 和 `ACTION_NEW_VIDEO` 則不允許在所有 APP 的 Menifest 上註冊。若想要註冊這兩個方法只能使用動態註冊，動態註冊意味著只有當該 APP 正在運行時會收到以動態註冊的 action，註冊方法則是透過 `registerReceiver(BroadcastReceiver, IntentFilter)` 完成。Android 8.0 (API26) 發布後只允許[這份名單上的行為](https://developer.android.com/guide/components/broadcast-exceptions.html)在 Menifest 上註冊。基於這些限制，開發者可透過 [JobSecheduler](https://foolcodefun.github.io/blog/android/2018/01/08/Android-JobSchedule.html) 或是動態註冊的方法取代 Menifest 註冊。


<h2 id="2"></h2>

# 二、Broadcast 的基本使用與註冊

APP 接收 Broadcast 的方法有兩種，一種是在 Menifest.xml 中宣告，另一種是在 APP 中的 Context 註冊 (動態註冊)。動態註冊只有在 APP 的 Runtime 可以收到 Broadcast，第一種方法則是裝置只要再開啟的狀態 APP 都可以收到在 Menifest 中宣告過的 Broadcast。

接收 Broadcast 仰賴 BroadcastReceiver，因此請各位者在專案中新增一類別繼承 BroadcastReceiver 如下範例。當 BroadcastReceiver 接收到 Broadcast 時會調用 `onReceive` 方法。

MyReciver.java

```java
package com.little.testbroadcast;

import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.util.Log;

/**
 * Created by sarah on 15/01/2018.
 */

public class MyReciver extends BroadcastReceiver {
    private static final String TAG = MyReciver.class.getSimpleName();

    @Override
    public void onReceive(Context context, Intent intent) {
        Log.d(TAG, "onReceive: ");
        
        }
    }
}

```

<h2 id="2.1"></h2>

## 2.1 Menifest 註冊

Android 8.0 (API26) 限制了[背景執行](Background Execution Limits)，Broadcast 也受到影響。能在 Menifest 文件中註冊的 Implict Broadcast 必須符合[規範](https://developer.android.com/guide/components/broadcast-exceptions.html)，規範中有列出可以在 Menifest 中註冊的 Implict Broadcast。

以註冊 BOOT_COMPLETED 為例，首先這個 APP 必須有 RECEIVE_BOOT_COMPLETED 的權限，且 APP 中繼承 BroadcastReceiver 的類別必須設定要接收的 action 為 BOOT_COMPLETED。程式碼如下，請讀者看 `ueses-permission` 和 `receiver` 的標籤，這兩個標籤裡的設定舊日上述文字說明的部分。

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          package="com.little.testbroadcast">

    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>
    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN"/>

                <category android:name="android.intent.category.LAUNCHER"/>
            </intent-filter>
        </activity>
        <receiver
            android:name=".MyReciver">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
            </intent-filter>
        </receiver>
    </application>
</manifest>
```

完成上述程式碼後，只要手機重新啟動，這個 APP 就會接收到 Broadcast 進而調用 `onReceive` 方法。

<h2 id="2.2"></h2>

## 2.2 動態註冊

如果特定廣播只需要在 APP 運行時接收那就可以使用動態註冊。此 APP 範例會在按下按鈕後送出廣播，也就是 `sendBroadcast`，因此 BroadcastReceiver 必須同一個 Activtiy 中註冊，如此一來才會能夠接收到送出去的廣播。

動態註冊 Broadcast 首先需要建立一個 BroadcastReceiver 的實例，在定義其所接收 Broadcast 的 action 為何。

建立 BroadcastReceiver 實例

```java
BroadcastReceiver mBroadcastReceiver=new MyReciver();
```

註冊 BroadcastReceiver

```java
IntentFilter filter = new IntentFilter(mAction);
registerReceiver(mBroadcastReceiver,filter);
```

解註冊

```java
unregisterReceiver(mBroadcastReceiver);
```

<h2 id="3"></h2>

# 三、簡易範例

此範例使用了 Menifest 註冊 BOOT_COMPLETED，使用動態註冊 ACTION_CLICK。BroadcastReceiver 會在手機開機完成時接收到訊息，另一個會接收到訊息的時機是在點下螢幕按鈕時調用 `sendBroadcast` 方法時，這兩個時機都會使 APP 中的 BroadcastReceiver 執行 onReceive 方法。

Menifesdt.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          package="com.little.testbroadcast">

    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>
    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN"/>

                <category android:name="android.intent.category.LAUNCHER"/>
            </intent-filter>
        </activity>
        <receiver
            android:name=".MyReciver">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
            </intent-filter>
        </receiver>
    </application>
</manifest>
```

activity_main.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.little.testbroadcast.MainActivity">

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="8dp"
        android:layout_marginEnd="8dp"
        android:layout_marginStart="8dp"
        android:layout_marginTop="8dp"
        android:onClick="setActoin"
        android:text="Call broadcast"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"/>
</android.support.constraint.ConstraintLayout>
```

MainActivity.java

```java
package com.little.testbroadcast;

import android.content.BroadcastReceiver;
import android.content.Intent;
import android.content.IntentFilter;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.View;

public class MainActivity extends AppCompatActivity {

    MyReciver mBroadcastReceiver;
    public static final String mAction ="com.little.testbroadcast.Action";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        mBroadcastReceiver=new MyReciver();
    }

    @Override
    protected void onResume() {
        super.onResume();
        IntentFilter filter = new IntentFilter(mAction);
        registerReceiver(mBroadcastReceiver,filter);
    }

    public void setActoin(View view) {
        Intent intent = new Intent(mAction);
        sendBroadcast(intent);
    }

    @Override
    protected void onPause() {
        super.onPause();
        unregisterReceiver(mBroadcastReceiver);
    }
}
```

MyReciver.java

```java
package com.little.testbroadcast;

import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.util.Log;

/**
 * Created by sarah on 15/01/2018.
 */

public class MyReciver extends BroadcastReceiver {
    private static final String TAG = MyReciver.class.getSimpleName();

    @Override
    public void onReceive(Context context, Intent intent) {
        Log.d(TAG, "onReceive: ");
        if (intent.getAction() == Intent.ACTION_BOOT_COMPLETED
                || intent.getAction() == MainActivity.mAction) {
            Log.d(TAG, "onReceive: " + intent.getAction());
        }
    }
}
```

完成此範例後，只要按下送出 action 為 com.little.testbroadcast.Action 的 Broadcast，程式碼中的 MyReciver 接收到廣播便會調用 `onReceive` 方法。另一個會接收到廣播的時機是裝置開機完成時，當開機動作完成時 MyReciver 的 `onReceive`` 法也會因為接收到廣播而被調用。

<h2 id="4"></h2>

# 參考資料

[Android 官網：Broadcast](https://developer.android.com/guide/components/broadcasts.html)