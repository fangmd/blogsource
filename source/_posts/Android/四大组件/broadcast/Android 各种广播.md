---
title: Android 各种广播
date: 2016-12-05 14:38:14
tags: [广播]
categories: android

---

# 手机电池监控

## 创建广播 BroadcastReceiver

public class BatteryReceiver extends BroadcastReceiver {

    @Override
    public void onReceive(Context context, Intent intent) {

        // Are we charging / charged?
        int status = intent.getIntExtra(BatteryManager.EXTRA_STATUS, -1);
        boolean isCharging = status == BatteryManager.BATTERY_STATUS_CHARGING ||
                status == BatteryManager.BATTERY_STATUS_FULL;

        // How are we charging?
        int chargePlug = intent.getIntExtra(BatteryManager.EXTRA_PLUGGED, -1);
        boolean usbCharge = chargePlug == BatteryManager.BATTERY_PLUGGED_USB;
        boolean acCharge = chargePlug == BatteryManager.BATTERY_PLUGGED_AC;

        //电池电量，数字
        int level = intent.getIntExtra(BatteryManager.EXTRA_LEVEL, -1);
        //电池最大容量
        int scale = intent.getIntExtra(BatteryManager.EXTRA_SCALE, -1);
        //电池伏数
        int voltage = intent.getIntExtra(BatteryManager.EXTRA_VOLTAGE, -1);
        //电池温度
        int template = intent.getIntExtra(BatteryManager.EXTRA_TEMPERATURE, -1);

        float batteryPct = level / (float) scale;

        String bat = level + "%";

        LoggerUtils.d("status:" + status + " isCharging:" + isCharging +
                "How are we charging: usbCharge:" + usbCharge + " arCharge:" + acCharge +
                " level=" + level + " scale=" + scale + " batteryPct=" + batteryPct + " bat:" + bat);
    }
}

## 注册广播
在 AndroidManifest 中注册

    <receiver android:name=".br.BatteryReceiver">
        <intent-filter>
            <action android:name="android.intent.action.BATTERY_CHANGED"/>
            <action android:name="android.intent.action.ACTION_POWER_CONNECTED"/>
            <action android:name="android.intent.action.ACTION_POWER_DISCONNECTED"/>
        </intent-filter>
    </receiver>

**注意：** Android 中部分广播静态注册是无效的，需要在程序中动态注册。比如 这里的电量调节通过静态注册会发现虽然广播能够触发，但是 log 中获取不到电量的信息。还有耳机插拔的广播也需要动态注册。

动态注册：（对于需要一直监听的广播建议在 BaseApplication 中注册）

    // 这个方法在 BaseApplication 中的 onCreate 中调用
    private void startBatterMonitor() {
        IntentFilter intentFilter = new IntentFilter(Intent.ACTION_BATTERY_CHANGED);
        intentFilter.addAction(Intent.ACTION_POWER_CONNECTED);
        intentFilter.addAction(Intent.ACTION_POWER_DISCONNECTED);
        registerReceiver(new BatteryReceiver(), intentFilter);
    }

# 开／关 机监控

## 创建广播 BootReceiver

    public class BootReceiver extends BroadcastReceiver implements OnDownListener {

        @Override
        public void onReceive(Context context, Intent intent) {

            switch (intent.getAction()) {
                case Intent.ACTION_BOOT_COMPLETED:
                    // 开机

                    break;
                case Intent.ACTION_SHUTDOWN:
                    // 关机

                    break;
            }
        }
    }

## 注册广播

在 AndroidManifest 中：

    <receiver android:name=".br.BootReceiver">
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED"/>
            <action android:name="android.intent.action.ACTION_SHUTDOWN"/>
        </intent-filter>
    </receiver>

# 短信接收监控

## 创建广播 SMSReceiver

    public class SmsBR extends BroadcastReceiver {

        @Override
        public void onReceive(Context context, Intent intent) {
            // Get the data (SMS data) bound to intent
            Bundle bundle = intent.getExtras();
            SmsMessage[] msgs;

            String from = "";
            String body = "";

            if (bundle != null) {
                // Retrieve the SMS Messages received
                Object[] pdus = (Object[]) bundle.get("pdus");
                msgs = new SmsMessage[pdus.length];

                // For every SMS message received
                for (int i = 0; i < msgs.length; i++) {
                    // Convert Object array
                    msgs[i] = SmsMessage.createFromPdu((byte[]) pdus[i]);
                    // Sender's phone number
    //                from += "SMS from " + msgs[i].getOriginatingAddress() + " : ";
                    from = msgs[i].getOriginatingAddress();
                    // Fetch the text message
                    body += msgs[i].getMessageBody().toString();

                }
                Logger.d("检测到短信:" + from + ":" + body);
            }
        }

    }

