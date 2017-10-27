---
layout: post
title:  "Android SQLite"
date: 2017-10-27
desc: "Android SQLite"
keywords: "Android, SQLite"
categories: [Android]
tags: [Android,SQLite]
icon: icon-html
---

# 一、前言簡介

在 Android App 開發中儲存資料是必要的行為，而資料可透過 Internal Storage、External Storage、SharedPreferences、SQLite 或 Server 等方法儲存。
Internal Storage 的資料儲存於手機內部記憶體，其資料為資料，只有該 app 可使用其儲存的資料，資料會隨著 app 刪除而清除；External Storage 資料儲存於手機外部記憶體，資料為公有的，可透過 USB 將資料傳輸到電腦；SharedPreferences 的資料以鍵值對儲存，當 app 刪除時，資料透過 user sessions 儲存；SQLite 資料儲存於資料庫的資料表中，只有該 app 可存取該資料庫的資料，資料會隨著 app 刪除而清除；Server 資料儲存於遠端的網路主機 ，適用於大量資料的儲存。本篇文章將針對 SQLite 說明。

# 二、SQLite 基本介紹

SQLite 是一個小型開放式原始碼的關聯式資料庫，其設計目地為嵌入式裝置，且裝置不需安裝資料庫伺服器，這意味著此資料庫不用透過網路連線便能使用。在大多的 Android App 中資料儲存是必須的，若需儲存的資料具結構性且只需提供給單機中使用，則可以 SQLite 作為資料儲存的方式。android.database.sqlite 套件提供在 android 上使用 SQLite 的 API，透過該 App 創建的資料庫只能提供資料給該 App 使用。

## 1. SQLite DataType

