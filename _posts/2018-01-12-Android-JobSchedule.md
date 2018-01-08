---
layout: post
title:  "Android 淺談後台計算與 JobScheduler"
date:   2018-01-12
desc: "BackGround Operation"
keywords: "Android,JobScheduler"
categories: [Android]
tags: [Android,JobScheduler]
icon: icon-html
---

* ### [一、前言簡介](#1)
* ### [二、JobScheduler 的基本使用](#2)
> ### [2.1 JobScheduler](#2.1)
> ### [2.2 JobInfo](#2.2)
> ### [2.3 JobService](#2.3)
* ### [三、簡易範例](#3)
* ### [參考資料](#4)

<h2 id="1"></h2>

# 一、前言簡介

在很多情況下 APP 必須進行後台計算，後台計算意味著此時 APP 並沒有顯示在手機螢幕上，但 APP 仍然正在執行一些功能，像是利用網路更新消息、播放音樂或是壓縮照片等等。根據[官方網站](https://developer.android.com/guide/background/index.html)說明，許多 APP 需要在背景運作，開發者可透過 Schedule Jobs、Services、Broadcasts 和 Alarms 來進行後台計算，也就是背景任務處理。但在眾多 APP 都進行背景處理的情況下會造手機資源 (RAM、電力或 CPU 等等) 資源耗盡，因此官方針對 API26 以上的後台計算進行[限制](https://developer.android.com/about/versions/oreo/background.html?#overview)。以下將簡單介紹 Schedule Jobs、Services、Broadcasts 和 Alarms 四種後台處理的方式及其於 API26 以上版本的限制。

* ## Schedule Jobs

> 這個選項後台計算時並不會消耗太多的系統資源且能規定多個調用條件，因此在官方將此選項列為最佳選項。開發者可利用 JobScheduler 處理 APP 在後台時資料更新，且可規定只有在裝置街上插頭電源和連接 wifi 時在進行資料下載更新，如此一來便不用擔心手機會因此耗盡電源或是下載資料時。大多數的後台運算都可藉由此方法完成。
> ### 特徵：
> * ### 只支援 API21 以上的系統
> * ### 可以規定多個執行件
> * ### 手機重新開機後會持續執行中斷的 Job，不需要額外加上監聽事件
>
> ### 使用時機：
> * ### 需要網路或 wifi 環境下的事件
> * ### 當手機正在連接電源時才執行的事件
> * ### 固定時間間隔執行的事件


* ## Services

> Services 通常應用在前景服務，例如正在播放的音樂會在通知列常駐並想是播放音樂的進度，所以他需要一個通知的 status bar icon。另外在 API26 以上的版本，為了避免後台 service 消耗太多手機資源，後台 service 已經被限制，當使用者結束 APP 的使用，後台 service 便會在很短的時間內關掉。但是光方提供了一個[白名單](https://developer.android.com/about/versions/oreo/background.html?#overview)，就是在以下幾種狀況下，service 是可以在後台被啟用。
> * ### 處理高優先權的 Firebase Cloud Messaging (FCM) 訊息
> * ### 接收廣播，像是 SMS/MMS 訊息
> * ### 從 notification 執行 PendingIntent
> * ### 當 VPN APP 還在後台時啟用 VpnService

* ## Broadcasts

> 從[官方網站](https://developer.android.com/guide/background/index.html)敘述中表示，利用註冊 APP 在 menifest 裡可以使 APP 接收報系統的廣播，但是同時有多個 APP 接收到同一個廣播可能會對系統造成傷害。因此在 API26 以上版對可以註冊的 Broadcast 作了一些[限制](https://developer.android.com/about/versions/oreo/background.html#broadcasts)，官方網站也同時提出了一個[可以在 menifest 註冊的清單](https://developer.android.com/guide/components/broadcast-exceptions.html)。若不在上述清單中或是開發人員若想自定義 action 則需利用動態註冊的方式達成目的。

* ## Alarms

> [AlarmManager](https://developer.android.com/training/scheduling/alarms.html) 裝置會在特定的時間醒來在透過傳送 intent 喚醒 APP。如果在特定時刻需要使用到這個 APP 便可使用這個方法，但是只是要展示一些簡單的事件則用 Job Scheduler 比較好。

想要了解更多關於 API26 後台計算限制的對應方法可參考[官方網站說明](https://developer.android.com/about/versions/oreo/background.html?#migration)。現在我們對後台計算有了一些概念，可以來詳細了解官方最推薦的後台計算方式，也就是 JobScheduler。

# 二、JobScheduler 的基本使用

首先我們來看看下面的程式碼來理解 [JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html) 的基本使用。

```java
JobScheduler jobScheduler = (JobScheduler) getSystemService(JOB_SCHEDULER_SERVICE);
if(true){
        JobInfo jobInfo = new JobInfo.Builder(JOB_TEST
                ,new ComponentName(getPackageName(),NotificationJobService.class.getName()))
                .setPeriodic(AlarmManager.INTERVAL_FIFTEEN_MINUTES)
                .setPersisted(true)
                .build();
        jobScheduler.schedule(jobInfo);
}else{
        jobScheduler.cancel(JOB_TEST);
}
```

在程式碼的一開始看到 JobScheduler 以 `Context.getSystemService(Context.JOB_SCHEDULER_SERVICE)` 取得實例。接著程式碼出現了一個 JobInfo，JobInfo 設定了所有關於這一個任務的詳細情形，包括此任務要喚醒的 JobService、以多少時間間隔執行此任務還有是否要 Persist 任務 (代表裝置重新開機後會不會繼續)。接著，在程式碼內可以看到 JobScheduler 呼叫了 `schedule(jobInfo)` 將任務排入。程式碼的最後則是用 `cancel()` 取消掉指定 jobId 的任務。以上是 JobScheduler 的基本使用，我們會發現要用 JobScheduler 還得要知道 JobInfo 和 JobService 是什麼。接下來分 JobScheduler、JobInfo 和 JobService 三個類別仔細說明。

<h2 id="2.1"></h2>

## 2.1 JobScheduler

取得 JobScheduler 實例的方法有 `Context.getSystemService(Context.JOB_SCHEDULER_SERVICE)` 和 `Context.getSystemService(Class)` 而參數 Class 是 JobScheduler.class。JobScheduler 是一個抽象類別，其公開方法也都是抽象方法，所以千萬不要用 `new JobScheduler` 取得 JobScheduler，而是用上述方法取的 JobScheduler 實例。

JobScheduler 的可用的公開方法有以下五種

> void cancel(int jobId)：取消 jobId 指定的 JobInfo 任務。
> 
> void cancelAll()：取消所有的任務。
> 
> int enqueue(JonInfo job, JobWorkItem work)
> 
> List<JobInfo> getAllPendingJobs()：取得所有 JobInfo。
>
> JobInfo getPendingJob(int jobId)：取得指定 jobId 的 JobInfo。
>
> int schedule(JonInfo jobInfo)：將 JobInfo 排入工作行程。
>

<h2 id="2.2"></h2>

## 2.2 JobInfo

JobInfo 是用 JobInfo.Builder 來建置的，在建置時必須指定一種條件，這樣才會系統知道在哪種情況下要執行任務。

```java
JobInfo.Builder(int jobId, ComponentName jobService);
```

上述程式碼是 JobInfo.Builder 的建構子，第一個參數是用來區別不同任務的 ID，第二個任參數是 ComponentName。在官方說明中 [ComponentName](https://developer.android.com/reference/android/content/ComponentName.html#ComponentName(java.lang.String, java.lang.String)) 是用來辨別 application component (Activity、 Service、BroadcastReceiver 或 ContentProvider)。在這裡我們得建置一個 JobService 的 ComponentName。

JobInfo.Builder 建置好後還得指定至少一種條件，如此一來系統才能透過該觸發條件之行任務。[官方文件](https://developer.android.com/reference/android/app/job/JobInfo.Builder.html#JobInfo.Builder(int, android.content.ComponentName)) 中列出了非常多種觸發條件可以設定。需要注意的是有些條件的設定需要特定 Permiision 才可以，像是 `setPersisted` 就需要  RECEIVE_BOOT_COMPLETED Permiision。

當 JobInfo.Builder 的條件與設定都完成後還要呼叫 `build` 方法，JobInfo 才算是建置完成。讀者可參考以下的程式碼作為建置 JobInfo 的範例。

```java
JobInfo jobInfo = new JobInfo.Builder(JOB_TEST
        ,new ComponentName(getPackageName(),NotificationJobService.class.getName()))
        .setPeriodic(AlarmManager.INTERVAL_FIFTEEN_MINUTES)
        .setPersisted(true)
        .build();
jobScheduler.schedule(jobInfo);
```
上述程式碼中的 NotificationJobService 其實是開發者建立的一個繼承 JobService 的類別。下一小節將會介紹 JobService。

<h2 id="2.3"></h2>

## 2.3 JobService

前面我們說明了 JobInfo 如何設定觸發執行的條件，但卻沒有說明條件觸發後要做些什麼。條件觸發後的持行內容就是靠 JobService 來決定。開發者要建立一個繼承 JobService 的類別並且覆寫 `onStartJob` 和 `onStopJob` 兩個方法。顧名思義 `onStartJob` 就是任務觸發條件滿足後要執行的內容，在這之後觸發條件不滿足的話程式會執行 `onStopJob`。執得注意的是  這些工作都主線程執行，所以要執行耗時事件的話，開發者要多加一個執行緒或是用異步處理。另外，繼承 JobService 的類別需要 Permiision 如下

```xml
<service android:name=".NotificationJobService"
         android:permission="android.permission.BIND_JOB_SERVICE" 
```

```java
public class NotificationJobService extends JobService {
    private static final String TAG = NotificationJobService.class.getSimpleName();

    @Override
    public boolean onStartJob(JobParameters params) {
        Log.d(TAG, "onStartJob: ");
        return false;
    }

    @Override
    public boolean onStopJob(JobParameters params) {
        Log.d(TAG, "onStopJob: ");
        return false;
    }
}
```

<h2 id="3"></h2>

# 三、簡易範例

為了更完整理解 JobScheduler 的使用方法以及執行結果，這裡將會展示一個簡易範例的程式碼供讀者測試用，此程式碼執行後不管是 APP 正在執行、移到背景、被滑掉還是手機重新開機後都會在一定的時間間隔調用 JobService 的 `onStartJob`。

activity_main.xml 中簡單的配置一個 Button 作為 JobScheduler 的開關

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.little.testjobscheduler.MainActivity">

    <Button
        android:id="@+id/setting"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="8dp"
        android:layout_marginEnd="8dp"
        android:layout_marginStart="8dp"
        android:layout_marginTop="8dp"
        android:text="Button"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"/>

</android.support.constraint.ConstraintLayout>
```

MainActivity.java 在每個方法裡加入 Log 以便觀察執行時的情形。第一次按按鈕時會 schedule job，下次按按鈕就會 cancel job。

```java
package com.little.testjobscheduler;

import android.app.AlarmManager;
import android.app.job.JobInfo;
import android.app.job.JobScheduler;
import android.content.ComponentName;
import android.content.Context;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;

public class MainActivity extends AppCompatActivity {

    private static final int JOB_TEST = 11;
    private static final String TAG = MainActivity.class.getSimpleName();
    private Button btmSetting;
    private boolean enable;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        enable = false;

        btmSetting = findViewById(R.id.setting);
        btmSetting.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                enable = enable == false ? true : false;
                Log.d(TAG, "onClick: set enable " + enable);
                schedulJob(enable);
            }
        });
    }

    public void schedulJob(boolean enable) {
        JobScheduler jobScheduler = (JobScheduler) getSystemService(JOB_SCHEDULER_SERVICE);

        if (enable) {
            Log.d(TAG, "schedulJob: " + enable);
            JobInfo jobInfo = new JobInfo.Builder(
                    JOB_TEST
                    , new ComponentName(getPackageName()
                    , NotificationJobService.class.getName()))
                    .setPersisted(true)
                    .setPeriodic(AlarmManager.INTERVAL_FIFTEEN_MINUTES)
                    .build();
            jobScheduler.schedule(jobInfo);
        } else {
            Log.d(TAG, "schedulJob: " + enable);
            jobScheduler.cancel(JOB_TEST);
        }
    }
}
```

NotificationJobService.java 簡單加入 Log 來觀察系統何時會調用 onStartJob 和 onStopJob 方法。

```java
package com.little.testjobscheduler;

import android.app.job.JobParameters;
import android.app.job.JobService;
import android.util.Log;

/**
 * Created by sarah on 07/01/2018.
 */

public class NotificationJobService extends JobService {
    private static final String TAG = NotificationJobService.class.getSimpleName();

    @Override
    public boolean onStartJob(JobParameters params) {
        Log.d(TAG, "onStartJob: ");
        return false;
    }

    @Override
    public boolean onStopJob(JobParameters params) {
        Log.d(TAG, "onStopJob: ");
        return false;
    }
}
```

記得要在 Menifest.xml 加入權限

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          package="com.little.testjobscheduler">

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
        <service android:name=".NotificationJobService"
            android:permission="android.permission.BIND_JOB_SERVICE"/>
    </application>

</manifest>
```

<h2 id="4"></h2>

# 參考資料
[官方文章：後台計算的選擇](https://developer.android.com/guide/background/index.html)
[官方文章：後台計算的限制](https://developer.android.com/about/versions/oreo/background.html?#overview)
[官方文章：intelligent Job-Scheduling](https://developer.android.com/topic/performance/scheduling.html)
[官方文章：JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)
[官方文章：JobInfo](https://developer.android.com/reference/android/app/job/JobInfo.html)
[官方網站：Scheduling Repeating Alarms](https://developer.android.com/training/scheduling/alarms.html)


