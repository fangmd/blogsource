---
title: FFMPEG 使用记录
date: 2020-09-28 08:18:12
tags: FFMPEG
category: program

---

# Install 

```
brew install ffmpeg
```

查看信息:

```
brew info ffmpeg
```


# 添加图片水印

2种方式：

```
ffmpeg -i raw.mp4 -vf "movie=watermask.png[watermark];[in][watermark] overlay=(main_w-overlay_w)/2:main_h-overlay_h-20[out] " output.mp4

ffmpeg -i raw.mp4 -i watermask.png -filter_complex 'overlay=(main_w-overlay_w)/2:main_h-overlay_h-20' output.mp4
```

- `watermask.png` 水印图片地址
- `overlay` 水印位置， `(main_w-overlay_w)/2` 水平居中
- `-i raw.mp4` 视频输入
- `output.mp4` 视频输出