在使用 MSSQL 或 MYSQL 等大多數的資料庫系統時會發現資料庫需規定每一列資料的資料類型，其資料類型必須是為靜態的，而 SQLite 可以動態的的調整資料庫內值的資料類型，而資料類型的決定與儲存的值本身有關。本小節內容參考 [SQLite 官方文件](https://sqlite.org/datatype3.html)，若需更詳細的資料請參閱上述連結。

### (1) 儲存類別 (storage classes) 與資料類型 (DataTypes)

每一個儲存於資料庫的值都會有一個對應的數據儲存類別，下列為 SQLite 的支援資料儲存類別，一共有五個。

> * NULL：其值是一個 NULL 值。
> * INTEGER：其值是一個有正負之分的整數，儲存空間依據值的大小來決定，可能為1、2、3、4、6 或 8 個位元組。
> * REAL：其值是一個浮點數，其儲存空間為一個八位元組 IEEE 浮點數標準。
> * TEXT：其值是文字字串，儲存方式是資料庫編碼 (UTF-8、UTF-16BE 或 UTF-16LE).
> * BLOB：其值是一個 blob 數據，儲存形式完全按照其輸入的方式。

### (2) SQLite 親和類型 (Type Affinity) 

SQLite 並不會強制規定每一列中的資料類型，它會為輸入的資料推薦其親和的類型。這種運作方式使得 SQLite 系統只需處理五種資料儲存類別，因此系統較為輕便，但是在處理資料比較時可能會出現資料類型不一致的情形。為了處理不同親和類型之間的大小比較，SQLite 規定 NULL 為最小值；INTEGER 和 REAL 大於 NULL 且兩者皆為數值，可互相比較；TEXT 高於 INTEGER 和 REAL；BLOB 具有最高的數值。親和類型跟儲存類別並不相同，親和類型為其系統建議其值可能為的類型，是系統將值存為某一類型的判斷方式。

> * TEXT：具 TEXT 格式親和的資料會以 NULL、TEXT 或 BLOB 方式儲存。當資料輸入系統時，會先轉換成 TEXT 格式再轉乘目標格式。
> * NUMERIC：一個 NUMERIC 親和資料會用到上節中所提到的五種儲存類別，他有三種轉換方式。第一種是當資料為可逆並且在不會丟失資料的情況下，資料將轉成 INTEGER 或 REAL 格式，另外當一個浮點數轉成 INTEGER 並不會有資料丟失時就會以 INTEGER 格式儲存，例如：3.0；第二種是在當資料進行第一種方式轉換失敗後，資料會保持 TEXT 格式；第三種 NULL 或 BLOB，此二類型的資料並不會進行轉換格式。
> * INTEGER：一個 INTEGER 親和類型的資料的資料儲存方式與 NUMERIC 類似，唯一的差別是 [CAST expression](https://sqlite.org/lang_expr.html#castexpr)。
> * REAL：一個 REAL 親和類型的資料的資料儲存方式與 NUMERIC 類似，但會將整數以浮點數表示。
> * BLOB：一個 BLOB 西和類型的資料沒有偏好的類別，也不會強行進行類別轉換。

### (3) 資料輸入類型的 SQLite 親和類型結果

> * 親和結果為 INTEGER：INT、INTEGER、TINYINT、SMALLINT、MEDIUMINT、BIGINT、UNSIGNED BIG INT、INT2 和 INT8	
> * 親和結果為 TEXT：CHARACTER(20)、VARCHAR(255)、VARYING CHARACTER(255)、NCHAR(55)、NATIVE CHARACTER(70)、NVARCHAR(100)、TEXT 和 CLOB	
> * 親和結果為 BLOB：BLOB 和 no datatype specified	
> * 親和結果為 REAL：REAL、DOUBLE、DOUBLE PRECISION 和 FLOAT
> * 親和結果為 NUMERIC：NUMERIC、DECIMAL(10,5)、BOOLEAN、DATE 和 DATETIME

### (4) SQLite Boolean 數據

當一個布林數傳入時，資料會以 INTEGER 1 (true) 或 0 (false) 儲存。

### (5) SQLite 時間數據

當時間數據傳入時會以下列三種形式儲存。

> * TEXT：輸入格式為 "YYYY-MM-DD HH:MM:SS.SSS"
> * REAL：Julian 天數，從格林威治時間公元前 4714 年 11 月 24 日 的正午算起的時間。
> * INTEGER：UNIX 時間，從 1970-01-01 00:00:00 UTC 算起的總秒數。

# 三、基本用法


## 1. SQL Helper

資料庫的創建可透過繼承 SQLiteOpenHelper 抽象類別實現。

```java
public class FinDBHelper extends SQLiteOpenHelper {

    //資料表的內容
    public static abstract class FeedEntry implements BaseColumns {
        public static final String TABLE_NAME = "main.exp";
        public static final String COLUMN_NAME_DATE = "date";
        public static final String COLUMN_NAME_INFO = "info";
        public static final String COLUMN_NAME_COST = "cost";
    }

    public static final int DATABASE_VERSION = 1;
    public static final String DATABASE_NAME = "FeedReader.db";
    private static final String TEXT_TYPE = " TEXT";
    private static final String COMMA_SEP = ",";
    private static final String SQL_CREATE_ENTRIES =
            "CREATE TABLE " + FeedEntry.TABLE_NAME + " (" +
                    FeedEntry._ID + " INTEGER PRIMARY KEY NOT NULL" + COMMA_SEP +
                    FeedEntry.COLUMN_NAME_DATE + TEXT_TYPE + COMMA_SEP +
                    FeedEntry.COLUMN_NAME_INFO + TEXT_TYPE + COMMA_SEP +
                    FeedEntry.COLUMN_NAME_COST + TEXT_TYPE +
                    " )";

    private static final String SQL_DELETE_ENTRIES =
            "DROP TABLE IF EXISTS " + FeedEntry.TABLE_NAME;

    public FinDBHelper(Context context) {
        super(context, DATABASE_NAME, null, DATABASE_VERSION);
    }

    @Override
    public void onCreate(SQLiteDatabase sqLiteDatabase) {
        sqLiteDatabase.execSQL(SQL_CREATE_ENTRIES);
    }

    @Override
    public void onUpgrade(SQLiteDatabase sqLiteDatabase, int i, int i1) {
        sqLiteDatabase.execSQL(SQL_DELETE_ENTRIES);
        onCreate(sqLiteDatabase);
    }
}
```

此類別的使用如下。

```java
FeedReaderDbHelper helper = new FeedReaderDbHelper(Context);
```

## 2. 資料的基本操作

### (1) 新增資料到資料庫中

```java
FinDBHelper helper = new FinDBHelper(this);
SQLiteDatabase db = helper.getWritableDatabase();

// Create a new map of values, where column names are the keys
ContentValues values = new ContentValues();
values.put(FinDBHelper.FeedEntry.COLUMN_NAME_DATE, date.getText().toString());
values.put(FinDBHelper.FeedEntry.COLUMN_NAME_INFO, info.getText().toString());
values.put(FinDBHelper.FeedEntry.COLUMN_NAME_COST, cost.getText().toString());
// Insert the new row,
long newRowId = db.insert(
        FinDBHelper.FeedEntry.TABLE_NAME,
        null,
        values);
```

### (2) 取得資料庫內的資料

```java
FinDBHelper helper = new FinDBHelper(this);
// get all data in the table
Cursor cursor = helper.getReadableDatabase().query(
        FinDBHelper.FeedEntry.TABLE_NAME, null, null, null, null, null, null);
```

# 四、範例

本範例參考[綠豆湯](https://litotom.com/2016/05/09/android%E9%AB%98%E6%95%88%E5%85%A5%E9%96%80-sqlite%E8%B3%87%E6%96%99%E5%BA%AB/)內容和[Android 官方教學](https://developer.android.com/training/basics/data-storage/databases.html)，此範例與差別主要在於[綠豆湯][綠豆湯](https://litotom.com/2016/05/09/android%E9%AB%98%E6%95%88%E5%85%A5%E9%96%80-sqlite%E8%B3%87%E6%96%99%E5%BA%AB/) 使用 SimpleCursorAdapter 而本篇文章對使用 CursorAdapter。

## 1. 程式碼

在 MainActivity.java 中透過 ListView 顯示 SQLite 資料庫中的資料。

```java
package com.example.sarah.testsqlite;

import android.content.Intent;
import android.database.Cursor;
import android.os.Bundle;
import android.support.design.widget.FloatingActionButton;
import android.support.v7.app.AppCompatActivity;
import android.support.v7.widget.Toolbar;
import android.util.Log;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
import android.widget.ListView;

public class MainActivity extends AppCompatActivity {

    ListView finRecordsLV;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar);
        setSupportActionBar(toolbar);

        FloatingActionButton fab = (FloatingActionButton) findViewById(R.id.fab);

        finRecordsLV = (ListView) findViewById(R.id.finRecords);
        FinDBHelper helper = new FinDBHelper(this);
        Cursor cursor = helper.getReadableDatabase().query(
                FinDBHelper.FeedEntry.TABLE_NAME, null, null, null, null, null, null);
        FinCursorAdapter adpater = new FinCursorAdapter(this,cursor,0 );

        finRecordsLV.setAdapter(adpater);

    }

    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        // Inflate the menu; this adds items to the action bar if it is present.
        getMenuInflater().inflate(R.menu.menu_main, menu);
        return true;
    }

    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        // Handle action bar item clicks here. The action bar will
        // automatically handle clicks on the Home/Up button, so long
        // as you specify a parent activity in AndroidManifest.xml.
        int id = item.getItemId();

        //noinspection SimplifiableIfStatement
        if (id == R.id.action_settings) {
            return true;
        }

        return super.onOptionsItemSelected(item);
    }

    public void addItem(View view){
            Intent intent = new Intent();
            intent.setClass(MainActivity.this,AddFinActivity.class);
            startActivity(intent);
            finish();
    }
}
```

在 FinDBHelper.java 繼承 SQLiteOpenHelper，用以創建或刪除資料庫，且將資料表個欄位結構透過 BaseColumns 的實作實現。

```java
package com.example.sarah.testsqlite;

import android.content.Context;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;
import android.provider.BaseColumns;

/**
 * Created by sarah on 2017/10/26.
 */

public class FinDBHelper extends SQLiteOpenHelper {

    public static final int DATABASE_VERSION = 1;
    public static final String DATABASE_NAME = "FeedReader.db";
    private static final String TEXT_TYPE = " TEXT";
    private static final String COMMA_SEP = ",";
    private static final String SQL_CREATE_ENTRIES =
            "CREATE TABLE " + FeedEntry.TABLE_NAME + " (" +
                    FeedEntry._ID + " INTEGER PRIMARY KEY NOT NULL" + COMMA_SEP +
                    FeedEntry.COLUMN_NAME_DATE + TEXT_TYPE + COMMA_SEP +
                    FeedEntry.COLUMN_NAME_INFO + TEXT_TYPE + COMMA_SEP +
                    FeedEntry.COLUMN_NAME_COST + TEXT_TYPE +
                    " )";
    private static final String SQL_DELETE_ENTRIES =
            "DROP TABLE IF EXISTS " + FeedEntry.TABLE_NAME;

    public FinDBHelper(Context context) {
        super(context, DATABASE_NAME, null, DATABASE_VERSION);
    }

    @Override
    public void onCreate(SQLiteDatabase sqLiteDatabase) {
        sqLiteDatabase.execSQL(SQL_CREATE_ENTRIES);
    }

    @Override
    public void onUpgrade(SQLiteDatabase sqLiteDatabase, int i, int i1) {
        sqLiteDatabase.execSQL(SQL_DELETE_ENTRIES);
        onCreate(sqLiteDatabase);
    }

    public void onDowngrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        onUpgrade(db, oldVersion, newVersion);
    }
    
    public static abstract class FeedEntry implements BaseColumns {
        public static final String TABLE_NAME = "main.exp";
        public static final String COLUMN_NAME_DATE = "date";
        public static final String COLUMN_NAME_INFO = "info";
        public static final String COLUMN_NAME_COST = "cost";
    }
}

```

FinCursorAdapter.java 是繼承 CursorAdapter 的類別，是作為 ListView 與 Cursor 資料的轉接器。

```java
package com.example.sarah.testsqlite;

import android.content.Context;
import android.database.Cursor;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.CursorAdapter;
import android.widget.TextView;

/**
 * Created by sarah on 2017/10/26.
 */

public class FinCursorAdapter extends CursorAdapter {

    private LayoutInflater cursorInflater;

    public FinCursorAdapter(Context context, Cursor cursor, int flags) {
        super(context, cursor, flags);

        cursorInflater = LayoutInflater.from(context);
        //cursorInflater = (LayoutInflater) context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);

    }

    @Override
    public View newView(Context context, Cursor cursor, ViewGroup viewGroup) {

        View view = cursorInflater.inflate(R.layout.fin_lv_item, viewGroup, false);
        ViewHolder holder  =   new ViewHolder();

        holder.id=view.findViewById(R.id.item_ID);
        holder.date=view.findViewById(R.id.item_date);
        holder.info=view.findViewById(R.id.item_info);
        holder.cost=view.findViewById(R.id.item_cost);

        view.setTag(holder);

        return view;
    }

    @Override
    public void bindView(View view, Context context, Cursor cursor) {

        ViewHolder holder = (ViewHolder)view.getTag();

        holder.id.setText("test");
        holder.id.setText(cursor.getString(cursor.getColumnIndexOrThrow("_id")));
        holder.date.setText(cursor.getString(cursor.getColumnIndexOrThrow("date")));
        holder.info.setText(cursor.getString(cursor.getColumnIndexOrThrow("info")));
        holder.cost.setText(cursor.getString(cursor.getColumnIndexOrThrow("cost")));

    }

    private static class ViewHolder {
        TextView id;
        TextView date;
        TextView info;
        TextView cost;
    }
}

```

在 AddFinActivity.java 將想儲存的資料插入資料庫中。

```java
package com.example.sarah.testsqlite;

import android.content.ContentValues;
import android.content.Intent;
import android.database.sqlite.SQLiteDatabase;
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.view.View;
import android.widget.TextView;

public class AddFinActivity extends AppCompatActivity {

    FinDBHelper helper;
    TextView date;
    TextView info;
    TextView cost;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_add_fin);

        helper = new FinDBHelper(this);
        date = (TextView)findViewById(R.id.editDate);
        info = (TextView)findViewById(R.id.editInfo);
        cost = (TextView)findViewById(R.id.editCost);

    }

    public void addFinItem(View view){
        SQLiteDatabase db = helper.getWritableDatabase();

        // Create a new map of values, where column names are the keys
        ContentValues values = new ContentValues();
        values.put(FinDBHelper.FeedEntry.COLUMN_NAME_DATE, date.getText().toString());
        values.put(FinDBHelper.FeedEntry.COLUMN_NAME_INFO, info.getText().toString());
        values.put(FinDBHelper.FeedEntry.COLUMN_NAME_COST, cost.getText().toString());
        // Insert the new row, returning the primary key value of the new row
        long newRowId;
        newRowId = db.insert(
                FinDBHelper.FeedEntry.TABLE_NAME,
                null,
                values);

        Intent intent = new Intent();
        intent.setClass(AddFinActivity.this,MainActivity.class);
        startActivity(intent);
        finish();
    }

    public void cancel(View view){
        Intent intent = new Intent();
        intent.setClass(AddFinActivity.this,MainActivity.class);
        startActivity(intent);
        finish();
    }
}

```

activity_main.xml 是主畫面。

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.design.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.example.sarah.testsqlite.MainActivity">

    <include layout="@layout/content_main" />

    <android.support.design.widget.FloatingActionButton
        android:id="@+id/fab"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom|end"
        android:layout_margin="@dimen/fab_margin"
        android:onClick="addItem"
        app:srcCompat="@android:drawable/ic_dialog_email" />

    <android.support.design.widget.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:theme="@style/AppTheme.AppBarOverlay">

        <android.support.v7.widget.Toolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            android:background="?attr/colorPrimary"
            app:popupTheme="@style/AppTheme.PopupOverlay" />

    </android.support.design.widget.AppBarLayout>

</android.support.design.widget.CoordinatorLayout>

```

content_main.xml 中的 ListView 用以顯示資料庫中的所有資料。

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:layout_behavior="@string/appbar_scrolling_view_behavior"
    tools:context="com.example.sarah.testsqlite.MainActivity"
    tools:showIn="@layout/activity_main">

    <ListView
        android:id="@+id/finRecords"
        android:layout_width="0dp"
        android:layout_height="0dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        android:layout_marginLeft="24dp"
        android:layout_marginRight="24dp"
        android:layout_marginTop="24dp"
        android:layout_marginBottom="24dp" />

</android.support.constraint.ConstraintLayout>

```

fin_lv_item.xml 是 ListView 中每一列的資料畫面排版。

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="horizontal" android:layout_width="match_parent"
    android:layout_height="match_parent">

    <TextView
        android:id="@+id/item_ID"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="1"/>

    <TextView
        android:id="@+id/item_date"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="2"/>

    <TextView
        android:id="@+id/item_info"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="1"/>

    <TextView
        android:id="@+id/item_cost"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="1"/>

</LinearLayout>
```

activity_add_fin.xml 是新增資料的畫面。

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.example.sarah.testsqlite.AddFinActivity">

    <TextView
        android:id="@+id/textDate"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="日期"
        android:layout_marginLeft="16dp"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        android:layout_marginTop="24dp"
        android:layout_marginStart="16dp"/>

    <EditText
        android:id="@+id/editDate"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="16dp"
        android:layout_marginRight="16dp"
        android:layout_marginTop="16dp"
        app:layout_constraintHorizontal_bias="0.437"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/textDate" />

    <TextView
        android:id="@+id/textInfo"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="品項"
        android:layout_marginLeft="16dp"
        app:layout_constraintLeft_toLeftOf="parent"
        android:layout_marginTop="24dp"
        android:layout_marginStart="16dp"
        app:layout_constraintTop_toBottomOf="@+id/editDate" />

    <EditText
        android:id="@+id/editInfo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginEnd="16dp"
        android:layout_marginLeft="16dp"
        android:layout_marginRight="16dp"
        android:layout_marginStart="16dp"
        android:layout_marginTop="16dp"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/textInfo" />



    <TextView
        android:id="@+id/textCost"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="金額"
        android:layout_marginTop="24dp"
        app:layout_constraintTop_toBottomOf="@+id/editInfo"
        android:layout_marginLeft="16dp"
        app:layout_constraintLeft_toLeftOf="parent"
        android:layout_marginStart="16dp" />

    <EditText
        android:id="@+id/editCost"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginEnd="16dp"
        android:layout_marginLeft="16dp"
        android:layout_marginRight="16dp"
        android:layout_marginStart="16dp"
        android:layout_marginTop="16dp"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/textCost" />

    <Button
        android:id="@+id/newFinItem"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="新增"
        android:layout_marginTop="24dp"
        app:layout_constraintTop_toBottomOf="@+id/editCost"
        android:layout_marginLeft="16dp"
        android:onClick="addFinItem"
        app:layout_constraintLeft_toLeftOf="parent" />

    <Button
        android:id="@+id/cancel"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="取消"
        app:layout_constraintTop_toTopOf="@+id/newFinItem"
        android:layout_marginTop="0dp"
        android:layout_marginRight="16dp"
        android:onClick="cancel"
        app:layout_constraintRight_toRightOf="parent" />

</android.support.constraint.ConstraintLayout>

```

## 2. 執行結果

<img src="{{ site.img_path }}/20171027/SQLite1.png" width="40%"/>

<img src="{{ site.img_path }}/20171027/SQLite2.png" width="40%"/>

<img src="{{ site.img_path }}/20171027/SQLite3.png" width="40%"/>

<img src="{{ site.img_path }}/20171027/SQLite4.png" width="40%"/>

<img src="{{ site.img_path }}/20171027/SQLite5.png" width="40%"/>

<img src="{{ site.img_path }}/20171027/SQLite6.png" width="40%"/>



下週將會詳細介紹 ListView、CursorAdapter 結合 SQLite 的應用範例。

## 參考資料

[Android 官方教學](https://developer.android.com/training/basics/data-storage/databases.html)

[綠豆湯](https://litotom.com/2016/05/09/android%E9%AB%98%E6%95%88%E5%85%A5%E9%96%80-sqlite%E8%B3%87%E6%96%99%E5%BA%AB/)

