---
layout: post
title:  "書籍：Android Programming CH10 挑戰 (RecyclerView)"
date:   2018-03-23
desc: "Preference"
keywords: "Android"
categories: [Android]
tags: [Android,The Big Nerd Ranch Guide]
icon: icon-html
---

* ### [一、前言簡介](#1)
* ### [二、Chapter 10 Challenge](#2)
> ### [2.1 挑戰一：Efficient RecyclerView Reloading](#2.1)
> ### [2.2 挑戰二：Improving CrimeLab Performance](#2.2)
* ### [參考資料](#3)

<h2 id="1"></h2>

# 一、前言簡介
本篇文章是 Android Programming 書中第十章的 Challenge，此次的 Challenge 與 RecyclerView 的重載以及 Java Collection 的效率有關。

<h2 id="2"></h2>
# 二、Chapter 10 Challenge

<h2 id="2.1"></h2>

## 2.1 挑戰一：Efficient RecyclerView Reloading
每次載入 RecyclerView 時最多只會有一個 Crime 的資料有變化，因此在原本的程式碼中使用 `notifyDataSetChanged()` 方法將 RecyclerView 中所有 Crime 資料更新是效率較差的做法。比較有效率的方法是找出哪一個 Crime 資料更動，並用 `notifyItemChanged(int position)` 更新 RecyclerView 的 Crime 資料。

### 解題想法
當按下 CrimeListFragment 的其中一個 Crime 時，畫面會移動到其中一個 Crime 的詳細資料，也就是 CrimeFragment 中綁定的畫面。在按下 CrimeListFragment 的其中一個 Crime 時，CrimeListFragment 的生命週期會呼叫 `onPause()` 和 `onStop()`，這代表 CrimeListFragment 並沒有被摧毀，CrimeListFragment 中的屬性都還保留著原本的資料。因此在 CrimeListFragment 中可以新設一個屬性記錄所點擊 Crime 在 RecyclerView 中的 position。在畫面從 Crime 的詳細資訊回到 CrimeListFragment 只需要在 `onResume()` 方法中調用 RecyclerView.Adapter 的 `notifyItemChanged(int position)` 方法即可。以下是 CrimeListFragment.java中更動過的程式碼。

CrimeListFragment.java

```java
public class CrimeListFragment extends Fragment {
    ······
    private int mCrimePosition;
    
    private void updateUI(Bundle savedInstanceState) {
    ······
    
        if (mAdapter == null) {
            mAdapter = new CrimeAdapter(crimes);
            mCrimeRecyclerView.setAdapter(mAdapter);
        } else {
            mAdapter.notifyItemChanged(mCrimePosition);
        }
    }
    ······
    
    private class CrimeHolder extends RecyclerView.ViewHolder implements View.OnClickListener {
    
        @Override
        public void onClick(View v) {
            ······
            
            mCrimePosition = getAdapterPosition();
            ······
            
        }
    }
```
在程式碼中先宣告一個屬性 `private int mCrimePosition;` 用來記錄要更新的 Crime 在 RecyclerView 中的位置。`updateUI()` 方法中利用記錄到的 mCrimePosition 來更新單個Ｃrime 的資料。最後在 CrimeHolder 的 `onClick()` 方法裡，也就是當點擊某個Ｃrime 會調用的點擊事件裡用 ViewHolder 中的 `onClick()` 方法，在此方法中以 `getAdapterPosition()` 方法取得所點擊的Ｃrime 的位置。這樣就完成了此題挑戰的需求。

<h2 id="2.2"></h2>

## 2.2 挑戰二：Improving CrimeLab Performance
此挑戰中的 CrimeLab 用 ArrayList 儲存了許多 Crime，`getCrime(UUID)` 方法可以從 CrimeLab 尋找特定的 Crime，但是效率上較差，因此挑戰中希望讀者看以尋找更有效率的方法並且不改變 RecyclerView 中顯示 Crime 的順序。

### 解題想法：
CrimeLab 中是利用 ArrayList 儲存並搜尋 Crime，在題目中要求要提升尋找單個 Crime 的速率，且 RecyclerView 在顯示Ｃrime 時的順序不可改變。HashLinkMap 正好可以符合上訴兩個條件。

CrimeLab.java

```java
public class CrimeLab {
    ······
    
    private LinkedHashMap<UUID, Crime> mCrimes;
    ······
    
    private CrimeLab(Context context) {
        mCrimes = new LinkedHashMap<UUID, Crime>();
        for (int i = 0; i < 100; i++) {
            ······
            
            mCrimes.put(crime.getId(), crime);
        }
    }

    public List<Crime> getCrimes() {
        return new ArrayList<>(mCrimes.values());
    }

    public Crime getCrime(UUID id) {
        return mCrimes.get(id);
    }
}

```

# 參考資料

[Big Nerd Ranch](https://forums.bignerdranch.com/c/android-programming-the-big-nerd-ranch-guide)