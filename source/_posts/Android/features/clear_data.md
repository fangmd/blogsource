---
title: Android clean data in Application
date: 2016-11-27 09:23:14
tags: features
categories: android

---

![http://stackoverflow.com/questions/6134103/clear-applications-data-programmatically](http://stackoverflow.com/questions/6134103/clear-applications-data-programmatically)

Utils:

    public static void clearApplicationData() {
        File cacheDirectory = BaseApplication.getInstance().getCacheDir();
        File applicationDirectory = new File(cacheDirectory.getParent());
        if (applicationDirectory.exists()) {
            String[] fileNames = applicationDirectory.list();
            for (String fileName : fileNames) {
                if (!fileName.equals("lib")) {
                    deleteFile(new File(applicationDirectory, fileName));
                }
            }
        }
    }

    public static boolean deleteFile(File file) {
        boolean deletedAll = true;
        if (file != null) {
            if (file.isDirectory()) {
                String[] children = file.list();
                for (int i = 0; i < children.length; i++) {
                    deletedAll = deleteFile(new File(file, children[i])) && deletedAll;
                }
            } else {
                deletedAll = file.delete();
            }
        }

        return deletedAll;
    }

# Clean SharePreferences

    prefs.edit().clear().commit();

# Api 19 

    import android.os.Build.*;
    if (VERSION_CODES.KITKAT <= VERSION.SDK_INT) {
        ((ActivityManager)context.getSystemService(ACTIVITY_SERVICE))
                .clearApplicationUserData(); // note: it has a return value!
    } else {
        // use old hacky way, which can be removed
        // once minSdkVersion goes above 19 in a few years.
    }
