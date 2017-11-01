---
title: System UI Manage
date: 2017-10-30 12:18:12
tags: [Train]
category: android

---

![https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/SystemUIManage.gif](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/SystemUIManage.gif)

[系统 UI 管理，官方 Train](https://developer.android.com/training/system-ui/dim.html#reveal)

# Dimming the System Bars  （沉浸模式）

```java
// This example uses decor view, but you can use any visible view.
View decorView = getWindow().getDecorView();
int uiOptions = View.SYSTEM_UI_FLAG_LOW_PROFILE;
decorView.setSystemUiVisibility(uiOptions);
```

# Reveal the Status and Navigation Bars （全屏模式）

```java
    public void toggleHideyBar() {

        // BEGIN_INCLUDE (get_current_ui_flags)
        // The UI options currently enabled are represented by a bitfield.
        // getSystemUiVisibility() gives us that bitfield.
        int uiOptions = getWindow().getDecorView().getSystemUiVisibility();
        int newUiOptions = uiOptions;
        // END_INCLUDE (get_current_ui_flags)
        // BEGIN_INCLUDE (toggle_ui_flags)
        boolean isImmersiveModeEnabled =
                ((uiOptions | View.SYSTEM_UI_FLAG_IMMERSIVE_STICKY) == uiOptions);
        if (isImmersiveModeEnabled) {
            Log.i("SystemUI", "Turning immersive mode mode off. ");
        } else {
            Log.i("SystemUI", "Turning immersive mode mode on.");
        }

        // Navigation bar hiding:  Backwards compatible to ICS.
        if (Build.VERSION.SDK_INT >= 14) {
            newUiOptions ^= View.SYSTEM_UI_FLAG_HIDE_NAVIGATION;
        }

        // Status bar hiding: Backwards compatible to Jellybean
        if (Build.VERSION.SDK_INT >= 16) {
            newUiOptions ^= View.SYSTEM_UI_FLAG_FULLSCREEN;
        }

        // Immersive mode: Backward compatible to KitKat.
        // Note that this flag doesn't do anything by itself, it only augments the behavior
        // of HIDE_NAVIGATION and FLAG_FULLSCREEN.  For the purposes of this sample
        // all three flags are being toggled together.
        // Note that there are two immersive mode UI flags, one of which is referred to as "sticky".
        // Sticky immersive mode differs in that it makes the navigation and status bars
        // semi-transparent, and the UI flag does not get cleared when the user interacts with
        // the screen.
        if (Build.VERSION.SDK_INT >= 18) {
            newUiOptions ^= View.SYSTEM_UI_FLAG_IMMERSIVE_STICKY;
        }

        getWindow().getDecorView().setSystemUiVisibility(newUiOptions);
        //END_INCLUDE (set_ui_flags)
    }
```

# Hiding the Status Bar

## Hide the Status Bar on Android 4.0 and Lower

[https://developer.android.com/training/system-ui/status.html#40](https://developer.android.com/training/system-ui/status.html#40)

## Android 4.1 and Higher

```java
View decorView = getWindow().getDecorView();
// Hide the status bar.
int uiOptions = View.SYSTEM_UI_FLAG_FULLSCREEN;
decorView.setSystemUiVisibility(uiOptions);
// Remember that you should never show the action bar if the
// status bar is hidden, so hide that too if necessary.
ActionBar actionBar = getActionBar();
actionBar.hide();
```

# Hiding the Navigation Bar

```java
View decorView = getWindow().getDecorView();
// Hide both the navigation bar and the status bar.
// SYSTEM_UI_FLAG_FULLSCREEN is only available on Android 4.1 and higher, but as
// a general rule, you should design your app to hide the status bar whenever you
// hide the navigation bar.
int uiOptions = View.SYSTEM_UI_FLAG_HIDE_NAVIGATION
              | View.SYSTEM_UI_FLAG_FULLSCREEN;
decorView.setSystemUiVisibility(uiOptions);
```


# Register a Listener 监听 系统界面变化

```java
View decorView = getWindow().getDecorView();
decorView.setOnSystemUiVisibilityChangeListener
        (new View.OnSystemUiVisibilityChangeListener() {
    @Override
    public void onSystemUiVisibilityChange(int visibility) {
        // Note that system bars will only be "visible" if none of the
        // LOW_PROFILE, HIDE_NAVIGATION, or FULLSCREEN flags are set.
        if ((visibility & View.SYSTEM_UI_FLAG_FULLSCREEN) == 0) {
            // TODO: The system bars are visible. Make any desired
            // adjustments to your UI, such as showing the action bar or
            // other navigational controls.
        } else {
            // TODO: The system bars are NOT visible. Make any desired
            // adjustments to your UI, such as hiding the action bar or
            // other navigational controls.
        }
    }
});
```

