---
title: Fastlane
date: 2020-10-12 13:18:12
tags: ios
category: ios

---



# Flutter - ios 添加 fastlane 配置

1. ios 目录下： `fastlane init`

```
选择 2. 👩‍✈️  Automate beta distribution to TestFlight

输入 apple 账号，密码


```

2. 设置 `ios/fastlane/Gymfile`

```
# For more information about this configuration visit
# https://docs.fastlane.tools/actions/gym/#gymfile

# In general, you can use the options available
# fastlane gym --help

# Remove the # in front of the line to enable the option

scheme("Runner")

# sdk("iphoneos9.0")

# output_directory("./")

# export_xcargs("allowProvisioningUpdates")

export_options("./export-options.plist")

silent(true)

```

3. `ios/export-options.plist`

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>provisioningProfiles</key>
    <dict>
        <key>com.taurus.ucg</key>
        <string>jeffery-ad</string>
    </dict>
    <key>compileBitcode</key>
    <false/>
    <key>method</key>
    <string>ad-hoc</string>
</dict>
</plist>
```


检查是否成功: `ios/test_fastlane.sh`

```
#!/bin/bash -l

flutter build ios

fastlane gym --scheme Runner --clean --archive_path /Users/double/work/jinniu/00.project/UCG_mobile/ios/ipa --output_name chicha.ipa
```

