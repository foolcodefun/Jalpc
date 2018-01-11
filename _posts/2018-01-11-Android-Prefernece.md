---
layout: post
title:  "Android 偏好設定 (Preference)"
date:   2018-01-11
desc: "Preference"
keywords: "Android,Preference"
categories: [Android]
tags: [Android,Preference]
icon: icon-html
---

* ### [一、前言簡介](#1)
* ### [二、PreferenceFragment](#2)
> ### [2.1 設定畫面：XML 與 PreferenceScreen](#2.1)
> * ### [(1) 各式 Preference 清單](#2.1.1)
> * ### [(2) 設定群組](#2.1.2)
>
> ### [2.2 取得設定改變後的值：OnSharedPreferenceChangeListener](#2.2)
* ### [三、簡易範例](#3)
* ### [參考資料](#4)

<h2 id="1"></h2>

# 一、前言簡介

使用者操作一個 APP 時可能會需要設定一些功能，像是是否要開啟通知功能或與雲端同步的頻率等等。這些設定 (Settings) 功能是透過 [Android Preference API](https://developer.android.com/reference/android/preference/Preference.html?hl=zh-tw) 實現的，本篇文章將會介紹 Preference 的基本使用方法，下圖是設定頁面的樣子。

<img src="{{ site.img_path }}/20180111/setting.png" width="100%" style="max-width:300px;"/>

設定須通過 Activity 或 Fragment 顯示。在 Android 3.0 之前 (API10 以期以前) 應該用 PreferenceActivity；Android 3.0 之後可以用 Activity 與 PreferenceFragment 的搭配來完成。Android 3.0 之後若是有多個設定群組，需要用大畫面顯示兩個版面則可用 PreferenceActivity 來完成。

<h2 id="2"></h2>

# 二、設定與 PreferenceFragment

PreferenceFragment 可用來管理設定頁面以及設定的監聽事件，此章節以 Activity 搭配 PreferenceFragment 說明設定偏好的基本使用方式。在這裡要先請各位讀者建立一個專案、新增一個名為 SettingActivity.java 的 Activity 和繼承 PreferenceFragment 的 SettingFragment.java。

在 SettingFragment.java 中覆寫 onCreate 方法並用 `addPreferencesFromResource` 方法將 XML 中的 PreferenceScreen 資源加入。

```java
public class SettingsFragment extends PreferenceFragment {

    @Override
    public void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        addPreferencesFromResource(R.xml.preferences);
    }
}
```

將 Fragment 加入 Activity 的方法如下，可以參考 [Android Fragment 入門](https://foolcodefun.github.io/blog/android/2017/12/15/Android-Fragment.html)。

```java
public class SettingsActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_settings);

        FragmentManager fm = getFragmentManager();
        Fragment fragment = fm.findFragmentById(R.id.frame_settings_container);

        if(fragment==null){
            fragment = new SettingsFragment();
            fm.beginTransaction()
                    .add(R.id.frame_settings_container,fragment)
                    .commit();
        }
    }
}
```

<h2 id="2.1"></h2>

## 2.1 設定畫面：XML 與 PreferenceScreen

設定片好要面的設計會儲存在 res/xml 資料夾內，傳統命名為 preferences.xml。以下為程式範例及畫面。從程式碼中可以看到 root 必須是 PreferenceScreen，在  PreferenceScreen 元素內的 Preference 子類別分別有 CheckBoxPreference、ListPreference 和 SwitchPreference。

```xml
<?xml version="1.0" encoding="utf-8"?>
<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android">

    <CheckBoxPreference
        android:defaultValue="false"
        android:key="check_box_preference_1"
        android:title="Check box preference"/>
    <ListPreference
        android:defaultValue="1"
        android:entries="@array/time_display"
        android:entryValues="@array/time"
        android:key="list_preference_1"
        android:title="List preference"/>
    <SwitchPreference
        android:defaultValue="false"
        android:key="switch_preference_1"
        android:title="Switch preference"/>
</PreferenceScreen>
```

<img src="{{ site.img_path }}/20180111/settingExaple.png" width="100%" style="max-width:300px;"/>

<h2 id="2.1.1"></h2>

### (1) 各式 Preference 清單

在設定頁面可用的 Preference 有 [CheckBoxPreference](https://developer.android.com/reference/android/preference/CheckBoxPreference.html)、[EditTextPreference](https://developer.android.com/reference/android/preference/EditTextPreference.html)、[ListPreference](https://developer.android.com/reference/android/preference/ListPreference.html)、[MultiSelectListPreference](https://developer.android.com/reference/android/preference/MultiSelectListPreference.html)、[RingtonePreference](https://developer.android.com/reference/android/preference/RingtonePreference.html) 和 [SwitchPreference](https://developer.android.com/reference/android/preference/SwitchPreference.html)，如下圖所示。有興趣知道各個類別使用方法的話，可以點選上述連結，查看官方文件。Preference 類別有三個必須設定的元素，分別是 `android:title`、`android:key` 和 `android:defaultValue`。

<img src="{{ site.img_path }}/20180111/Preference.png" width="100%" style="max-width:300px;"/>

<h2 id="2.1.2"></h2>

### (2) 設定群組

設定清單的項目太多會造成使用者混淆，這個時後可以善用群組。[官方網站](https://developer.android.com/guide/topics/ui/settings.html?hl=zh-tw#Groups)介紹中設定群組的方法有兩種，分別是使用標題和使用子畫面。以下兩個範例皆是從官方網站取得。

> 設定標題

```xml
<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android">
    <PreferenceCategory
        android:title="@string/pref_sms_storage_title"
        android:key="pref_key_storage_settings">
        <CheckBoxPreference
            android:key="pref_key_auto_delete"
            android:summary="@string/pref_summary_auto_delete"
            android:title="@string/pref_title_auto_delete"
            android:defaultValue="false"... />
        <Preference
            android:key="pref_key_sms_delete_limit"
            android:dependency="pref_key_auto_delete"
            android:summary="@string/pref_summary_delete_limit"
            android:title="@string/pref_title_sms_delete"... />
        <Preference
            android:key="pref_key_mms_delete_limit"
            android:dependency="pref_key_auto_delete"
            android:summary="@string/pref_summary_delete_limit"
            android:title="@string/pref_title_mms_delete" ... />
    </PreferenceCategory>
    ...
</PreferenceScreen>
```

下圖中以的 1 以 [PreferenceCategory](https://developer.android.com/reference/android/preference/PreferenceCategory.html) 分類，2 以 android:title 來設定標題。 

<img src="{{ site.img_path }}/20180111/category.png" width="100%" style="max-width:300px;"/>

> 使用子畫面

在 root 元素中再放入一個 PreferenceScreen 作為群組

```xml
<PreferenceScreen  xmlns:android="http://schemas.android.com/apk/res/android">
    <!-- opens a subscreen of settings -->
    <PreferenceScreen
        android:key="button_voicemail_category_key"
        android:title="@string/voicemail"
        android:persistent="false">
        <ListPreference
            android:key="button_voicemail_provider_key"
            android:title="@string/voicemail_provider" ... />
        <!-- opens another nested subscreen -->
        <PreferenceScreen
            android:key="button_voicemail_setting_key"
            android:title="@string/voicemail_settings"
            android:persistent="false">
            ...
        </PreferenceScreen>
        <RingtonePreference
            android:key="button_voicemail_ringtone_key"
            android:title="@string/voicemail_ringtone_title"
            android:ringtoneType="notification" ... />
        ...
    </PreferenceScreen>
    ...
</PreferenceScreen>
```

<img src="{{ site.img_path }}/20180111/PreferenceScreen.png" width="100%" style="max-width:300px;"/>

<h2 id="2.2"></h2>

### 2.2 取得設定改變後的值：OnSharedPreferenceChangeListener

當使用這道設定頁面更動了偏好值，程式會透過 OnSharedPreferenceChangeListener 取得改動的 SharedPreference 和 key 值，這個介面的使用需進行註冊與姊註冊的動作。透過 key 可以知道哪一個 Preference 子類別被改動，透過 SharedPreference 則可取得改動得值。

```java
package com.little.testpreference;

import android.content.SharedPreferences;
import android.os.Bundle;
import android.preference.PreferenceFragment;
import android.preference.PreferenceManager;
import android.support.annotation.Nullable;
import android.util.Log;

/**
 * Created by sarah on 10/01/2018.
 */

public class SettingsFragment extends PreferenceFragment implements SharedPreferences.OnSharedPreferenceChangeListener{

    private static final String TAG = SettingsFragment.class.getSimpleName();

    @Override
    public void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        addPreferencesFromResource(R.xml.preferences);
    }

    @Override
    public void onSharedPreferenceChanged(SharedPreferences sharedPreferences, String key) {
        switch(key){
            case "check_box_preference_1":
                boolean check_enable = sharedPreferences.getBoolean(key,false);
                Log.d(TAG, "onSharedPreferenceChanged: "+check_enable );
                break;
            case "list_preference_1":
                String time = sharedPreferences.getString(key,"07:00");
                Log.d(TAG, "onSharedPreferenceChanged: "+time);
                break;
            case "switch_preference_1":
                boolean switch_eable = sharedPreferences.getBoolean(key,false);
                Log.d(TAG, "onSharedPreferenceChanged: "+switch_eable);
                break;
            default:
                break;
        }
    }

    @Override
    public void onResume() {
        super.onResume();
        PreferenceManager.getDefaultSharedPreferences(getActivity())
                .registerOnSharedPreferenceChangeListener(this);
    }

    @Override
    public void onPause() {
        super.onPause();
        PreferenceManager.getDefaultSharedPreferences(getActivity())
                .unregisterOnSharedPreferenceChangeListener(this);
    }
}
```

<h2 id="3"></h2>

# 三、簡易範例

MainActivity.java

```java
package com.little.testpreference;

import android.content.Intent;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.Menu;
import android.view.MenuItem;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }

    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        getMenuInflater().inflate(R.menu.menu,menu);
        return true;
    }

    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()){
            case R.id.settings:
                Intent intent = new Intent(this,SettingsActivity.class);
                startActivity(intent);
                break;
            default:
                break;
        }
        return super.onOptionsItemSelected(item);
    }
}
```

layout/activity_main.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.little.testpreference.MainActivity">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World!"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent"/>

</android.support.constraint.ConstraintLayout>
```

menu/menu.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:app="http://schemas.android.com/apk/res-auto"
      xmlns:android="http://schemas.android.com/apk/res/android">

    <item
        android:id="@+id/settings"
        android:title="Settings"/>
</menu>
```

settingsActivity.java

```java
package com.little.testpreference;

import android.app.Fragment;
import android.app.FragmentManager;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;

public class SettingsActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_settings);

        FragmentManager fm = getFragmentManager();
        Fragment fragment = fm.findFragmentById(R.id.frame_settings_container);

        if(fragment==null){
            fragment = new SettingsFragment();
            fm.beginTransaction()
                    .add(R.id.frame_settings_container,fragment)
                    .commit();
        }
    }
}
```

layout/activity_settings.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/frame_settings_container"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.little.testpreference.SettingsActivity">

</FrameLayout>
```

settingsFragment.java

```java
package com.little.testpreference;

import android.content.SharedPreferences;
import android.os.Bundle;
import android.preference.PreferenceFragment;
import android.preference.PreferenceManager;
import android.support.annotation.Nullable;
import android.util.Log;

/**
 * Created by sarah on 10/01/2018.
 */

public class SettingsFragment extends PreferenceFragment implements SharedPreferences.OnSharedPreferenceChangeListener {

    private static final String TAG = SettingsFragment.class.getSimpleName();


    @Override
    public void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        addPreferencesFromResource(R.xml.preferences);
    }

    @Override
    public void onSharedPreferenceChanged(SharedPreferences sharedPreferences, String key) {
        boolean eable;
        switch (key) {
            case "check_box_preference_1":
                eable = sharedPreferences.getBoolean(key, false);
                Log.d(TAG, "onSharedPreferenceChanged: " + eable);
                break;
            case "list_preference_1":
                String time = sharedPreferences.getString(key, "07:00");
                Log.d(TAG, "onSharedPreferenceChanged: " + time);
                break;
            case "switch_preference_1":
                eable = sharedPreferences.getBoolean(key, false);
                Log.d(TAG, "onSharedPreferenceChanged: " + eable);
                break;
            case "switch_preference_2":
                eable = sharedPreferences.getBoolean(key, false);
                Log.d(TAG, "onSharedPreferenceChanged: " + eable);
                break;
            case "switch_preference_3":
                eable = sharedPreferences.getBoolean(key, false);
                Log.d(TAG, "onSharedPreferenceChanged: " + eable);
                break;
            case "switch_preference_4":
                eable = sharedPreferences.getBoolean(key, false);
                Log.d(TAG, "onSharedPreferenceChanged: " + eable);
                break;
            default:
                break;
        }
    }

    @Override
    public void onResume() {
        super.onResume();
        PreferenceManager.getDefaultSharedPreferences(getActivity())
                .registerOnSharedPreferenceChangeListener(this);
    }

    @Override
    public void onPause() {
        super.onPause();
        PreferenceManager.getDefaultSharedPreferences(getActivity())
                .unregisterOnSharedPreferenceChangeListener(this);
    }
}

```

xml/preferences.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android">
    <PreferenceScreen
        android:key="button_voicemail_category_key"
        android:persistent="false"
        android:title="PreferenceScreen">>
        <CheckBoxPreference
            android:defaultValue="false"
            android:key="check_box_preference_1"
            android:title="Check box preference"/>
        <ListPreference
            android:defaultValue="1"
            android:entries="@array/time_display"
            android:entryValues="@array/time"
            android:key="list_preference_1"
            android:title="List preference"/>
        <SwitchPreference
            android:defaultValue="false"
            android:key="switch_preference_1"
            android:title="Switch preference"/>

    </PreferenceScreen>
    <PreferenceCategory
        android:title="分類標題">
        <SwitchPreference
            android:defaultValue="false"
            android:key="switch_preference_２"
            android:title="Switch preference"/>
        <SwitchPreference
            android:defaultValue="false"
            android:key="switch_preference_３"
            android:title="Switch preference"/>
        <SwitchPreference
            android:defaultValue="false"
            android:key="switch_preference_４"
            android:title="Switch preference"/>

    </PreferenceCategory>

</PreferenceScreen>
```

values/arrays.xml

```java
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string-array name="time">
        <item>07:00</item>
        <item>08:00</item>
        <item>20:00</item>
        <item>21:00</item>
    </string-array>
    <string-array name="time_display">
        <item>07:00 AM</item>
        <item>08:00 AM</item>
        <item>08:00 PM</item>
        <item>09:00 PM</item>
    </string-array>
</resources>
```

# 參考資料

[官方網站：設定](https://developer.android.com/guide/topics/ui/settings.html?hl=zh-tw)