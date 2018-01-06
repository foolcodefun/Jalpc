---
layout: post
title:  "Android ListView 基本介紹以及客製化"
date: 2017-09-29
desc: "ListView 的基本用法以及客製化"
keywords: "Android, ListView, Custom, Adapter, ArrayAdapter,BaseAdapter"
categories: [Android]
tags: [Android,ListView]
icon: icon-html
---

# 一、前言
在 Android app 開發中常會需要展示多個資料在清單上，像是通訊軟體的聊天記錄
、記事本的清單或是或是 email 信箱每一封郵件的展示，如下圖所示。ListView 是一個可以上下滑動 ( vertically-scrollable ) 的清單列，可以做出各式各樣的清單，此篇文章將會介紹 ListView 的基本使用以及客製化的方法。

<img src="{{ site.img_path }}/20170929/基本ListView.png" width="30%">

## Adapter 簡介
ListView 是 AdatpterView 的次類別，作為資料的容器需要一個能為之處理資料以及畫面的 [ListAdapter](https://developer.android.com/reference/android/widget/ListAdapter.html)。在 [Android Developers](https://developer.android.com/reference/android/widget/Adapter.html) 介紹中可知 Adatper 是作為 [AdapterView](https://developer.android.com/reference/android/widget/AdapterView.html) 與資料間的橋樑，它能為每一筆資料製作一個畫面 ( View )。而常見 [ListAdapter](https://developer.android.com/reference/android/widget/ListAdapter.html) 的次類別有 ArrayAdapter、CursorAdapter、SimpleAdapter、SimpleCursorAdapter 和 BaseAdapter，其繼承關係如下圖所示。

<img src="{{ site.img_path }}/20170929/AdSt.png" width="30%">

### 常見 ListAdapter 次類別的使用時機

>1. ArrayAdapter：輸入資料為陣列或是 List 集合。
>2. SimpleAdapter：輸入資料為表格時 ( List < ? extends Map < String , ? > > )。
>3. SimpleCursorAdapter：輸入資料是由資料庫 ( SQLite ) 查詢的Cursor時。
>4. BaseAdapter：需完整的客製化 ( custom )。

看到這裡我們可以發現 ListView 和資料源之間使用 ListAdapter 進行資料轉接的行為正是設計模式 ( Design Pattern ) 中的轉接器模式 ( Adapter Pattern )，也稱作適配器模式。轉接器模式可使不相容的介面互相合作，而 ListAdapter 的目標就是將資料員轉成 ListView 的形式且串在一個 ListView 需要的畫面 ( View )。

<img src="{{ site.img_path }}/20170929/AdpLVD.png" width="30%">

---------------------------------------

# 二、基本使用方法介紹

## 將資料放入 Adapter
以 ArrayAdapter 為例，利用字串陣列作為資料來源的的宣告如下
```java
ArrayAdapter adapter = new ArrayAdapter(this,
            android.R.layout.simple_list_item_1,str);
```
引用的參數如下
1. 應用程式 Context context ( 程式碼中的 this )
2. 版面配置 int resource ( 程式碼中的 android.R.layout.simple_list_item_1 )
3. 陣列 T[] objects ( 程式碼中的 str )

ArrayAdapter 還有其他建構子，詳細資料請參考[官方網站](https://developer.android.com/reference/android/widget/ArrayAdapter.html?hl=zh-tw)

接著呼叫 `setAdatper(ListAdatper adapter)`
```java
ListView listView =(ListView)findViewById(R.id.listview1);
listView.setAdapter(adapter);
```

## 點擊事件處理

回應 AdapterView 所點擊的項目方式是實作
```java
private AdapterView.OnItemClickListener onItemClickListener=new AdapterView.OnItemClickListener(){
    @Override
    public void onItemClick(AdapterView parent, View v, int position, long id) {
        // Do something in response to the click
    }
};
```

接著呼叫 `setOnItemClickListener(AdapterView.OnItemClickListener onItemClickListener)`
```java
listView.setOnItemClickListener(onItemClickListener);
```

---------------------------------------

# 三、範例
## ListView 的基本使用方法：以 ArrayAdapter 為範例 

ArrayAdapter 的資料元陣列，此類別再創造一個畫面 ( View ) 時會對陣列的每一個元素呼叫 toString()，並將其結果放置到 TextView 中。

以下為 activity_main.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.example.sarah.testlistview.MainActivity">

    <ListView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:id="@+id/listview1"
        />
</android.support.constraint.ConstraintLayout>
```

以下為 MainActivity.java

```java
package com.example.sarah.testlistview;

import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.View;
import android.widget.AbsListView;
import android.widget.AdapterView;
import android.widget.ArrayAdapter;
import android.widget.ListView;
import android.widget.Toast;

public class MainActivity extends AppCompatActivity {

    String[]str={"test1","test2","test3","test4"};
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        ListView listView =(ListView)findViewById(R.id.listview1);
        ArrayAdapter adapter = new ArrayAdapter(this,android.R.layout.simple_list_item_1,str);
        listView.setAdapter(adapter);
        listView.setOnItemClickListener(onItemClickListener);
    }

    private AdapterView.OnItemClickListener onItemClickListener=new AdapterView.OnItemClickListener(){
        @Override
        public void onItemClick(AdapterView<?> adapterView, View view, int i, long l) {
            Toast.makeText(MainActivity.this,"點選第 "+(i +1) +" 個 \n內容："+str[i], Toast.LENGTH_SHORT).show();
        }
    };
}
```
上述程式碼中 ListView 所使用 item 的 layout ( android.R.layout.simple_list_item_1 ) 是 android 所提供的內建 layout。其他官方提供的 layout 有 simple_list_item_checked、
simple_list_item_multiple_choice 和 simple_list_item_single_choice，使用者可自行更改嘗試執行結果。


在[官方網站](https://developer.android.com/reference/android/widget/ArrayAdapter.html)中提到若要使用ListView搭配ArrayAdapter建議改用RecyclerView類別，因為此類別有更好的表現。

## 客製化 ListView：以 BaseAdapter 為範例

BaseAdapter 是一個可以使用在 ListView 或是 Spinner 的基礎抽象類別，使用時機是在一個 item ( Data row ) 有多個 View 要呈現時，例如在一個 item 放 Image、Buttom 和 TextView。BaseAdapter 的使用上有極大的彈性，其客製化的使用上比基本使用多了兩個步驟：
>1. 建立每一個 item 的 layout ( 可參看下面 list_item 的程式碼 )
>2. BaseAdapter 的實作 ( 可參看 MyBaseAdapter.java 程式碼 )

此範例中放入兩個 TextView，每個 item 都能呈現兩個文字資料。
以下為 list_item.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <TextView
        android:id="@+id/textView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:text="TextView" />

    <TextView
        android:id="@+id/textView2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_weight="4"
        android:text="TextView" />
</LinearLayout>
```
繼承 BaseAdapter 抽象類別需要實作四個方法，如下
```java
public class MyBaseAdapter extends BaseAdapter {

    //  取得集合的大小
    @Override
    public int getCount() {
        return 0;
    }

    // 透過索引取得 item ( 某一列 ) 的內容
    @Override
    public Object getItem(int i) {
        return null;
    }

    // 取得 item ( 某一列 ) 的 ID
    @Override
    public long getItemId(int i) {
        return 0;
    }

    // 取得 item ( 某一列 ) 的畫面 ( View )
    @Override
    public View getView(int i, View view, ViewGroup viewGroup) {
        return null;
    }
}

```

此段的解說重點將放在 getView 方法上。在滑動 ListView 時， ListView 中的某一列 ( item ) 超出手機螢幕畫面時會呼叫 getView 方法。為了不浪費系統資源必須先判斷畫面是否已經存在，若已存在則不須建立新的畫面，若不存在再去建立新的畫面。
以下為 MyBaseAdapter.java
```java
package com.example.sarah.testlistview;

import android.content.Context;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;
import android.widget.TextView;
import java.util.ArrayList;

/**
 * Created by sarah on 2017/9/27.
 */

public class MyBaseAdapter extends BaseAdapter {

    private Context context;
    private LayoutInflater li;
    private ArrayList<String[]> data;

    public MyBaseAdapter(Context context, ArrayList data){
        this.context = context;
        this.data = data;
        this.li = LayoutInflater.from(this.context);
    }

    @Override
    public int getCount() {
        return data.size();
    }

    @Override
    public Object getItem(int i) {
        return data.get(i);
    }

    @Override
    public long getItemId(int i) {
        Object obj = data.get(i);
        return data.indexOf(obj);
    }

    private static class ViewHolder{
        TextView data;
        TextView entertainment;
    }

    @Override
    public View getView(int i, View view, ViewGroup viewGroup) {
        ViewHolder holder;
        if(view == null){
            view = li.inflate(R.layout.list_item,viewGroup,false);
            holder = new ViewHolder();
            holder.data = (TextView)view.findViewById(R.id.textView);
            holder.entertainment=(TextView)view.findViewById(R.id.textView2);
            view.setTag(holder);
        }else{
            holder = (ViewHolder)view.getTag();
        }
        holder.data.setText(data.get(i)[0]);
        holder.entertainment.setText(data.get(i)[1]);
        return view;
    }
}

```

剩下的部分跟ListView的基本使用方法是同一個概念。
以下為 activity_main.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.example.sarah.testlistview.MainActivity">

    <ListView
        android:id="@+id/listview1"
        android:layout_width="368dp"
        android:layout_height="495dp"
        android:layout_marginRight="8dp"
        app:layout_constraintRight_toRightOf="parent"
        android:layout_marginLeft="8dp"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"
        android:layout_marginBottom="8dp"
        app:layout_constraintTop_toTopOf="parent"
        android:layout_marginTop="8dp" />
</android.support.constraint.ConstraintLayout>
```

以下為 MainActivity.java
```java
package com.example.sarah.testlistview;

import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.view.View;
import android.widget.AdapterView;
import android.widget.ListView;
import android.widget.Toast;

import java.util.ArrayList;

public class MainActivity extends AppCompatActivity {

    private ListView ls;
    ArrayList<String[]> data;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        ls = (ListView) findViewById(R.id.listview1);

        data = new ArrayList();

        String [][] datas = { {"星期一","吃飯睡覺打東東"},
                {"星期二","買書爬山好幸福"},
                {"星期三","聊天打屁發神經"},
                {"星期四","讀書放空真好笑"},
                {"星期五","悠哉悠哉寫程式"},
                {"星期六","跑步休息真愜意"},
                {"星期日","開開心心去郊遊"}};

        for(int i =0;i<datas.length;i++){
            data.add(datas[i]);
        }

        MyBaseAdapter mba = new MyBaseAdapter(this,data);

        ls.setAdapter(mba);
        ls.setOnItemClickListener(onItemClickListener);

    }
    private AdapterView.OnItemClickListener onItemClickListener=new AdapterView.OnItemClickListener(){
        @Override
        public void onItemClick(AdapterView<?> adapterView, View view, int i, long l) {
            StringBuilder completeData = new StringBuilder();
            for(int j=0;j<data.get(i).length;j++)
                completeData.append(data.get(i)[j]);
            Toast.makeText(MainActivity.this,"點選第 "+(i +1) +" 個 \n內容："+completeData.toString(), Toast.LENGTH_SHORT).show();
        }
    };
}
```

相關文章

* [Android ListView Tutorial](https://www.raywenderlich.com/124438/android-listview-tutorial)
* [Using lists in Android wth ListView - Tutorial](http://www.vogella.com/tutorials/AndroidListView/article.html#androidlists_overview)
* [Android开发：ListView、AdapterView、RecyclerView全面解析](http://www.jianshu.com/p/4e8e4fd13cf7)