## 注册广播

在 AndroidManifest 中：

    <receiver
        android:name=".br.SmsBR"
        android:priority="999">
        <intent-filter>
            <action android:name="android.provider.Telephony.SMS_RECEIVED"/>
        </intent-filter>
    </receiver>

# Gps 开关检测

## 创建广播 GpsReceiver

    public class GpsReceiver extends BroadcastReceiver {

        @Override
        public void onReceive(Context context, Intent intent) {

            if (intent.getAction().matches(LocationManager.PROVIDERS_CHANGED_ACTION)) {
                Boolean gpsOpen = isGpsOpen();
                LoggerUtils.d("gpsOpen= " + gpsOpen);
            }
        }

        public  Boolean isGpsOpen() {
            LocationManager locationManager
                    = (LocationManager) BaseApplication.getInstance().getSystemService(Context.LOCATION_SERVICE);
            Boolean IsOpen = false;
            if (locationManager.isProviderEnabled(LocationManager.GPS_PROVIDER)) {
                IsOpen = true;
            } else {
                IsOpen = false;
            }
            return IsOpen;
        }
    }

## 注册广播

在 AndroidManifest 中:

    <receiver android:name=".br.GpsReceiver">
        <intent-filter>
            <action android:name="android.location.PROVIDERS_CHANGED"/>
            <category android:name="android.intent.category.DEFAULT"/>
        </intent-filter>
    </receiver>

# 手机应用安装广播

## Receiver

    public class AppReceiver extends BroadcastReceiver {

        @Override
        public void onReceive(Context context, Intent intent) {

            //接收安装广播
            if (intent.getAction().equals("android.intent.action.PACKAGE_ADDED")) {
                String packageName = intent.getDataString();
                System.out.println("安装了:" +packageName + "包名的程序");
            }

            //接收卸载广播
            if (intent.getAction().equals("android.intent.action.PACKAGE_REMOVED")) {
                String packageName = intent.getDataString();
                System.out.println("卸载了:"  + packageName + "包名的程序");
            }

        }
    }

## 注册

    <receiver android:name=".br.AppReceiver">
        <intent-filter>
            <action android:name="android.intent.action.PACKAGE_ADDED"/>
            <action android:name="android.intent.action.PACKAGE_REMOVED"/>

            <data android:scheme="package"/>
        </intent-filter>
    </receiver>

# 蓝牙广播

## Receiver

    public class BluetoothReceiver extends BroadcastReceiver {

        @Override
        public void onReceive(Context context, Intent intent) {
            String action = intent.getAction();
            int state = intent.getIntExtra(BluetoothAdapter.EXTRA_STATE, -1);

            String ret = "";
            BluetoothStatus bluetoothStatus = new BluetoothStatus();
            switch (state) {
                case BluetoothAdapter.STATE_OFF:
                    ret = "Bluetooth off";
                    break;
                case BluetoothAdapter.STATE_ON:
                    ret = "Bluetooth on";
                    break;
                case BluetoothAdapter.ERROR:
                    ret = "Bluetooth error";
                    break;
                case BluetoothAdapter.STATE_TURNING_OFF:
                    ret = "Bluetooth STATE_TURNING_OFF";
                    break;
                case BluetoothAdapter.STATE_TURNING_ON:
                    ret = "Bluetooth STATE_TURNING_ON";
                    break;
                case BluetoothAdapter.STATE_CONNECTED:
                    ret = "Bluetooth STATE_CONNECTED";

                    BluetoothManager bluetoothManager = (BluetoothManager) BaseApplication.getInstance().getSystemService(Context.BLUETOOTH_SERVICE);
                    BluetoothAdapter bluetoothAdapter = BluetoothAdapter.getDefaultAdapter();

                    if (bluetoothAdapter != null) {
                        Set<BluetoothDevice> bondedDevices = bluetoothAdapter.getBondedDevices();
                        for (BluetoothDevice bondedDevice : bondedDevices) {
                            // Add the name and address to an array adapter to show in a ListView
                            Method method = null;
                            try {
                                method = bondedDevice.getClass().getMethod("isConnected");
                                Boolean connected = (Boolean) method.invoke(bondedDevice);
                                if (connected) {
                                    bluetoothStatus.id = bondedDevice.getName();
                                    bluetoothStatus.mac = bondedDevice.getAddress();
                                    bluetoothStatus.bluetoothStatus = 1;
                                    LoggerUtils.d("connect to  bluetooth:  id=" + bluetoothStatus.id + "  mac=" + bluetoothStatus.mac);
                                }

                            } catch (NoSuchMethodException e) {
                                e.printStackTrace();
                            } catch (InvocationTargetException e) {
                                e.printStackTrace();
                            } catch (IllegalAccessException e) {
                                e.printStackTrace();
                            }

                        }
                    } else {
                        LoggerUtils.e("bluetooth disable");
                    }

                    break;
                case BluetoothAdapter.STATE_CONNECTING:
                    ret = "Bluetooth STATE_CONNECTING";
                    break;
                default:
                    break;
            }


        }
    }

