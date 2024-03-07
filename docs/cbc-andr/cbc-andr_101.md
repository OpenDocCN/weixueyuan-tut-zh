# Android 环境传感器开发教程

> 原文：[`c.biancheng.net/view/3264.html`](http://c.biancheng.net/view/3264.html)

Android 平台支持的环境传感器有如下几种：

*   TYPE_AMBIENT_TEMPERATURE。
*   TYPE_LIGHT。
*   TYPE_PRESSURE。
*   TYPE_RELATIVE_HUMIDITY。
*   TYPE_TEMPERATURE。

下列示例代码演示了使用压力传感器测量大气气压的方法，其他环境传感器的使用方法相同，在此不再一一描述（可参考《运动传感器教程》）：

```

public class SensorActivity extends Activity implements SensorEventListener {
    private SensorManager mSensorManager;
    private Sensor mPressure;

    @Override
    public final void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        // Get an instance of the sensor service, and use that to get an instance of
        // a particular sensor.
        mSensorManager = (SensorManager) getSystemService(Context.SENSOR_SERVICE);
        mPressure = mSensorManager.getDefaultSensor(Sensor.TYPE_PRESSURE);
    }

    @Override
    public final void onAccuracyChanged(Sensor sensor, int accuracy) {
        // Do something here if sensor accuracy changes.
    }

    @Override
    public final void onSensorChanged(SensorEvent event) {
        float millibars_of_pressure = event.values[0];
        // Do something with this sensor data.
    }

    @Override
    protected void onResume() {
        // Register a listener for the sensor, super.onResume();
        mSensorManager.registerListener(this, mPressure, SensorManager.SENSOR_DELAY_NORMAL)
    }

    @Override
    protected void onPause() {
        // Be sure to unregister the sensor when the activity pauses.
        super.onPause();
        mSensorManager.unregisterListener(this);
    }
}
```