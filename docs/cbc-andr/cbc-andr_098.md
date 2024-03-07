# Android 传感器开发教程

> 原文：[`c.biancheng.net/view/3258.html`](http://c.biancheng.net/view/3258.html)

大部分的 Android 设备都带有内建的用于测量运动、方位以及各种环境条件的传感器。这些传感器能够提供高精度、高准确性的原始数据，当用户需要监控设备的三维运动和位置或者监控周围条件的变换时，这些传感器很有效。

举例来讲，一个游戏可以通过不断读取设备的重力传感器的方式推断用户的动作和运动，如倾斜、摇晃、旋转、摆动等；一个天气相关的应用程序可以通过设备的温度传感器和湿度传感器计算并报告露水情况；一个旅游相关的应用程序可以根据地磁传感器和加速传感器模拟一个罗盘。

总的来讲，Android 平台支持三种类型的传感器。

**1) 运动传感器（Motion Sensors）**

这种类型的传感器用于在三维方向测量加速力和旋转力，包括加速度传感器、重力传感器、陀螺仪和旋转向量传感器等。

**2) 环境传感器（Environmental Sensors）**

这种类型的传感器用于测量各种环境参量，如环境温度、气压和湿度等，例如气压计、光度计和温度计等。

**3) 位置传感器（Position Sensors）**

这种类型的传感器用于测量设备的物理位置，包括方向传感器和磁力计等。

开发者可以访问设备上支持的传感器，并且通过 Android 传感器框架获取相关的原始数据。

Android 传感器框架提供几个类和接口帮助开发者开发完成与传感器相关的各种任务。例如，可以使用传感器框架完成如下任务：

*   获取当前设备支持的传感器类型。
*   获取某个传感器的具体信息，例如最大范围、生产商、功耗和分辨率等。
*   从传感器获取原始信息以及获取信息的频率。
*   注册或者注销用于监测传感器变化的监听器。

Android 的传感器框架允许开发者访问当前设备上各种类型的传感器，包括硬件传感器和软件传感器。

硬件传感器指内建在 Android 设备中的硬件部分，它们直接测量具体数据并传递给应用程序。

软件传感器不是以硬件方式存在于设备中的，而是软件模拟出来的，因此又叫虚拟传感器或者合成传感器。它们的数据来自一个或者多个硬件传感器。线性加速度传感器和重力传感器是典型的软件传感器。

表 1 所示为 Android 平台支持的所有的传感器类型。

**表 1 Android 平台支持的传感器类型**

| 传感器 | 类型 | 用途 |
| --- | --- | --- |
| TYPE_ACCELEROMETER | 硬件传感器 | 运动探测 |
| TYPE_AMBIENT_TEMPERATURE | 硬件传感器 | 监控环境温度 |
| TYPE_GRAVITY | 软件或者硬件传感器 | 运动探测 |
| TYPE_GYROSCOPE | 硬件传感器 | 旋转探测 |
| TYPE_LIGHT | 硬件传感器 | 控制屏幕亮度 |
| TYPE_LINEAR_ACCELERATION | 软件或者硬件传感器 | 探测某个方向的加速度 |
| TYPE_MAGNETIC_FIELD | 硬件传感器 | 创建罗盘 |
| TYPE_ORIENTATION | 软件传感器 | 探测设备方位 |
| TYPE_PRESSURE | 硬件传感器 | 探测空气压力变化 |
| TYPE_PROXIMITY | 硬件传感器  | 用于监测打电话时手机与耳朵的距离 |
| TYPE_RELATIVE_HUMIDITY | 硬件传感器 | 探测结露点、相对和绝对湿度 |
| TYPE_ROTATION_VECTOR | 软件或者硬件传感器 | 运动检测和旋转检测 |
| TYPE_TEMPERATURE | 硬件传感器 | 探测温度 |

以上传感器类型在 Android N 系统中都获得了支持，但是 TYPE_ ORIENTATION 和 TYPE_TEMPERATURE 两种类型已经被弃用。

如果应用程序要被发布在 Google Play 上，则可以通过应用程序的 Manifest 配置文件中的 <uses=features> 属性来设置应用程序的发布对象。例如以下代码：

<uses-feature android:name="android.hardware.sensor.accelerometer" android:required="true"/>

可以保证应用程序只能被带有加速度传感器的手机设备搜索到。

Android 传感器框架存放在 android.hardware 包中，主要涉及以下几个类和接口。

#### 1）SensorManager

这个类被用于创建传感器服务实例。该类提供了访问和罗列传感器的各种方法，用于注册和注销传感器事件监听器并获取方向信息。该类也提供了几个常量，用于报告传感器的精度、数据获取率和校正传感器。

#### 2）Sensor

