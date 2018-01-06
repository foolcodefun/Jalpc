---
layout: post
title:  "Android AsynTask 異步處理"
date:   2017-09-15
desc: "3 Steps (2 minutes) to Setup Your Personal Website with Jalpc"
keywords: "Android,AsynTask,異步處理"
categories: [Android]
tags: [Android,AsynTask]
icon: icon-html
---

在 Android 程式開發中，若在主線程 (UI Thread) 進行耗時的程式運作會使程式介面卡住或發生 ANR (Aplocation Not Responding)，此時正是使用異步處理的時機。

AsyncTask 是一個用來進行異步處理的抽象類別，適合應用於API串接、網路下載以及讀取資料等等花費大概幾秒鐘時間的事件。在官方網站中提到耗時較長的事件建議使用 Executor、ThreadpoolExecutor 或 FutureTask。

此為 AsyncTask 定義的一部份
```java
public abstrask class AsyncTask<Params, Progress, Result>
```
使用 AsyncTask 需掌握三個泛型以及四個步驟

以下為三泛型

1. Params：傳入 doInBackground 的參數。
2. Progress：傳入 onProgressUpdate 的參數，可在 doInBackground 執行時更新介面。
3. Result：doInBackground 結束後傳入 onPostExecute 的參數。

若不指定泛型的類型，使用如下

```java
private class DownloadTask extends AsyncTask<Void, Void, Void> { ... }
```

以下為AsyncTask的四個步驟

1. onPreExecute：此步驟是在 UI Thread 中，可處理 UI 上的動作。此步驟結束後將調用 doInBackground。
2. doInBackground：此步驟為異步處理，在 onPreExecute 結束後執行並在結束時執行 onPostExecute。
3. onProgressUpdate：此步驟在 UI Thread 中，可在 doInBackground 執行時用來顯示下載進度或時間等等。
4. onPostExecute：doInBadkground 結束後會將結果傳至此步驟，此步驟可將結果顯示於 UI 中。


AsyncTask 的使用須至少覆寫 doInBackground，另外通常會搭配 onPostExecute 來呈現異步處理的結果。AsynTask 通常會調用介面元件，因此以內部類別 (subclass) 的方式宣告。

```java
private class DownloadTask extends AsyncTask<Integer, Integer, String> {
        protected void onPreExecute() {
            text.setText("開始執行異步處理");
        }
        protected String doInBackground(Integer...time){
            //模擬下載資料所佔用的時間
            try {
                for(int i=1;i[0]+1;i++){
                    Thread.sleep(1000);
                    publishProgress(i);//調用onprogressUpdate
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return"共耗時"+time[0]+"秒";
        }
        
        protected void onProgressUpdate(Integer... progress) {
            text.setText("經過"+progress[0].toString()+"秒");
        }
        
        protected void onPostExecute(String result) {
            text.setText("線程結束"+result);
        }
}
```

此類別的執行方法如下
```java
new DownloadTask().execute(3);
```
數字 3 代表三秒，也可輸入其他數字但須符合上段程式碼定義的 Integer...，此參數會傳入 doInBackground

### 完整程式碼如下

activity_main.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.example.sarah.testAsyncTask.MainActivity">

    <TextView
        android:id="@+id/textview"
        android:textSize="20dp"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World!"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="8dp"
        android:layout_marginLeft="8dp"
        android:layout_marginRight="8dp"
        android:layout_marginTop="8dp"
        android:onClick="download"
        android:text="Button"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintVertical_bias="0.626" />

</android.support.constraint.ConstraintLayout>
```

MainActivity.java

```java
package com.example.sarah.testAsyncTask;

import android.os.AsyncTask;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;

public class MainActivity extends AppCompatActivity {

    private Button btdownload;
    private TextView text;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        btdownload = (Button)findViewById(R.id.button);
        text=(TextView)findViewById(R.id.textview);
    }

    public void download(View view){
        btdownload.setClickable(false);
        new DownloadTask().execute(3);
    }

    private class DownloadTask extends AsyncTask<Integer, Integer, String> {
        protected void onPreExecute() {
            text.setText("開始執行異步處理");
        }
        protected String doInBackground(Integer...time){
            //模擬下載資料所佔用的時間
            try {
                for(int i=1;i<time[0]+1;i++){
                    Thread.sleep(1000);
                    publishProgress(i);//調用onprogressUpdate
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "共耗時"+time[0]+"秒";
        }

        protected void onProgressUpdate(Integer... progress) {
            text.setText("經過"+progress[0].toString()+"秒");
        }

        protected void onPostExecute(String result) {
            text.setText("線程結束"+result);
            btdownload.setClickable(true);
        }
    }
}
```