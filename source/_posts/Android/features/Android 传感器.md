---
title: Android 传感器
date: 2016-05-04 12:18:12
tags: sensor
category: android

---

# Android 传感器

	private SensorManager mSensorManager;

	    @Override
	    protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);

		mSensorManager = (SensorManager) getSystemService(SENSOR_SERVICE);
	//        List<Sensor> sensors = sensorManager.getSensorList(Sensor.TYPE_ALL);
	//        for (Sensor sensor : sensors) {
	//            Log.d(TAG, "onCreate: " + sensor.getName());
	//        }

		Sensor acce = mSensorManager.getDefaultSensor(Sensor.TYPE_ACCELEROMETER);
		mSensorManager.registerListener(this, acce, SensorManager.SENSOR_DELAY_NORMAL); //参数3：监听频率，4种
	    }


	    @Override
	    public void onSensorChanged(SensorEvent event) {
		float x = event.values[0];
		float y = event.values[1];
		float z = event.values[2];
		Log.d(TAG, "onSensorChanged: x =" + x + ", y =" + y + ", z =" + z);
	    }

	    @Override
	    public void onAccuracyChanged(Sensor sensor, int accuracy) {

	    }

	    @Override
	    protected void onDestroy() {
		super.onDestroy();
		mSensorManager.unregisterListener(this);
	    }
