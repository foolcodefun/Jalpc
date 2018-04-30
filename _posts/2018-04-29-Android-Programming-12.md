---
layout: post
title:  "書籍：Android Programming CH12 挑戰 (DatePicker & DialogFragment)"
date:   2018-04-29
desc: "Preference"
keywords: "Android"
categories: [Android]
tags: [Android,The Big Nerd Ranch Guide]
icon: icon-html
---

* ### [一、前言簡介](#1)
* ### [二、Chapter 12 Challenge](#2)
> ### [2.1 挑戰一：More Dialogs](#2.1)
> ### [2.2 挑戰二：A Responsive DialogFragment](#2.2)
* ### [參考資料](#3)

<h2 id="1"></h2>

# 一、前言簡介
這章的內容在介紹了 DatePicker 和 DialogFragment 的結合應用。在此篇文章中提到，將 DatePicker 結合 DialogFragment 對於程式開發有更高的彈性，因為利用 DialogFragment 可以在開發時能更容易達到手機跟平板上不同 UI 呈現。像是在手機上 DatePicker 的呈現最好以 Activity 的方式，而在平板上以 Dialog 的方式呈現 DatePicker 會更好。

此章有兩個挑戰要做。第一個是 More Dialogs，此挑戰要求新增一個名為 TimePickerFragment 的 DialogFragment。第二個挑戰是 A Responsive DialogFragment，此挑戰要求在手機與平板上，DatePicker 呈現不同的樣式。

<h2 id="2"></h2>

# 二、Chapter 12 Challenge

<h2 id="2.1"></h2>

## 2.1 挑戰一：More Dialogs

### 題目

題目中要求讀者新增一個繼承 DialogFragment 的 TimePickerFragment 類別，這個類別可讓使用者透過 TimePicker 更改 Crime 的時間。請讀者新增一個 Button，此 Button 被點擊時會顯示 TimePicker。

### 解題想法

首先，我們知在 Crime 類別有一個 mDate 屬性，在 DatePickerFragment 中會將 Crime 的 mDate 作為 init 的日期，並在 DatePicker 選擇的新的日期在傳回 CrimeFragment 去更改指定 Crime 的 mDate。而要新增的這個 TimePickerFragment 也可利用原本的 mDate 屬性最為 TimePicker 的初始時間，再利用 TargetFragment 的 `onActivityResult` 將資料回傳至 CrimeFragment 用以更新 Crime 的 mDate 時間 。以下為 TimePickerFragmen.java 的程式碼。

TimePickerFragment.java

```java
package com.little.criminalintent;

import android.app.Activity;
import android.app.AlertDialog;
import android.app.Dialog;
import android.content.DialogInterface;
import android.content.Intent;
import android.os.Bundle;
import android.support.annotation.NonNull;
import android.support.v4.app.DialogFragment;
import android.view.LayoutInflater;
import android.view.View;
import android.widget.TimePicker;

import java.util.Calendar;
import java.util.Date;

/**
 * Created by sarah on 12/04/2018.
 */

public class TimePickerFragment extends DialogFragment {

    public static final String EXTRA_TIME = "com.little.criminalIntent.time";
    private static final String ARG_TIME = "time";
    private TimePicker mTimePicker;

    public static TimePickerFragment newInstance(Date date) {
        Bundle args = new Bundle();
        args.putSerializable(ARG_TIME, date);
        TimePickerFragment fragment = new TimePickerFragment();
        fragment.setArguments(args);
        return fragment;
    }

    @NonNull
    @Override
    public Dialog onCreateDialog(Bundle savedInstanceState) {
        View view = LayoutInflater.from(getActivity())
                .inflate(R.layout.dialog_time, null);

        mTimePicker = view.findViewById(R.id.dialog_time_picker);
        Bundle args = getArguments();
        Date date = (Date) args.getSerializable(ARG_TIME);
        Calendar calendar = Calendar.getInstance();
        calendar.setTime(date);
        mTimePicker.setHour(calendar.get(Calendar.HOUR_OF_DAY));
        mTimePicker.setMinute(calendar.get(Calendar.MINUTE));

        return new AlertDialog.Builder(getActivity())
                .setTitle(R.string.time_picker_title)
                .setView(view)
                .setPositiveButton(android.R.string.ok, new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialog, int which) {
                        int hour = mTimePicker.getHour();
                        int minute = mTimePicker.getMinute();

                        Calendar calendar = Calendar.getInstance();
                        calendar.set(Calendar.HOUR_OF_DAY, hour);
                        calendar.set(Calendar.MINUTE, minute);

                        Date date = calendar.getTime();

                        sendResult(date);
                    }
                }).create();
    }

    private void sendResult(Date date) {
        Intent intent = new Intent();
        intent.putExtra(EXTRA_TIME, date);
        getTargetFragment().onActivityResult(getTargetRequestCode(), Activity.RESULT_OK, intent);
    }
}

```

