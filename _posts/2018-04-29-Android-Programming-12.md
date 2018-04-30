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

# 參考資料

[Big Nerd Ranch](https://forums.bignerdranch.com/c/android-programming-the-big-nerd-ranch-guide)
