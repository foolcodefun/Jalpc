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

在Android程式開發中，若在主線程(UI Thread)進行耗時的程式運作會使程式介面卡住或發生ANR(Aplocation Not Responding)，此時正是使用異步處理的時機。

AsyncTask是一個用來進行異步處理的抽象類別，適合應用於API串接、網路下載以及讀取資料等等花費大概幾秒鐘時間的事件。在官方網站中提到耗時較長的事件建議使用Executor、ThreadpoolExecutor或FutureTask。

此為AsyncTask定義的一部份
```java
public abstrask class AsyncTask<Params, Progress, Result>
```
使用AsyncTask需掌握三個泛型以及四個步驟

以下為三泛型

1. Params:傳入doInBackground的參數。
2. Progress:傳入onProgressUpdate的參數，可在doInBackgroundg執行時更新介面。
3. Result:doInBacjground結束後傳入onPostExecute的參數。

若不指定泛型的類型，使用如下
```java
private class DownloadTask extends AsyncTask<Void, Void, Void> { ... }
```
以下為AsyncTask的四個步驟

1. onPreExecute:此步驟是在UI Thread中，可處理UI上的動作。此步驟結束後將調用doInBackground。
2. doInBackground:此步驟為異步處理，在onPreExecute結束後執行並在結束時執行onPostExecute。
3. onProgressUpdate:此步驟在UI Thread中，可在doInBackground執行時用來顯示下載進度或時間等等。
4. onPostExecute:doInBadkground結束後會將結果傳至此步驟，此步驟可將結果顯示於UI中。


AsyncTask的使用須至少覆寫doInBackground，另外通常會搭配onPostExecute來呈現異步處理的結果。

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
數字3代表三秒，也可輸入其他數字但須符合上段程式碼定義的Integer...，此參數會傳入doInBackground

<a href="https://github.com/foolcodefun/testAsyncTask">完整專案連結</a>