先看到 `newInstance` 方法，此方法中明確的表達在使用此類別時要傳入一個 Date。這個 Date 在 `onCreateDialog` 方法中會用來設定 TimePicker 的時間。設定好時間後，要來撰寫點擊 OK 按扭的監聽事件。此事件的主要功能在於回傳所選的時間給 CrimeFragment，`sendResult` 方法利用 `getTargetFragment` 呼叫 `onActivityResult` 來傳送時間資料。

回到 CrimeFragment 中，要在 layout 中多拉一個 Button 以外，還要將此類別設定為 TimePicker 的 TargetFragemt，最後要在 `onActivityResult` 方法中設定新的 Crime 時間。時間經由 CrimeFragment 呼叫 TimePickerFragment 的 `newInstance` 方法傳遞給 TimePickerFragment，TimePickerFragment 再利用 `getTargetFragment` 呼叫 `onActivityResult` 回傳給 CrimeFragment。

CrimeFragment.java 更改過程式碼的部分
```java
package com.little.criminalintent;

import android.app.Activity;
import android.app.DatePickerDialog;
import android.app.Dialog;
import android.content.Intent;
import android.os.Bundle;
import android.support.annotation.NonNull;
import android.support.annotation.Nullable;
import android.support.v4.app.DialogFragment;
import android.support.v4.app.Fragment;
import android.support.v4.app.FragmentManager;
import android.text.Editable;
import android.text.TextWatcher;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.Button;
import android.widget.CheckBox;
import android.widget.CompoundButton;
import android.widget.EditText;

import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Calendar;
import java.util.Date;
import java.util.UUID;

import static android.widget.CompoundButton.OnCheckedChangeListener;
import static android.widget.CompoundButton.OnClickListener;

public class CrimeFragment extends Fragment {
    private static final String ARG_CRIME_ID = "crime_id";
    public static final String DIALOG_DATE = "DialogDate";
    private static final String DIALOG_TIME = "DialogTime";
    private static final int REQUEST_DATE = 0;
    private static final int REQUEST_TIME = 1;
    private String datePattern = "EEE MMM dd YYYY";
    private String timePattern = "HH:mm";
    private Crime mCrime;
    private EditText mTitleField;
    private Button mDateButton;
    private Button mTimeButton;
    private CheckBox mSolvedCheckBox;

    ......

    @Nullable
    @Override
    public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.fragment_crime, container, false);

        ......
        mDateButton.setOnClickListener(new OnClickListener() {
            @Override
            public void onClick(View v) {
                DialogFragment dialog = DatePickerFragment.newInstance(mCrime.getDate());
                showDialog(dialog, REQUEST_DATE, DIALOG_DATE);
            }
        });

        mTimeButton = view.findViewById(R.id.crime_time);
        updateDate(timePattern, mTimeButton);
        mTimeButton.setOnClickListener(new OnClickListener() {
            @Override
            public void onClick(View v) {
                DialogFragment dialog = TimePickerFragment.newInstance(mCrime.getDate());
                showDialog(dialog, REQUEST_TIME, DIALOG_TIME);
            }
        });

        ......
    }

    @Override
    public void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (resultCode != Activity.RESULT_OK)
            return;

        switch (requestCode) {
            case REQUEST_DATE: {
                Calendar pickedDate = getCalendar(data, DatePickerFragment.EXTRA_DATE);
                setCrimeDate(pickedDate);
                updateDate(datePattern, mDateButton);
                break;
            }

            case REQUEST_TIME: {
                Calendar pickedDate = getCalendar(data, TimePickerFragment.EXTRA_TIME);
                setCrimeTime(pickedDate);
                updateDate(timePattern, mTimeButton);
                break;
            }
        }
    }

    private void setCrimeTime(Calendar pickedDate) {
        Calendar crimeDate = Calendar.getInstance();
        crimeDate.setTime(mCrime.getDate());
        crimeDate.set(Calendar.HOUR_OF_DAY, pickedDate.get(Calendar.HOUR_OF_DAY));
        crimeDate.set(Calendar.MINUTE, pickedDate.get(Calendar.MINUTE));

        mCrime.setDate(crimeDate.getTime());
    }

    private void setCrimeDate(Calendar pickedDate) {
        Calendar crimeDate = Calendar.getInstance();
        crimeDate.setTime(mCrime.getDate());
        crimeDate.set(Calendar.YEAR, pickedDate.get(Calendar.YEAR));
        crimeDate.set(Calendar.MONTH, pickedDate.get(Calendar.MONTH));
        crimeDate.set(Calendar.DAY_OF_MONTH, pickedDate.get(Calendar.DAY_OF_MONTH));

        mCrime.setDate(crimeDate.getTime());
    }

    @NonNull
    private Calendar getCalendar(Intent data, String EXTRA) {
        Date date = (Date) data.getSerializableExtra(EXTRA);
        Calendar calendar = Calendar.getInstance();
        calendar.setTime(date);
        return calendar;
    }

    private void showDialog(DialogFragment dialog, int requestCode, String tag) {
        FragmentManager manager = getFragmentManager();
        dialog.setTargetFragment(CrimeFragment.this, requestCode);
        dialog.show(manager, tag);
    }

    private void updateDate(String pattern, Button button) {
        DateFormat format = new SimpleDateFormat(pattern);
        button.setText(format.format(mCrime.getDate()));
    }
}

```

