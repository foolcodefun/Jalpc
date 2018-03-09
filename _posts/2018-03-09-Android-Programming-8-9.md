---
layout: post
title:  "Android Programming CH8 挑戰"
date:   2018-03-09
desc: "Preference"
keywords: "Android"
categories: [Android]
tags: [Android,The Big Nerd Ranch Guide]
icon: icon-html
---

* ### [一、前言簡介](#1)
* ### [二、Chapter 8 Challenge](#2)
* ### [參考資料](#3)

<h2 id="1"></h2>

# 一、前言簡介

在學習 android 的路上遇見了一本書叫做 Android Programming，其出版社為 The Big Nerd Ranch Guide。這本書在大部份章節的最後都會加上 Challenge 讓讀者挑戰，於是筆者決定每完成一個挑戰就將程式碼放在部落格中。

# 二、Challenge 8 Challenge

第八章的內容主要是在講 Fragment 的使用與整合、Singletan 模式的介紹以及 RecyclerView 的基本使用。此章節的挑戰是 RecyclerView 的延伸應用，希望在一個 RecyclerView 在不一樣程度的 Crime 以不一樣的 row_item 展示，挑戰中還提到了要 implement CrimeAdapter 類別中的  `getItenViewType(int)` 方法，並在 `onCreateViewHolder(ViewGroup, int)` 方法中回傳不一樣的 ViewHolder。

以下是筆者完成挑戰後對 CrimeAdapter 類別修改的部分

```
@Override
        public int getItemViewType(int position) {
            return mCrimes.get(position).isRequiresPolice() ? SERIOUS_CRIME : NORMAL_CRIME;
        }

        @Override
        public CrimeHolder onCreateViewHolder(ViewGroup parent, int viewType) {
            LayoutInflater inflater = LayoutInflater.from(getActivity());
            switch (viewType) {
                case NORMAL_CRIME:
                    return new NormalHolder(inflater, parent);
                case SERIOUS_CRIME:
                    return new CrimePoliceHolder(inflater, parent);
                default:
                    return null;
            }
        }
```
`getItemViewType(int)` 方法會根據 Crime 的資料來決定是要回傳什麼數字。而 `onCreateViewHolder` 會依照 viewType 的數字決定以哪一個 ViewHolder 展現資料。

以下是筆者完成挑戰後對 ViewHolder 類別修改的部分

```
private abstract class CrimeHolder extends ViewHolder {

        public CrimeHolder(View itemView) {
            super(itemView);
        }

        public abstract void bind(Crime crime) ;
    }

    private class NormalHolder extends CrimeHolder {

        private Crime mCrime;
        private final TextView mDateTextView;
        private final TextView mTitleTextView;

        public NormalHolder(LayoutInflater inflater, ViewGroup parent) {
            super(inflater.inflate(R.layout.list_item_crime, parent, false));
            mTitleTextView = itemView.findViewById(R.id.crime_title);
            mDateTextView = itemView.findViewById(R.id.crime_date);
        }

        public void bind(Crime crime) {
            mCrime = crime;
            mTitleTextView.setText(mCrime.getTitle());
            mDateTextView.setText(mCrime.getDate().toString());
        }
    }

    private class CrimePoliceHolder extends CrimeHolder {

        private Crime mCrime;
        private final TextView mDateTextView;
        private final TextView mTitleTextView;

        public CrimePoliceHolder(LayoutInflater inflater, ViewGroup parent) {
            super(inflater.inflate(R.layout.list_item_crime_police, parent, false));
            mTitleTextView = itemView.findViewById(R.id.crime_title);
            mDateTextView = itemView.findViewById(R.id.crime_date);
        }

        public void bind(Crime crime) {
            mCrime = crime;
            mTitleTextView.setText(mCrime.getTitle());
            mDateTextView.setText(mCrime.getDate().toString());
        }
    }
```

這就是這次挑戰的內容了。

# 參考資料
[Big Nerd Ranch](https://forums.bignerdranch.com/)