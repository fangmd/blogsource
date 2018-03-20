---
title: Android Permission--官方文档
date: 2016-04-03 13:18:12
tags: [permission，官方文档]
category: android

---

[https://developer.android.com/guide/topics/security/permissions.html](https://developer.android.com/guide/topics/security/permissions.html)

Android is a privilege-separated operating system, in which each application runs with a distinct system identity (Linux user ID and group ID). Parts of the system are also separated into distinct identities. Linux thereby isolates applications from each other and from the system.

Android是一个特权分离的操作系统，每个应用程序运行的时候都有一个独特的系统身份（Linux UID和GID），系统本身也被分成不同的身份，Linux系统让应用程序相互独立。

# Security Architecture

为了保护系统完整性和用户隐私，Android将每个应用程序运行在单独的“沙盒”中，如果app要去获取“沙盒”之外的资源或者信息，就需要区申请权限，更具权限的不同系统做出不同的反应。

<!--more-->

# Application Signing

>All APKs (.apk files) must be signed with a certificate whose private key is held by their developer. This certificate identifies the author of the application. The certificate does not need to be signed by a certificate authority; it is perfectly allowable, and typical, for Android applications to use self-signed certificates. The purpose of certificates in Android is to distinguish application authors. This allows the system to grant or deny applications access to signature-level permissions and to grant or deny an application's request to be given the same Linux identity as another application.

所有的APK必须被开发者提供的证书签名。这个证书用于认证应用的开发者。这个证书不需要由证书颁发机构签名；在Android应用程序中支持私有签名。证书的目的是区分应用的作者。同时也可以让系统去允许和否决应用去获取签名级别的权限，并且允许和否决一个应用申请同一个Linux身份认证。

# User IDs and File Access

>At install time, Android gives each package a distinct Linux user ID. The identity remains constant for the duration of the package's life on that device. On a different device, the same package may have a different UID; what matters is that each package has a distinct UID on a given device.

在应用安装的时候，Android系统给每个应用包一个不同的Linux UID。这个身份在应用在这个设备上的生命周期中都是不变的。在不同的设备上，同一个应用包可能会有不同的UID； 这会导致一些问题。

>Because security enforcement happens at the process level, the code of any two packages cannot normally run in the same process, since they need to run as different Linux users. You can use the sharedUserId attribute in the AndroidManifest.xml's manifest tag of each package to have them assigned the same user ID. By doing this, for purposes of security the two packages are then treated as being the same application, with the same user ID and file permissions. Note that in order to retain security, only two applications signed with the same signature (and requesting the same sharedUserId) will be given the same user ID.

因为安全执法是在进程的级别上的，两个不同包的程序不能运行在同一个进程中，因为他们有不用的Linux UID。你可以在`AndroidManifest.xml`使用`sharedUserId`属性标签在两个包中来让他们拥有同一个UID。做这个的目的是让系统将两个应用程序作为同一个应用程序，他们共用一个
UID和文件权限。注意：为了保证安全，只有两个应用使用同一个证书进行签名并且设置`sharedUserId`才会使用同一个UID。

>Any data stored by an application will be assigned that application's user ID, and not normally accessible to other packages. When creating a new file with getSharedPreferences(String, int), openFileOutput(String, int), or openOrCreateDatabase(String, int, SQLiteDatabase.CursorFactory), you can use the MODE_WORLD_READABLE and/or MODE_WORLD_WRITEABLE flags to allow any other package to read/write the file. When setting these flags, the file is still owned by your application, but its global read and/or write permissions have been set appropriately so any other application can see it.

应用存储的所有数据都会被UID签名，并且通常不会被其他应用获取。当使用下面的方法创建文件的时候：

- `getSharedPreferences(String, int), openFileOutput(String, int)`
- `openOrCreateDatabase(String, int, SQLiteDatabase.CursorFactory)`

你可以使用标签：` MODE_WORLD_READABLE`或者`MODE_WORLD_WRITEABLE `来允许其他的应用去读/写文件。当设置了这些flags的时候，文件还是属于该应用的，但是其他的应用有权限来读/写该文件。


## Using Permissions

>A basic Android application has no permissions associated with it by default, meaning it cannot do anything that would adversely impact the user experience or any data on the device. To make use of protected features of the device, you must include one or more <uses-permission> tags in your app manifest.

一个基本的Android应用程序是没有权限的，如果要让他获取权限首先应该在`Manifest`中配置权限tag。

>If your app lists normal permissions in its manifest (that is, permissions that don't pose much risk to the user's privacy or the device's operation), the system automatically grants those permissions. If your app lists dangerous permissions in its manifest (that is, permissions that could potentially affect the user's privacy or the device's normal operation), the system asks the user to explicitly grant those permissions. The way Android makes the requests depends on the system version, and the system version targeted by your app:

对于`Manifest`中的普通权限，系统会自动授权，但是对于危险权限系统回去询问用户是否授权，询问的方式取决于系统的版本和app所设置的target版本。


1. Android6.0(API 23)及以上，app在运行的时候向用户申请危险权限；
2. Andorid5.1(API 22)及以下，app会在安装的时候申请危险权限，而且权限不能管理，申请后不能取消危险权限；

A particular permission may be enforced at a number of places during your program's operation:

- At the time of a call into the system, to prevent an application from executing certain functions:在调用系统功能的时候,为了保护应用程序执行某些对系统有威胁的功能.
- When starting an activity, to prevent applications from launching activities if other applications:在开启activity的时候,为了保护应用程序开启其他应用程序的activity
- Both sending and receiving broadcasts, to control who can receive your broadcast or who can send a broadcast to you.发送和接受广播的时候,为了控制谁可以接受到你的广播或者谁可以给你发送广播.
- Both sending and receiving broadcasts, to control who can receive your broadcast or who can send a broadcast to you.发送和接受广播的时候,为了控制谁可以接受到你的广播或者谁可以给你发送广播.
- Both sending and receiving broadcasts, to control who can receive your broadcast or who can send a broadcast to you.发送和接受广播的时候,为了控制谁可以接受到你的广播或者谁可以给你发送广播.
- When accessing and operating on a content provider.在操作content provider的时候.
- Binding to or starting a service.绑定或者开启一个服务.

## Automatic permission adjustments

>Over time, new restrictions may be added to the platform such that, in order to use certain APIs, your app must request a permission that it previously did not need. Because existing apps assume access to those APIs is freely available, Android may apply the new permission request to the app's manifest to avoid breaking the app on the new platform version. Android makes the decision as to whether an app might need the permission based on the value provided for the targetSdkVersion attribute. If the value is lower than the version in which the permission was added, then Android adds the permission.



# Normal and Dangerous Permissions
系统的权限被划分为很多级别,两个最重要的级别就是下面两个:

1. Normal permissions:获取当前sandbox以外的资源数据并且不会对用户隐私或者其他app的运行造成威胁或者风险
2. Dangerous permissions

# Permission groups
所有的Android危险系统权限都属于权限组中, 如果设备运行在Android6.0或者app编译的`targetSdkVersion`是23及以上,就需要区申请权限.

所有的危险权限:(前面是权限组,后面是权限组中的权限)

1. CALENDAR: READ_CALENDAR, WRITE_CALENDAR
2. CAMERA:CAMERA
3. CONTACTS:READ_CONTACTS,WRITE_CONTACTS,GET_ACCPUNTS
4. LOCATION:ACCESS_FINE_LOCATION,ACCESS_COARSE_LOCATION
5. MICROPHONE:RECORD_AUDIO
6. PHONE:READ_PHOE_STATE,CALL_PHONE,READ_CALL_LOG,WRITE_CALL_LOG,ADD_VOICEMAIL,USE_SIP,PROCESS_OUTGOING_CALLS
7. SENSORS:BODY_SENSORS
8. SMS:SEND_SMS,RECEIVE_SMS,RECEIVE_SMS,READ_SMS,RECEIVE_WAP_PUSH,RECEIVE_MMS
9. STORAGE:READ_EXTRNAL_STORAGE,WRITE_EXTRAL_STORAGE

# 定义和执行 自定义的权限
用户自定义权限,需要先在`AndroidManifest.xml`中声明,使用`<permission>`标签;

比如应用可以定义一个权限控制是否可以启动`activity`:

	<manifest xmlns:android="http://schemas.android.com/apk/res/android"
	    package="com.example.myapp" >
	    <permission android:name="com.example.myapp.permission.DEADLY_ACTIVITY"
		android:label="@string/permlab_deadlyActivity"
		android:description="@string/permdesc_deadlyActivity"
		android:permissionGroup="android.permission-group.COST_MONEY"
		android:protectionLevel="dangerous" />
	    ...
	</manifest>

**注意:**系统允许多个应用定义相同权限名的权限,除非这几个包都使用同一个证书签名.  在系统安装应用的时候如果该应用定义了系统只能其他程序已经定义的自定义权限,这个应用就不能被安装到系统中. 为了避免命名重复,一定要遵循命名规则:比如`com.example.myapp.ENGAGE_HYPERSPACE`

其中:

1. `protectionLevel`:是必须的,告诉系统这个权限的保护等级
2. `android:permissionGroup`:可选项,只用于帮组系统显示权限, 大多数情况下我们会把权限组定义成系统已有的,也可以自定义权限组; 最好是选择系统已有的权限组,方便权限显示的UI界面.
3. `label`,`description`:描述权限作用,如果没有这个权限会导致的问题

例如:

	<string name="permlab_callPhone">directly call phone numbers</string>
	<string name="permdesc_callPhone">Allows the application to call
	    phone numbers without your intervention. Malicious applications may
	    cause unexpected calls on your phone bill. Note that this does not
	    allow the application to call emergency numbers.</string>

# 自定义权限的建议

确定是否需要自定义权限

1. 在开发一系列的app的时候,如果需要自定义权限,必须确保权限只声明了一次(即使这几个app都使用同一个证书签名也建议确保权限只定义一次)
2. 如果权限功能只是要提供给相同签名的app,应该避免使用签名检查来定义自定义权限.
3. 如果开发一系列的app只在自己的设备中运行,应该独立开发一个app来管理所有的自定义权限.(基本不可能)

# 强制执行权限在AndroidManifest.xml
.....