這樣就完成此挑戰的需求啦！

<h2 id="2.2"></h2>

## 2.2 挑戰二：A Responsive DialogFragment

### 題目：

此題要求讀者在手機上以 Activity 的方式呈現 DatePicker，在平板上以 Dialog 的方式呈現 DatePicker。整個過程分成三步驟：第一步驟，以 `onCreateView` 取代 `onCreateDialog` 方法，如此一來 DatePicker 可以 Dialog 呈現，或是可以將它整合到 Activity。第二步驟，新增一個 SingleFragmentActivity 的子類別，此類別會運用 DatePickerFragment 呈現 DatePicker，在這個做法中得利用 `startActivityForResult` 方法傳送資料，並利用 `sendResult` 將資料傳回。最後一個步驟是將 DatePicker 以 Activity 呈現於手機上，而 DatePicker 在平板上則以 Dialog 呈現。

### 解題想法：
在這個題目中，先要改寫 DatePickerFragment 的 `onCreateView` 方法，並法 `onCreateDialog` 方法刪掉，如此一來，開發者便能在不同的情況下決定要使用 Dialog 還是 Activity 來呈現 DatePicker。而在這個步驟要新增一個 OK 按鈕，還有此按鈕的點擊事件。若是在手機裝置上，也就是 TargetFragment 為 null 時，回傳 Date 資料用 Activity 的 `sendResult` 方法來達成；若是在平板裝置，也就是 TargetFragment 不為 null 時，用 `targetFragment.onActivityResult` 方法回傳資料。下面是完成此挑戰的程式碼，首先先看到 DatePickerActivity。

