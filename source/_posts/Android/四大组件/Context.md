---
title: Context
date: 2017-12-21 21:38:14
tags: Context
categories: android

---

|    name    | age |
| ---------- | --- |
| LearnShare |  12 |
| Mike       |  32 |



| 			  | Application | Activity | Service | ContentProvider | BroadcastReceiver |
| ----------- | ----------- | ------- | --------------- | ----------------- |
| Show a Dialog | NO | YES | NO | NO | NO |
|Start an Activity | NO1 | YES | NO1 | NO1 | NO1 |
|Layout Inflation | NO2 | YES | NO2 | NO2 |	NO2 |
|Start a Service | YES | YES | YES | YES | YES |
|Bind to a Service | YES | YES | YES | YES | NO |
|Send a Broadcast | YES | YES | YES | YES | YES |
|Register BroadcastReceiver | YES | YES | YES | YES | NO3 |
|Load Resource Values | YES | YES |	YES | YES |	YES |


参考：[https://possiblemobile.com/2013/06/context/](https://possiblemobile.com/2013/06/context/)
