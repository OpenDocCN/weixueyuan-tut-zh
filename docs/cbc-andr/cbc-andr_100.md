# Android 位置传感器开发教程

> 原文：[`c.biancheng.net/view/3262.html`](http://c.biancheng.net/view/3262.html)

Android 平台支持的位置传感器主要有三种：

*   TYPE_MAGNETIC_FIELD。
*   TYPE_ORIENTATION。
*   TYPE_PROXIMITY。

下面对这三种传感器做简单介绍。

## 磁场传感器

磁场传感器用于测量地球磁场的强度。获取磁场传感器实例的代码如下：

```

private SensorManager mSensorManager;
private Sensor mSensor;
...
mSensorManager=(SensorManager)getSystemService(Context.SENSOR_SERVICE);
mSensor=mSensorManager.getDefaultSensor(Sensor.TYPE_MAGNETIC_FIELD);
```

磁场传感器获取的原始数据记录的是在三个方向上地球磁场的强度。通常情况下，这些数据并不会直接使用，而是和旋转向量传感器、加速度传感器的数据一起用于计算设备的位置数据。

## 方位传感器

方位传感器用于监测设备相对于地球坐标系的位置。方位传感器从 Android 2.2（API Level 8）就被淘汰，在之后的设备上的访问传感器都是软件传感器。

获取方位传感器实例的代码如下：

```

private SensorManager mSensorManager;
private Sensor mSensor;
...
mSensorManager=(SensorManager)getSystemService(Context.SENSOR_SERVICE);
mSensor=mSensorManager.getDefaultSensor(Sensor.TYPE_ORIENTATION);
```

以下代码演示了从方位传感器获取数据的过程：

```

public class SensorActivity extends Activity implements SensorEventListener {
    private SensorManager mSensorManager;
    private Sensor mOrientation;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

        mSensorManager = (SensorManager) getSystemService(Context.SENSOR_SERVICE);
        mOrientation = mSensorManager.getDefaultSensor(Sensor.TYPE_ORIENTATION);
    }

    @Override
    public void onAccuracyChanged(Sensor sensor, int accuracy) {
        // Do something here if sensor accuracy changes.
        // You must implement this callback in your code.
    }

    @Override
    protected void onResume() {
        super.onResume();
        mSensorManager.registerListener(this, mOrientation, SensorManager.SENSOR_DELAY_NORMAL);
    }

    @Override
    protected void onPause() {
        super.onPause();
        mSensorManager.unregisterListener(this);
    }

    @Override
    public void onSensorChanged(SensorEvent event) {
        float azimuth_angle = event.values[0];
        float pitch_angle = event.values[1];
        float roll_angle = event.values[2];
        // Do something with these orientation angles.
    }
}
```

## 距离传感器

距离传感器用于探测 Android 设备与其他物体的距离，例如手机与耳朵的距离。获取距离传感器实例的代码如下：

```

private SensorManager mSensorManager;
private Sensor mSensor;
...
mSensorManager=(SensorManager)getSystemService(Context.SENSOR_SERVICE);
mSensor=mSensorManager.getDefaultSensor(Sensor.TYPE_PROXIMITY);
```

下列代码演示了使用距离传感器的方法：

```

public class SensorActivity extends Activity implements SensorEventListener {
    private SensorManager mSensorManager;
    private Sensor mProximity;

    @Override
    public final void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        // Get an instance of the sensor service, and use that to get an instance of
        // a particular sensor.
        mSensorManager = (SensorManager) getSystemService(Context.SENSOR_SERVICE);
        mProximity = mSensorManager.getDefaultSensor(Sensor.TYPE_PROXIMITY);
    }

    @Override
    public final void onAccuracyChanged(Sensor sensor, int accuracy) {
        // Do something here if sensor accuracy changes.
    }

    @Override
    public final void onSensorChanged(SensorEvent event) {
        float distance = event.values[0];
        // Do something with this sensor data.
    }

    @Override
    protected void onResume() {
        // Register a listener for the sensor.
        super.onResume();
        mSensorManager.registerListener(this, mProximity, SensorManager.SENSOR_DELAY_NORMAL);
    }

    @Override
    protected void onPause() {
        // Be sure to unregister the sensor when the activity pauses.
        super.onPause();
        mSensorManager.unregisterListener(this);
    }
}
```