DatePickerActivity.java

```java
package com.little.criminalintent;

import android.content.Context;
import android.content.Intent;
import android.support.v4.app.Fragment;

import java.util.Date;

public class DatePickerActivity extends SingleFragmentActivity {
    private static final String EXTRA_CRIME_DATE = "com.little.criminalIntent.crime_date";

    public static Intent newIntent(Context packageContext, Date date) {
        Intent intent = new Intent(packageContext, DatePickerActivity.class);
        intent.putExtra(EXTRA_CRIME_DATE, date);
        return intent;
    }

    @Override
    protected Fragment createFragment() {
        Intent intent = getIntent();
        Date date = (Date) intent.getSerializableExtra(EXTRA_CRIME_DATE);
        return DatePickerFragment.newInstance(date);
    }
}
```

DatePickerActivity 中的靜態方法 `newIntent` 可將需要的日其資料傳入，並在 new DatePickerFregmant 時，將日期資料傳給 DatePickerFragment。接著看到 DatePickerFragment 與 dialog_date，DatePickerFragment 會先將 Bundle 內的 Date 資料取出，再對 dialog_date 產生相對應的 View，並且在點擊事件中回傳新的 Date 資料，最後結束 Activity 或是 Dialog。

DatePickerFragment.java

```java
package com.little.criminalintent;

import android.app.Activity;
import android.content.Intent;
import android.os.Bundle;
import android.support.annotation.Nullable;
import android.support.v4.app.DialogFragment;
import android.support.v4.app.Fragment;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.Button;
import android.widget.DatePicker;

import java.util.Calendar;
import java.util.Date;

public class DatePickerFragment extends DialogFragment {

    public static final String ARG_DATE = "date";
    public static final String EXTRA_DATE = "com.little.criminalIntent";
    private DatePicker mDatePicker;

    public static DatePickerFragment newInstance(Date date) {
        Bundle args = new Bundle();
        args.putSerializable(ARG_DATE, date);
        DatePickerFragment fragment = new DatePickerFragment();
        fragment.setArguments(args);
        return fragment;
    }

    @Nullable
    @Override
    public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {

        Date date = (Date) getArguments().getSerializable(ARG_DATE);
        final Calendar calendar = Calendar.getInstance();
        calendar.setTime(date);
        int year = calendar.get(Calendar.YEAR);
        int month = calendar.get(Calendar.MONTH);
        int day = calendar.get(Calendar.DAY_OF_MONTH);

        final View view = inflater.inflate(R.layout.dialog_date, null);

        mDatePicker = view.findViewById(R.id.dialog_date_picker);
        mDatePicker.init(year, month, day, null);

        Button positiveButton = view.findViewById(R.id.positive);
        positiveButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                int year = mDatePicker.getYear();
                int month = mDatePicker.getMonth();
                int dayOfMonth = mDatePicker.getDayOfMonth();

                calendar.set(year, month, dayOfMonth);
                Date date = calendar.getTime();
                sendResult(Activity.RESULT_OK, date);
            }
        });
        return view;
    }

    private void sendResult(int resultCode, Date date) {
        Intent intent = new Intent();
        intent.putExtra(EXTRA_DATE, date);

        Fragment targetFragment = getTargetFragment();
        if (targetFragment == null) {
            getActivity().setResult(Activity.RESULT_OK, intent);
            getActivity().finish();
            return;
        }
        targetFragment.onActivityResult(getTargetRequestCode(), resultCode, intent);
        getDialog().dismiss();
    }
}
```

