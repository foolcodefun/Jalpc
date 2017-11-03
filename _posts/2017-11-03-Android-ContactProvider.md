---
layout: post
title:  "Android Contacts Provider"
date: 2017-11-03
desc: "Android Contacts Provider"
keywords: "Android, Contacts Provider"
categories: [Android]
tags: [Android,ContentProvider]
icon: icon-html
---

# 一、前言簡介

在解說 Contacts Provider 前先要對 ContentProvider 有基本的概念。一般來說，App 資料的儲存可透過上週介紹的 SQLite 儲存， SQLite 資料庫的資料只能提供給創建此資料庫的 App 使用，若其他 App 要使用該 App 資料庫中的資料則必須透過 ContentProvider 實現。 ContentProvider 是 android 的四大元件之一 (四大元件有 Activity、Service、BroadcastReceiver 和 ContentProvider)，它能夠將某個 app 的私有資料 (SQLite Databases、Internal Storage 和 Shared Preferences) 分享出去，其他 App 可透過 ContentResolver 對該資料庫進行操作，其操作概念如下圖所示。而 Contacts Provider 指的就是一個可以提供聯絡人應用程式中的聯絡人資訊的方法。

<img src="{{ site.img_path }}/20171103/ContentProvider.png" width="30%" style="min-width:300px;max-width:350px;"/>

# 二、權限

在讀取聯絡人資料前，我們須先知道使用聯絡人資訊需要取得系統權限，而權限又分為一般權限以及危險權限，讀取聯絡人資料屬於危險權限。需要危險權限請求的 app 在 Google Play 下載時便會詢問使用者是否允許該權限，若使用者拒絕權限請求，在執行 App 時便無法正常使用其功能。因此在除了在 App 執行時若發現使用者沒有允許權限則需再向使用者請求權限。此小節參考 [Android 官方網站](https://developer.android.com/training/permissions/requesting.html?hl=zh-cn)。 

## 1. 權限聲明

聯絡人的權限分別是讀取權限以及寫入權限，其聲明須寫在 AndroidManifest.xml 的 uses-permission 元素中，其程式碼分別如下：

```<uses-permission android:name="android.permission.READ_CONTACTS"/>```

```<uses-permission android:name="android.permission.WRITE_CONTACTS"/>```

## 2. 在 App 運作時取得權限

在此步驟則是在 App 執行時運作，在 App 執行時必須先判斷使用者是否允許此危險權限，若無允許則需要請求權限。已讀取聯絡人資訊為範例，檢查是否擁聯絡人權限可用```int permissionCheck = ContextCompat.checkSelfPermission(thisActivity,.permission.READ_CONTACTS);``` 若有權限則方法會回傳```PackageManager.PERMISSION_GRANTED```，若無權限則回傳```PERMISSION_DENIED```。

### (1) 判斷使用者是否取得權限，若無則請求權限

```java
if (ContextCompat.checkSelfPermission(this, Manifest.permission.READ_CONTACTS) != PackageManager.PERMISSION_GRANTED) {
    ActivityCompat.requestPermissions(this, new String[]{Manifest.permission.READ_CONTACTS}, MY_PERMISSIONS_REQUEST_READ_CONTACTS);
}
else{
    //TODO 取得聯絡人資訊的程式碼
}
```

### (2) onRequestPermissionsResult()

當 App 請求權限時，系統會調用 onRequestPermissionsResult() 方法，[Android 官方網站](https://developer.android.com/training/permissions/requesting.html?hl=zh-cn) 提供的範例程式碼如下：
```java
@Override
public void onRequestPermissionsResult(int requestCode,
        String permissions[], int[] grantResults) {
    switch (requestCode) {
        case MY_PERMISSIONS_REQUEST_READ_CONTACTS: {
            // If request is cancelled, the result arrays are empty.
            if (grantResults.length > 0
                && grantResults[0] == PackageManager.PERMISSION_GRANTED) {

                // permission was granted, yay! Do the
                // contacts-related task you need to do.
                //TODO 取得聯絡人資訊的程式碼

            } else {

                // permission denied, boo! Disable the
                // functionality that depends on this permission.
            }
            return;
        }

        // other 'case' lines to check for other
        // permissions this app might request
    }
}

```

# 三、取得聯絡人資訊

取得其他 App 的資料前須先確定該 App 有繼承 ContentProvider 的類別，若有則可以透過 ContentResolver 可調用 `query`、`inser`、`update` 和 `delete` 等方法取得、新增、更新或刪除該 App 資料庫中的資料。在 Contacts Provider 中有三種類型的資料，分別是ContactsContract.Contacts table、ContactsContract.RawContacts table 和 ContactsContract.Data table，分別代表著不同聯絡人、某個聯絡人的資料和某個聯絡人的詳細資料。本範例只需用到 ContactsContract.Contacts 的 CONTENT_URI，若讀者有其他需求 (聯絡人電話或聯絡人地址等等) 可參考 [ Android 官方網站](https://developer.android.com/reference/android/provider/ContactsContract.Contacts.html#CONTENT_URI)。

```java
ContentResolver contactResolver = getContentResolver();
        Cursor cursor = contactResolver.query(
                ContactsContract.Contacts.CONTENT_URI,
                null,
                null,
                null,
                null );
```

# 四、完整程式碼

在模擬器上執行此程式碼前要先在模擬器上新增聯絡資訊。

AndroidManifest.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.sarah.testcontactprovider">

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
    <uses-permission android:name="android.permission.READ_CONTACTS"/>

</manifest>
```

activity_main.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.example.sarah.testcontactprovider.MainActivity">

    <ListView
        android:id="@+id/list_contacts"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

</android.support.constraint.ConstraintLayout>

```

MainActivity.java

```java
package com.example.sarah.testcontactprovider;

import android.Manifest;
import android.content.ContentResolver;
import android.content.pm.PackageManager;
import android.database.Cursor;
import android.os.Bundle;
import android.provider.ContactsContract;
import android.support.v4.app.ActivityCompat;
import android.support.v4.content.ContextCompat;
import android.support.v7.app.AppCompatActivity;
import android.widget.ListView;
import android.widget.SimpleCursorAdapter;
import android.widget.Toast;
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        if (ContextCompat.checkSelfPermission(this, Manifest.permission.READ_CONTACTS) != PackageManager.PERMISSION_GRANTED) {
            ActivityCompat.requestPermissions(this, new String[]{Manifest.permission.READ_CONTACTS}, 1);
        }
        else
            readContactsName();
    }

    @Override
    public void onRequestPermissionsResult(int requestCode,
                                           String permissions[], int[] grantResults) {
        switch (requestCode) {
            case 1: {
                if (grantResults.length > 0
                        && grantResults[0] == PackageManager.PERMISSION_GRANTED) {

                    readContactsName();

                } else {

                    Toast.makeText(this, "必需允許聯絡人權限", Toast.LENGTH_LONG).show();
                }
                return;
            }
            default:
                break;
        }
    }

    private void readContactsName() {
        ContentResolver contactResolver = getContentResolver();
        Cursor cursor = contactResolver.query(
                ContactsContract.Contacts.CONTENT_URI,
                null,
                null,
                null,
                null );
        ListView contactsList = (ListView) findViewById(R.id.list_contacts);
        SimpleCursorAdapter contctsAdapter = new SimpleCursorAdapter(
                this,
                android.R.layout.simple_list_item_1,
                cursor,
                new String[]{ContactsContract.Contacts.DISPLAY_NAME},
                new int[] {android.R.id.text1},
                1);
        contactsList.setAdapter(contctsAdapter);
    }
}

```




