
---
title: gradle 配置记录
date: 2020-07-05 12:18:12
tags: [apk]
category: Android

---


# 修改 apk 名称

```
android{
	    // applicationVariants.all { variant ->
    //     variant.outputs.all {
    //         def appName = "chicha_"
    //         def buildType = variant.variantData.variantConfiguration.buildType.name
    //         def newName
    //         if (buildType == 'debug'){
    //             newName = "app-${variant.getFlavorName()}-debug.apk"
    //         } else {
    //             newName = "app-${appName}${defaultConfig.versionName}_${variant.getFlavorName()}.apk"
    //             outputFileName = newName
    //         }
    //     }
    // }
}
```