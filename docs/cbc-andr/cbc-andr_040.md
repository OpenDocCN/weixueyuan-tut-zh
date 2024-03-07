# Android Bitmap（点阵图像、绘制图像）

> 原文：[`c.biancheng.net/view/3039.html`](http://c.biancheng.net/view/3039.html)

Bitmap 称为点阵图像或绘制图像，是由称作像素（图片元素）的单个点组成的，这些点通过不同的排列和染色以构成图样。

Bitmap 是 Android 系统中图像处理最重要的类之一，用它可以获取图像文件信息，对图像进行剪切、旋转、缩放等操作，并可以将图像保存成特定格式的文件。

Bitmap 位于 android.graphics 包中，不提供对外的构造方法，只能通过 BitmapFactory 类进行实例化。利用 BitmapFactory 的 decodeFile 方法可以从特定文件中获取 Bitmap 对象，也可以使用 decodeResource() 从特定的图片资源中获取 Bitmap 对象。

实例 BitmapDemo 从资源文件中创建 Bitmap 对象，并对其进行一些操作，运行效果如图 1 所示。

![Bitmap 对象的效果](img/19158bca05541b1d6196eb741db92982.png)
图 1  Bitmap 对象的效果
其对应布局文件 Main.xml 的内容如下：

```

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical">

    <SeekBar
        android:id="@+id/seekBarId"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content" />

    <ImageView
        android:id="@+id/imageview"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@drawable/img1" />
</LinearLayout>
```

BitmapActivity.Java 的代码如下：

```

package introduction.android.bitmapdemo;

import android.annotation.SuppressLint;
import android.app.Activity;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.graphics.Matrix;
import android.os.Bundle;
import android.widget.ImageView;
import android.widget.SeekBar;

public class BitmapActivity extends Activity {

    ImageView myImageView;
    Bitmap myBmp, newBmp;
    int bmpWidth, bmpHeight;
    SeekBar seekbarRotate;
    float rotAngle;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_bitmap);
        myImageView = (ImageView) findViewById(R.id.imageview);
        //由 Resource 载入图片
        myBmp = BitmapFactory.decodeResource(getResources(), R.drawable.img1);
        bmpWidth = myBmp.getWidth();
        bmpHeight = myBmp.getHeight();
        //实例化 matrix
        Matrix matrix = new Matrix();
        //设定 Matrix 属性 x、y 缩放比例为 1.5
        matrix.postScale(1.5F, 1.5F);
        //顺时针旋转 45 度
        matrix.postRotate(45.0F);
        newBmp = Bitmap.createBitmap(myBmp, 0, 0, bmpWidth, bmpHeight, matrix, true);
        seekbarRotate = (SeekBar) findViewById(R.id.seekBarId);
        seekbarRotate.setOnSeekBarChangeListener(onRotate);
    }

    private SeekBar.OnSeekBarChangeListener onRotate = new SeekBar.OnSeekBarChangeListener() {
        public void onStopTrackingTouch(SeekBar seekBar) {
            // TODO Auto-generated method stub
        }

        public void onStartTrackingTouch(SeekBar seekBar) {
            // TODO Auto-generated method stub
        }

        public void onProgressChanged(SeekBar seekBar, int progress, boolean fromUser) {
            // TODO Auto-generated method stub
            Matrix m = new Matrix();
            m.postRotate((float) progress * 3.6F);
            newBmp = Bitmap.createBitmap(myBmp, 0, 0, bmpWidth, bmpHeight, m, true);
            myImageView.setImageBitmap(newBmp);
        }
    };
}
```

本实例实现了拖动进度条图片旋转的效果。

使用 BitmapFactory 从资源中载入图片，并获取图片的宽和高，之后使用 Matrix 类对图片进行缩放和旋转操作。