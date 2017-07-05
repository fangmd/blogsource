---
title: Gradle 密钥配置方式
date: 2016-12-01 09:13:12
tags: [Gradle]
category: Android

---


`app/gradle`:

```
Properties props = new Properties()
props.load(new FileInputStream(file("signing.properties")))
android {
    signingConfigs {
        release{
            keyAlias props['KEY_ALIAS']
            keyPassword props['KEY_PASSWORD']
            storeFile file(props['KEYSTORE_FILE'])
            storePassword props['KEYSTORE_PASSWORD']
        }
    }
    buildTypes {
        release {
            signingConfig signingConfigs.release
        }
        debug {
            signingConfig signingConfigs.release
        }
    }
}
```

`app/siging.properties`

```
KEYSTORE_FILE = C:\\Users\\Mr.Jude\\Documents\\Android\\HelloWorld.jks
KEYSTORE_PASSWORD = xxxxxx
KEY_ALIAS = xxxxxx
KEY_PASSWORD = xxxxxx
```

将signing.properties添加进忽略目录。
其他人pull下来代码后。自己新建signing.properties填入相应信息后即可编译成功。