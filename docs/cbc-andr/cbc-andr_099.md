# Android 运动传感器开发教程

> 原文：[`c.biancheng.net/view/3261.html`](http://c.biancheng.net/view/3261.html)

目前，Android 平台支持的运动传感器包括以下 5 种：

*   TYPE_ACCELEROMETER。
*   TYPE_GRAVITY。
*   TYPE_GYROSCOPE。
*   TYPE_LINEAR_ACCELERATION。
*   TYPE_ROTATION_VECTOR。

本节教程将对这几种传感器的用法做简单介绍。

## 加速度传感器

获取加速度传感器实例的代码如下：

```

private SensorManager mSensorManager;
private Sensor mSensor;
...
mSensorManager=(SensorManager)getSystemService(Context.SENSOR_SERVICE);
mSensor=mSensorManager.getDefaultSensor(Sensor.TYPE_ACCELEROMETER);
```

从传感器获取数据并计算三个方向的加速度的代码如下：

```

public void onSensorChanged(SensorEvent event) {
    // In this example, alpha is calculated as t / (t+dT) ,
    // where t is the low-pass filter's time-constant and
    // dT is the event delivery rate.

    final float alpha = 0.8;

    // Isolate the force of gravity with the low-pass filter.
    gravity[0] = alpha * gravity[0] + (1 - alpha) * event.values[0];
    gravity[1] = alpha * gravity[l] + (1 - alpha) * event.values[1];
    gravity[2] = alpha * gravity[2] + (1 - alpha) * event.values[2];

    // Remove the gravity contribution with the high-pass filter.
    linear_acceleration[0] = event.values[0] - gravity[0];
    linear_acceleration[1] = event.values[1] - gravity[l];
    linear_acceleration[2] = event.values[2] - gravity[2];
}
```

该计算方法仅是举例使用，实际计算方法要针对应用而确定。

## 重力传感器

重力传感器是加速度传感器的一种，其数据处理方式也相似。此处不再重复重力传感器的数据计算方法。获取重力传感器的代码如下：

```

private SensorManager mSensorManager;
private Sensor mSensor;
...
mSensorManager=(SensorManager)getSystemService(Context.SENSOR_SERVICE);
mSensor=mSensorManager.getDefaultSensor(Sensor.TYPE_GRAVITY);
```

## 陀螺仪

陀螺仪可以在三个纬度上测量设备的旋转情况。获取陀螺仪传感器的代码如下：

```

private SensorManager mSensorManager;
private Sensor mSensor;
...
mSensorManager=(SensorManager)getSystemService(Context.SENSOR_SERVICE);
mSensor=mSensorManager.getDefaultSensor(Sensor.TYPE_GYROSCOPE);
```

从陀螺仪数据计算三个纬度旋转情况的代码如下：

```

// Create a constant to convert nanoseconds to seconds.
private static final float NS2S = 1.0f / 1000000000.0f;
private final float[] deltaRotationVector = new float[4]();
private float timestamp;

public void onSensorChanged(SensorEvent event) {
    // This timestep's delta rotation to be multiplied by the current rotation
    // after computing it from the gyro sample data.
    if (timestamp != 0) {
        final float dT = (event.timestamp - timestamp) * NS2S;
        // Axis of the rotation sample, not normalized yet.
        float axisX = event.values[0];
        float axisY = event.values[1];
        float axisZ = event.values[2];

        // Calculate the angular speed of the sample
        float omegaMagnitude = sqrt(axisX * axisX + axisY * axisY + axisZ * axisZ);

        // Normalize the rotation vector if it's big enough to get the axis
        // (that is, EPSILON should represent your maximum allowable margin of error)
        if (omegaMagnitude > EPSILON) {
            axisX /= omegaMagnitude;
            axisY /= omegaMagnitude;
            axisZ /= omegaMagnitude;
        }
        // Integrate around this axis with the angular speed by the timestep
        // in order to get a delta rotation from this sample over the timestep
        // We will convert this axis-angle representation of the delta rotation
        // into a quaternion before turning it into the rotation matrix.
        float thetaOverTwo = omegaMagnitude * dT / 2\. Of;
        float sinThetaOverTwo = sin(thetaOverTwo);
        float cosThetaOverTwo = cos(thetaOverTwo);
        deltaRotationVector[0] = sinThetaOverTwo * axisX;
        deltaRotationVector[1] = sinThetaOverTwo * axisY;
        deltaRotationVector[2] = sinThetaOverTwo * axisZ;
        deltaRotationVector[3] = cosThetaOverTwo;
    }
    timestamp = event.timestamp;
    float[] deltaRotationMatrix = new float[9];
    SensorManager.getRotationMatrixFromVector(deltaRotationMatrix, deltaRotationVector);
    // User code should concatenate the delta rotation we computed with the current rotation
    // in order to get the updated rotation.
    // rotationCurrent=rotationCurrent * deltaRotationMatrix;
}
```

## 线性加速度传感器

线性加速度传感器是传感器的一种。其获取实例的代码如下：

```

private SensorManager mSensorManager;
private Sensor mSensor;
...
mSensorManager=(SensorManager)getSystemService(Context.SENSOR_SERVICE);
mSensor=mSensorManager.getDefaultSensor(Sensor.TYPE_LINEAR_ACCELERATION);
```

## 旋转向量传感器

旋转向量传感器能反映出当前设备的状态，其返回值是旋转角度与旋转轴的集合。获取旋转向量传感器实例的相关代码如下：

```

private SensorManager mSensorManager;
private Sensor mSensor;
....
mSensorManager=(SensorManager)getSystemService(Context.SENSOR_SERVICE);
mSensor=mSensorManager.getDefaultSensor(Sensor.TYPE_ROTATION_VECTOR);
```