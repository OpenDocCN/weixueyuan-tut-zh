# Android 2D 绘图开发简介

> 原文：[`c.biancheng.net/view/3267.html`](http://c.biancheng.net/view/3267.html)

Android 系统提供了非常强大的图形处理能力。Android 系统对于 2D 图形的处理采用自定义的一系列 2D 图形处理类，而没有使用 Java JDK 提供的图形处理类。

这些自定义的类分别位于 android.graphics、android.graphics.drawable.shapes 和 android.view.animation 包中。而对 3D 图形进行处理的类分别位于 avax.microedition.khronos.opengles 和 android. opengl 包中。

Android 系统中的图形处理基本可以分为两类，一类是针对不经常变化的图像，即静态图形处理；另一类是针对经常变化的图像，即动态图形处理。

## 2D 绘图

Android API 提供一系列进行 2D 绘图的方法，被放置在 android.graphics.drawable 包下。通常有两种 2D 绘图的方法：

*   在布局文件定义的 View 组件中进行绘图。绘图工作由系统的绘制进程管理，开发者只需绘制图形即可。这种方式适合绘制不需要实时更新的静态图像。
*   在 Canvas 中绘图。这种方式需要由开发者自己调用 onDraw() 方法来对图像进行绘制。当图像需要定时更新时最好使用这种方式，适合动画绘制和视频游戏开发。

## 获取 Canvas 对象

要在 Android 系统下绘制图形，需要 4 个基本组件，分别说明如下。

| 名称 | 说明 |
| --- | --- |
| Bitmap | 相当于画布，用于管理像素。 |
| Canvas | 相当于在 Bitmap 上绘图的画家，用于管理绘制过程，提供绘图方法。 |
| Drawable | 绘制要素包括形状、路径、文本，图像等，用于将 Canvas 绘制的图像显示给用户。 |
| Paint | 相当于绘图用到的画笔，可以设置画笔的颜色、类型等。 |

若在自定义的 View 组件上绘制图像，只需重写 onDraw() 方法即可。示例代码如下：

```

public class MyView extends View{
    @Override
    protected void onDraw(Canvas canvas){
        //使用 Canvas 绘图
    }
}
```

如果需要新建一个 Canvas，则必须定义一个 Bitmap 对象用于绘图。获取 Bitmap 对象，并且使用 Canvas 绘图的示例代码如下：

Bitmap b=Bitmap.createBitmap(100, 100, Bitmap.Config.ARGB_8888);
Canvas c=new Canvas(b);
//使用 Canvas 绘图

若使用 SurfaceView 对象绘制动态图像，一般通过 SurfaceHolder.lockCanvas() 方法获取 Canvas 对象，然后通过 Canvas 进行绘图，绘图结束后，通过 surfaceHolder.unlockCanvasAndPost() 方法释放 Canvas 对象。示例代码如下：

```

SurfaceHolder surfaceHolder=surfaceView.getHolder();
Canvas canvas=surfaceHolder.lockCanvas();//获得 canvas 对象
//使用 Canvas 绘图
...
surfaceHolder.unlockCanvasAndPost(canvas); //释放 canvas
```