## 注册

    <receiver android:name=".br.BluetoothReceiver">
        <intent-filter>
            <action android:name="android.bluetooth.adapter.action.STATE_CHANGED"/>
        </intent-filter>
    </receiver>

# 网络状态广播

# USB 状态广播

## Receiver

    public class USBReceiver extends BroadcastReceiver {

        @Override
        public void onReceive(Context context, Intent intent) {

            String action = intent.getAction();
            switch (action) {
                case UsbManager.ACTION_USB_ACCESSORY_ATTACHED:

                    break;
                case UsbManager.ACTION_USB_ACCESSORY_DETACHED:

                    break;
                case UsbManager.ACTION_USB_DEVICE_ATTACHED:
                    LoggerUtils.d("usb attached");
                    break;
                case UsbManager.ACTION_USB_DEVICE_DETACHED:
                    LoggerUtils.d("usb detached");
                    break;
                case UsbManager.EXTRA_ACCESSORY:

                    break;
                case "android.hardware.usb.action.USB_STATE": // 0未使用 1充电 2媒体设备 3相机 4挂载SD卡 5挂载驱动
                        String functions = "";
                        boolean midi = intent.getBooleanExtra("midi", false);
                        if (midi) {
                            functions = "midi";
                        }
                        boolean mtp = intent.getBooleanExtra("mtp", false);
                        if (mtp) {
                            functions = "mtp";
                        }
                        boolean ptp = intent.getBooleanExtra("ptp", false);
                        if (ptp) {
                            functions = "ptp";
                        }
                        boolean adb = intent.getBooleanExtra("adb", false);
                        if (adb) {
                            functions = "adb";
                        }
                        boolean mass_storage = intent.getBooleanExtra("mass_storage", false);
                        if (mass_storage) {
                            functions = "mass_storage";
                        }
                        boolean charging = intent.getBooleanExtra("charging", false);
                        if (charging) {
                            functions = "charging";
                        }
                        LoggerUtils.d("usb status " + functions);
                    break;
            }

        }
    }

## 注册

    <receiver android:name=".br.USBReceiver">
        <intent-filter>
            <action android:name="android.hardware.usb.action.USB_DEVICE_ATTACHED"/>
            <action android:name="android.hardware.usb.action.USB_DEVICE_DETACHED"/>
            <action android:name="android.hardware.usb.action.USB_STATE"/>
        </intent-filter>
    </receiver>

# SIM 卡广播

    public class SimStateReceive extends BroadcastReceiver {

        @Override
        public void onReceive(Context context, Intent intent) {

            TelephonyManager tm = (TelephonyManager) context.getSystemService(Service.TELEPHONY_SERVICE);
            int state = tm.getSimState();

            String ret = "";
            int simCard = 0;
            switch (state) {
                case TelephonyManager.SIM_STATE_READY:
                    ret = "SIM Card input";
                    break;
                case TelephonyManager.SIM_STATE_UNKNOWN:
                    ret = "SIM_STATE_UNKNOWN";
                    break;
                case TelephonyManager.SIM_STATE_ABSENT:
                    ret = "No SIM Card";
                    break;
                case TelephonyManager.SIM_STATE_PIN_REQUIRED:
                    ret = "SIM_STATE_PIN_REQUIRED";
                    break;
                case TelephonyManager.SIM_STATE_PUK_REQUIRED:
                    ret = "SIM_STATE_PUK_REQUIRED";
                    break;
                case TelephonyManager.SIM_STATE_NETWORK_LOCKED:
                    ret = "SIM_STATE_NETWORK_LOCKED";
                    break;
                default:
                    break;
            }

            LoggerUtils.d(ret);

        }

    }

# 注册

    <receiver android:name=".br.SimStateReceive">
        <intent-filter>
            <action android:name="android.intent.action.SIM_STATE_CHANGED"/>
        </intent-filter>
    </receiver>

#
