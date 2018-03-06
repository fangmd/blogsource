---
title: Android内存优化 -- LruCache
date: 2018-01-29 21:03:14
tags: [Android优化]
categories: android

---


# Lru 算法

Least Recently Used 最近最少使用算法

数据结构：链表

访问数据的时候，如果缓存中有数据 就将改数据移动到链表顶端；没有该数据就在链表顶端加入该数据，如果链表满了就移除底端的数据。

## Java 

在 Java 中使用 LinkedHashMap 实现 LRU 算法。

LinkedHashMap 是以访问顺序排序的, 排序方式满足了 LRU 算法，同时属于 Map， get 数据的时候复杂度为 1。

```java
public class LRUCache{
  private LinkedHashMap<Integer, Integer> map;
  private final int CAPACITY;

  public LRUCache(int capacity){
    CAPACITY = capacity;
    map = new LinkedHashMap<Integer, Integer>(capacity, 0.75f, true){
      protected boolean removeEldestEntry(Map.Entry eldest){
        return size()>CAPACITY;
      }
    };
  }

  public int get(int key){
    return map.getOrDefault(key, -1);
  }
  public void put(int key, int value){
    map.put(key, value);
  }
}
```


# 例子

图片缓存

```java
int maxMemory = (int) (Runtime.getRuntime().totalMemory()/1024);
int cacheSize = maxMemory/8;

        mMemoryCache = new LruCache<String,Bitmap>(cacheSize){
            @Override
            protected int sizeOf(String key, Bitmap value) {
                return value.getRowBytes()*value.getHeight()/1024;
            }
        };

```

