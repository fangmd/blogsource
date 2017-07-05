---
title: ThreadPoll
date: 2016-04-22 08:00:35 
tags: ThreadPoll
categories: Android

---


1. 在AsyncTask加载图片的时候使用，需要开启多个AsyncTask加载很多图片
ExecutorService es = Executors.newFixedThreadPool(2);
new DownImages().executeOnExecutor(es,url1);
es.shutdown();

