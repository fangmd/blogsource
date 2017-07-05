---
title: android 读取本地json数据
date: 2016-04-03 13:18:12
tags: json
category: android

---

# android 读取本地json数据

## 0 

将json数据放在`app/src/main/assets`文件加下，如果没有这个文件夹，需要手动创建


## 1 
读取代码：

	//getActivity(), "d_select.json", DSelect.class
    public static Object readLocalFile(Activity activity, String title,Class c) throws IOException {
        InputStream is = activity.getResources().getAssets().open(title);
        ByteArrayOutputStream baos = new ByteArrayOutputStream();
        byte[] b = new byte[1024];
        int length = 0;
        while ((length = is.read(b)) != -1) {
            baos.write(b, 0, length);
        }
        Object o = new Gson().fromJson(baos.toString(), c);
        return o;
    }

参数3是json数据实例化后的对象类
