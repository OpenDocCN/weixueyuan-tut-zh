# Android Drawable 开发简介

> 原文：[`c.biancheng.net/view/3276.html`](http://c.biancheng.net/view/3276.html)

Drawable 是“可绘制的东西”的抽象类，被定义在 android.graphics.drawable 包下。

Drawable 类继承了很多代表不同形状的子类，例如 BitmapDrawable、ShapeDrawable、PictureDrawable 等。开发者也可以定义自己的用于特定形状绘制的子类。

获取 Drawable 对象有三种方式：

*   使用工程资源文件中保存的图像资源。
*   使用 XML 文件定义的 Drawable 属性。
*   通过构造方法构建。

## 从资源文件中创建 Drawable 对象

添加图像到应用程序工程中最简单的方式是从资源文件中获取图像。资源文件中的图像资源会被放置在 res/drawable/ 文件夹下，常见的图像资源类型有 PNG、JPG 和 GIF，在允许的情况下，建议优先使用 PNG 格式的图像，其次是 PG 格式的图像。

系统会自动为每个 drawable 文件夹下的资源文件生成一个格式为 R.drawable.xxx 的 ID 号，在工程中可以通过该 ID 使用该资源。在之前的实例中，我们一直在使用这种方法。

例如，在 res/drawable/ 文件夹下有一个资源文件为 my_image.png，系统为其生成的资源 ID 为 R.drawable.my_image，在 ImageView 组件中使用该图像的代码如下：

ImageView i=new ImageView(this);
i.setImageResource(R.drawable.my_image);

如果要获得该资源的 Drawable 对象，可使用如下代码：

Resources res=mContext.getResources();
Drawable myImage=res.getDrawable(R.drawable.my_image);

## 从 XML 文件中创建 Drawable 对象

以 TransitionDrawable 对象为例，假设 XML 文件中的描述如下：

<transition xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:drawable="@drawable/image_expand">
    <item android:drawable="@drawable/image_collapse">
</transition>

从该 XML 文件中获取 TransitionDrawable 对象的代码如下：

```

Resources res=mContext.getResources();
TransitionDrawable transition=(TransitionDrawable);
res.getDrawable(R.drawable.expand_collapse);
ImageView image=(ImageView)findViewById(R.id.toggle_image);
image.setImageDrawable(transition);
```

获取到 TransitionDrawable 对象后，便可以操作该对象，例如：

transition.startTransition(1000);

## 使用构造方法创建 Drawable 对象

以 ShapeDrawable 为例，ShapeDrawable 是 Drawable 的子类，ShapeDrawable 对象适合动态绘制二维图形。

以下代码演示了使用 ShapeDrawable 对象在自定义 View 组件上绘制一个椭圆的过程：

```

public class CustomDrawableView extends View {
    private ShapeDrawable mDrawable;

    public CustomDrawableView(Context context) {
        super(context);

        int x = 10;
        int y = 10;
        int width = 300;
        int height = 50;

        mDrawable = new ShapeDrawable(new OvalShape());
        mDrawable.getPaint().setColor(0xff74AC23);
        mDrawable.setBounds(x, y, x + width, y + height)
    }

    protected void onDraw(Canvas canvas) {
        mDrawable.draw(canvas);
    }
}
```

绘制完成后，可通过以下代码将自定义的 View 组件设置为 Activity 的视图：

```

CustomDrawableView mCustomDrawableView;
protected void onCreate (Bundle savedInstanceState) {
    super.onCreate (savedInstanceState); mCustomDrawableView=new CustomDrawableView (this);

    setContentView (mCustomDrawableView);
}
```

自定义的 View 组件也可以通过以下代码被放置到 XML 文件中使用：

```

<introducton.android.shapedrawable.CustomDrawableView
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
/>
```