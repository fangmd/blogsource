---
title: Android 时间操作
date: 2016-05-15 12:18:12
tags: DataUtils
category: android
---

# Android 时间操作

<!--more-->

```java
    String label = DateUtils.formatDateTime(this.getApplicationContext(), System.currentTimeMillis(),
                    DateUtils.FORMAT_SHOW_TIME | DateUtils.FORMAT_SHOW_DATE | DateUtils.FORMAT_ABBREV_ALL);
```

返回相对于当前时间的最大区间表示的字符串：几(分钟,小时,天,周,月,年)前/后。

```java
    CharSequence date = DateUtils.getRelativeTimeSpanString(System
                .currentTimeMillis() + 1000*1000);

    tv.setText(date);
```

返回相对于当前时间的一个时间字符串：在同一天显示时分；在不同一天，显示月日；在不同一年，显示年月日

```java
    CharSequence date = DateUtils.getRelativeTimeSpanString(
    MainActivity.this, System.currentTimeMillis() + 10000 * 10000);

    tv.setText(date);
```

返回两个时间值间的相距字符串

```java
    String date = DateUtils.formatDateRange(MainActivity.this,
                System.currentTimeMillis(),
                System.currentTimeMillis() + 60 * 60 * 10000,
                DateUtils.FORMAT_SHOW_TIME);

    tv.setText(date);
```

# SimpleDateFormat 代码

```java
        public static SimpleDateFormat sdf = new SimpleDateFormat("EEE, dd MMM yyyy HH:mm:ss Z", Locale.ENGLISH);

        public static void main(String[] args) {
            String str = "Mon, 11 Apr 2016 06:24:42 +0000";

            try {
                Date date1 = sdf.parse(str);
                SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd", Locale.CHINA);
                dateFormat.format(date1);
            } catch (ParseException e) {
                e.printStackTrace();
            }
        }
```
