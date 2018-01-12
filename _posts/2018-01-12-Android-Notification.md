---
layout: post
title:  "Android 通知的基本使用方法 (Notification)"
date:   2018-01-11
desc: "Notification"
keywords: "Android,Notification"
categories: [Android]
tags: [Android,Notification]
icon: icon-html
---

* ### [一、前言簡介](#1)
* ### [二、通知的基本使用](#2)
* ### [三、簡易範例](#3)
* ### [參考資料](#4)

<h2 id="1"></h2>

# 一、前言簡介

許多 APP 會設定通知 (Natification)，Natification 會在特定時間或特定情形下通知使用者一些訊息。本篇文章會帶會講解 Natification 用以及 Android 8.0 (API 26) 的 Notification Channel 的基本使用。下圖是一個 Notification。

<img src="{{ site.img_path }}/20180112/notification.png" width="100%" style="max-width:300px;"/>


<h2 id="2"></h2>

# 二、通知的基本使用

Natification 的是透過 Notification.builder 建置的；NotificationManager 顧名思義其功能是用來管理 Natification；NatificationChannel 則是在 Android 8.0 特別退出的新功能，它可以讓使用者設定此 APP 的某異類通知是否要開啟等功能，若是開發目標大於或等於 API26 則必須在 Notification.builder 設定 NatificationChannel，否則通知將不會被啟動。Natification 的基本使用可參考下面的程式碼。

```java
NotificationManager manager = (NotificationManager)                         getSystemService(NOTIFICATION_SERVICE);
Notification.Builder builder = new Notification.Builder(this)
    .setContentTitle("Test Title")
    .setContentText("This is a test of text")
    .setSmallIcon(R.drawable.alert);    
NotificationChannel channel;
if(Build.VERSION.SDK_INT>=Build.VERSION_CODES.O){
    channel = new NotificationChannel(TEST_NOTIFY_ID
        ,"Notify Test"
        ,NotificationManager.IMPORTANCE_HIGH);
    builder.setChannelId(TEST_NOTIFY_ID);
    manager.createNotificationChannel(channel);
}else{
    builder.setDefaults(Notification.DEFAULT_ALL)
            .setVisibility(Notification.VISIBILITY_PUBLIC);
}
manager.notify(testNotifyId,builder.build());
```

上述範例中職得注意的是 Notification.builder 必須要 `setSmallIcon`，否則統會閃退。API26 以上要記得設定 NotificationChanne。

API26 的模擬器執行結果

<img src="{{ site.img_path }}/20180112/notificationAPI26.png" width="100%" style="max-width:300px;"/>

在 API26 的執行時，通知會出現在螢幕上方如圖所示，並發出提示聲。


API25 的模擬器執行結果

<img src="{{ site.img_path }}/20180112/notificationAPI25.png" width="100%" style="max-width:300px;"/>

上圖的左上角可以看到一個 icon，那個是按下 APP 按鈕後出現的通知，並且發出提示聲。

<h2 id="3"></h2>

# 三、簡易範例

這個範例是從上述範例改寫而成，這個多了一個功能。此功能就是當使用者點擊通知時，行動裝置會將 APP 打開並顯示到 MainActivity 頁面。

activity_main.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.little.testnotification.MainActivity">

    <Button
        android:id="@+id/btn_notify"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="8dp"
        android:layout_marginEnd="8dp"
        android:layout_marginStart="8dp"
        android:layout_marginTop="8dp"
        android:onClick="showNotification"
        android:text="Notify"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"/>
</android.support.constraint.ConstraintLayout>
```
MainActivity.java

```java
package com.little.testnotification;

import android.app.Notification;
import android.app.NotificationChannel;
import android.app.NotificationManager;
import android.app.PendingIntent;
import android.content.Intent;
import android.os.Build;
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.util.Log;
import android.view.View;

public class MainActivity extends AppCompatActivity {

    private static final String TAG = MainActivity.class.getSimpleName();
    private static final String TEST_NOTIFY_ID = "test_notyfy_id";
    private static final int NOTYFI_REQUEST_ID = 300;
    private int testNotifyId = 11;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }

    public void showNotification(View view) {
        Log.d(TAG, "showNotification: ");

        Intent intent =new Intent(getApplicationContext(),MainActivity.class);
        PendingIntent pendingIntent = PendingIntent.getActivity(getApplicationContext(),
                NOTYFI_REQUEST_ID,
                intent,
                PendingIntent.FLAG_UPDATE_CURRENT);

        NotificationManager manager = (NotificationManager) getSystemService(NOTIFICATION_SERVICE);
        Notification.Builder builder = new Notification.Builder(this)
                .setContentTitle("Test Title")
                .setContentText("This is a test of text")
                .setSmallIcon(R.drawable.alert)
                .setContentIntent(pendingIntent);
        NotificationChannel channel;
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            channel = new NotificationChannel(TEST_NOTIFY_ID
                    , "Notify Test"
                    , NotificationManager.IMPORTANCE_HIGH);
            builder.setChannelId(TEST_NOTIFY_ID);
            manager.createNotificationChannel(channel);
        } else {
            builder.setDefaults(Notification.DEFAULT_ALL)
                    .setVisibility(Notification.VISIBILITY_PUBLIC);
        }
        manager.notify(testNotifyId,
                builder.build());
    }
}
```

<h2 id="4"></h2>

# 參考資料
[Android Developers：Notification Updates in Android Oreo](https://www.youtube.com/watch?v=zGIw4MIJn5o)

[官網：Notifications](https://developer.android.com/guide/topics/ui/notifiers/notifications.html?hl=zh-tw)

[官網：Building a Notification](https://developer.android.com/training/notify-user/build-notification.html)