这个类被用作创建某个特定传感器的实例。该类提供了用于确定传感器能力的各种方法。

#### 3）SensorEvent

该类被用作创建传感器事件对象。传感器事件对象包含传感器事件的相关信息，包括原始的传感器数据、传感器类型、产生的事件、事件精度以及事件发生的时间戳等。

#### 4）SensorEventListener

该接口包含两个回调方法。当传感器的值发生改变或者传感器的精度发生改变时，相关方法就会自动被调用。

通过传感器框架 API，开发者主要可以完成两件事：

*   识别传感器并且明确传感器功能。
*   监听传感器事件并进行处理。

## 标识传感器

识别传感器的工作要通过 SensorManager 类的实例来完成。获取 SensorManager 实例的代码如下：

private SensorManager mSensorManager;
...
mSensorManager=(SensorManager)getSystemService(Context.SENSOR_SERVICE);

通过 SensorManager 获取当前设备的传感器列表的代码如下：

List<Sensor>deviceSensors=mSensorManager.getSensorList(Sensor.TYPE_ALL);

要获取特定类型的传感器实例，使用 SensorManager.getDefaultSensor() 方法。以下代码演示了获取磁场传感器的方法：

```

if(mSensorManager.getDefaultSensor(sensor.TYPE_MAGNETIC_FIELD) != null){
    //Success!There's a magnetometer.
}else{
    //Failure! No magnetometer.
}
```

以下示例代码演示了获取当前设备的重力传感器的过程。要求重力传感器的销售商是“GoogleInc.”，如果满足该条件的传感器不存在，则尝试使用加速度传感器：

```

private SensorManager mSensorManager;
private Sensor mSensor;
...
mSensorManager=(SensorManager)getSystemService(Context.SENSOR_SERVICE);
if(mSensorManager.getDefaultSensor(Sensor.TYPE_GRAVITY)!=null){
    List<Sensor>gravSensors=mSensorManager.getSensorList(Sensor.TYPE_GRAVITY)
    for(int i=0;i<gravSensors.size();i++){
        if((gravSensors.get(i).getVendor().contains("Google Inc."))&&(gravSensors.get(i).getVersion()==3)){
            // Use the version 3 gravity sensor.
            mSensor=gravSensors.get (i);
        }
    }
}
else{
    // Use the accelerometer.
    if(mSensorManager.getDefaultSensor(Sensor.TYPE_ACCELEROMETER)!=null){
        mSensor = mSensorManager.getDefaultSensor(Sensor.TYPE_ACCELEROMETER);
}
else{
    // Sorry, there are no accelerometers on your device.
    // You can't play this game.
}
```

## 传感器事件处理

传感器事件监听器接口提供两个方法：onAccuracyChanged() 和 onSensorChanged() 方法，分别对传感器的精度改变和传感器的数值改变事件进行处理。

以下代码演示了从光敏传感器获取数据的过程：

```

public class SensorActivity extends Activity implements SensorEventListener {
    private SensorManager mSensorManager;
    private Sensor mLight;

    @Override
    public final void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

        mSensorManager = (SensorManager) getSystemService(Context.SENSOR_SERVICE);
        mLight = mSensorManager.getDefaultSensor(Sensor.TYPE_LIGHT);
    }

    @Override
    public final void onAccuracyChanged(Sensor sensor, int accuracy) {
        // Do something here if sensor accuracy changes.
    }

    @Override
    public final void onSensorChanged(SensorEvent event) {
        // The light sensor returns a single value.
        // Many sensors return 3 values, one for each axis,
        float lux = event.values[0];
        //Do something with this sensor value.
    }

    @Override
    protected void onResume() {
        super.onResume();
        mSensorManager.registerListener(this, mLight, SensorManager.SENSOR_DELAY_NORMAL);
    }

    @Override
    protected void onPause() {
        super.onPause();
        mSensorManager.unregisterListener(this);
    }
}
```

其中：

```

@Override
protected void onPause(){
    super.onPause();
    mSensorManager.unregisterListener(this);
}
```

这几行代码是很有必要的。如果当 Activity 暂停的时候，传感器的事件监听器没有被注销，则该监听器还会一直从传感器获取信息，这样会耗费大量的电力。

因此，当 Activity 暂停时，一定要使用上述代码在 onPause() 方法中注销对传感器事件的监听。

当 Activity 再次被激活时，在 onResume() 方法中使用 SensorManager.registerListener() 方法重新注册传感器事件监听器。

mSensorManager.registerListener(this, mLight, SensorManager.SENSOR_DELAY_NORMAL);

表示注册的传感器事件监听器为当前类的实例，被监听的传感器为 mLight，获取数据的频率为 SENSOR_DELAY_NORMAL。