上述程式碼中的 `sendResult(int resultCode, Date date)` 會判斷是否有沒有 targetFragment，沒有的話就代表著此 APP 正在 phone 裝置上運行，有的話就代表是在 tablet 裝置上運行，原因在之後會提到的 CrimeFragment 程式碼中。在 CrimeFragment 程式碼中，在 phone 上運行的 APP 會直接調用 `startActivityForResult`，並不會設定 TargetFragnemt；而在 tablet 上運行的 APP 則會設定好 DataPickerFragment 的 TargetFragment，並且以 Dialog 的形式 show 出 DataPicker。

res/layout/dialog_date.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <DatePicker
        android:id="@+id/dialog_date_picker"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="24dp"
        android:layout_marginEnd="8dp"
        android:layout_marginStart="8dp"
        android:layout_marginTop="24dp"
        android:calendarViewShown="false"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent">

    </DatePicker>

    <Button
        android:id="@+id/positive"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="8dp"
        android:background="@android:color/transparent"
        android:text="@string/ok"
        app:layout_constraintBottom_toBottomOf="@+id/dialog_date_picker"
        app:layout_constraintEnd_toEndOf="@+id/dialog_date_picker"/>
</android.support.constraint.ConstraintLayout>
```

以下為區分 APP 所在的裝置是 phone 還是 tablet 的程式碼。


res/values/refs.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <item name="date_picker" type="string">@string/phone</item>
</resources>
```

res/values-sw600dp/refs.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <item name="date_picker" type="string">@string/tablet</item>
</resources>
```

res/values/strings.xml

```xml
<resources>
    ......
    <string name="phone">phone</string>
    <string name="tablet">tablet</string>
</resources>
xml
```

CrimeFragment.java

```java
package com.little.criminalintent;

import android.app.Activity;
import android.content.Intent;
import android.os.Bundle;
import android.support.annotation.Nullable;
import android.support.v4.app.Fragment;
import android.support.v4.app.FragmentManager;
import android.text.Editable;
import android.text.TextWatcher;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.Button;
import android.widget.CheckBox;
import android.widget.CompoundButton;
import android.widget.EditText;

import java.util.Date;
import java.util.UUID;

import static android.widget.CompoundButton.OnCheckedChangeListener;
import static android.widget.CompoundButton.OnClickListener;

public class CrimeFragment extends Fragment {
    ......
    @Nullable
    @Override
    public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        ......
        mDateButton.setOnClickListener(new OnClickListener() {
            @Override
            public void onClick(View v) {
                String date_picker_type = getString(R.string.date_picker);

                if (date_picker_type.equals(getString(R.string.phone))) {
                    Intent intent = DatePickerActivity.newIntent(getActivity(), mCrime.getDate());
                    startActivityForResult(intent, REQUEST_DATE);
                } else {
                    FragmentManager manager = getFragmentManager();
                    DatePickerFragment dialog = DatePickerFragment.newInstance(mCrime.getDate());
                    dialog.setTargetFragment(CrimeFragment.this, REQUEST_DATE);
                    dialog.show(manager, DIALOG_DATE);
                }
            }
        });

        ......
    }

    ......
}
```

在不同的螢幕大小上，可對同一個資源設定不同的值，res/values/refs.xml 和 res/values-sw600dp/refs.xml 中的 date_picker 設定了不同的 string，分別是 phone 和 tablet。所以當點開 DatePicker 的點擊事件被觸發時，可以此來判斷 APP 所在的裝置是 phone 還是 tablet。至於選擇 smallest width 600 dp 為 phone 跟 tablet 的區分是根據[官方文章：Support different screen sizes](https://developer.android.com/training/multiscreen/screensizes)。

如此一來，此挑戰也完成啦！

# 參考資料

[Big Nerd Ranch](https://forums.bignerdranch.com/c/android-programming-the-big-nerd-ranch-guide)

[官方文章：Support different screen sizes](https://developer.android.com/training/multiscreen/screensizes)
