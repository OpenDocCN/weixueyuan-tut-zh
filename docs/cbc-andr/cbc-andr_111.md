# Android RenderScript 开发简介

> 原文：[`c.biancheng.net/view/3285.html`](http://c.biancheng.net/view/3285.html)

RenderScript 基于 C99 标准提供了一个平台独立的运行在底层的计算引擎，用于加速需要大量计算的应用程序，常用于 3D 图像渲染。

RenderScript 的主要优点如下。

**1）可移植性**

RenderScript 被设计为在各种具有不同 CPU 和 CPU 架构的设备上运行。由于其代码是在运行设备上进行编译和缓存的，因此 RenderScript 可以支持所有架构而不需要针对某种架构具体编程。

**2）性能**

RenderScript 能够提供与 OpenGL 相似的性能，同时提供与 Android 框架提供的 OpenGL API（android.opengl）相同的移植性。另外，RenderScript 提供 OpenGL 所没有的高性能计算 API。

**3）可用性**

RenderScript 尽可能简化了开发过程。

当然，RenderScript 也有缺点，主要表现在以下方面。

**1）开发复杂**

RenderScript 提供资金的 API 集合，开发者需要重新学习。RenderScript 处理内存的方式与 OpenGL 不同。

**2）调试可见**

RenderScript 可以在其他处理器上被执行，而不是主 CPU 上。在这种情况下，调试变得很困难。

**3）特性较少**

RenderScript 不像 OpenGL 那样提供很多特性，例如压缩纹理格式或者 GL 扩展。

## RenderScript 综述

RenderScript 采用的是主从结构。底层的本地化代码被高层的运行的虚拟机中的 Android 系统控制。

Android 虚拟机保有内存和声明周期的控制权，在需要的时候调用本地的 RenderScript 代码。本地化代码被编译为中间的字节码，并且被打包到应用程序的`.apk`文件中。

当应用程序在设备上运行的时候，字节码被编译为针对当前机器优化的机器码。编译的字节码被缓存起来，因此之后需要使用 RenderScript 代码时不需要重新编译。

RenderScript 有三个层次的代码，允许本地化代码和 Android 框架之间进行通信。

*   本地 RenderScript 层：该层负责密集运算或者图像渲染，相关代码被保存在`.rs`或者`.rsh`文件中。
*   反射层：该层由一系列类组成，这些类由本地代码反射而来。基本上是对本地代码的包装，以允许 Android 框架与本地 RenderScript 代码进行交互。Android Build 工具自动生成该层的相关类。
*   Android 框架层：该层由 Android 框架 API 组成，包括 android.renderscript 包。该层用于给反射层发出高级命令，如“旋转视图”或者“过滤位图”，然后反射层将命令传送给本地层执行。

1）本地 RenderScript 层的关键特性包括：

*   大量针对标量和向量计算的数学函数，包含加、乘、加乘、点乘等。
*   原始数据与向量的转换例程，如矩阵例程、日期和时间例程、图像例程等。
*   日志函数。
*   图形渲染函数。
*   内存分配请求特性。
*   支持 RenderScript 系统的数据类型和结构，例如二维向量、三维向量、四维向量等。

RenderScript 库相关头文件被放置在 <Androidsdk_root>/platform-tools/renderscript/include 目录下。该目录下的头文件会被自动保存进 .rs 文件中，除了 RenderScript 的图像处理头文件。因此，需要使用下面的代码手工导入：

#include"rs_graphics.rsh"

2）反射层是一组由 Android Build 工具生成的类，可以从 Android 虚拟机访问本地的 RenderScript 代码。

反射层定义了 RenderScript 函数和变量的访问点，也提供了构造方法，用于为定义在 RenderScript 代码中的指针分配内存。

下面简单介绍被反射的主要组件。

每个`.rs`文件都生成一个类，被存放在名为 ScriptC_renderscript_filename 的 ScriptC 类型的文件中，它相当于`.rs`文件的 .java 版本，可以被 Android 框架调用。该类包含下列反射：

*   `.rs`文件中的非静态方法。
*   非静态的全局的 RenderScript 变量。
*   全局指针。

3）Android 框架层通常由 Android 框架 API 组成，包含 android.renderscript 包。

该层管理 Activity 的声明周期以及应用程序的内存分配。它通过反射层发送命令给本地 RenderScript 代码，并接收用户事件，按需传递给 RenderScript 代码。

## 使用动态分配的内存

涉及 RenderScript 内存分配 API 的类有三个，分别说明如下。

*   Element：内存分配的基本单位，可以是基本的数据类型或者复合类型。
*   Type：表示要分配的元素个数。
*   Allocation：用于执行分配内存操作。

RenderScript 支持指针，但是必须在 Android 框架代码中为它分配内存。当开发者在 .rs 文件中声明一个全局的指针时，需要通过合适的反射层类来分配内存，并将其绑定到本地的 RenderScript 层。开发者可以通过 Android 框架层和 RenderScript 层读写该内存。

#### 1．定义指针

由于 RenderScript 是使用 C99 开发的，声明指针的方式也和 C99 语法很相似。以下代码声明了一个 Struct 结构，并为其定义了指针，另外还定义了一个指向 int32_t 类型的指针。

```

#pragma version(1)
#pragma rs java_package_name(com.example.renderscript)

...

typedef struct Point{
    float2 point;
}Point_t;

Point_t*touchPoints;
int32_t*intPointer;

...
```

这些代码需要被定义在`.rs`文件中。

## 2．反射指针

全局变量会有对应的 get 和 set 方法生成。一个全局指针会生成一个 bind_pointerName() 方法以代替 set 方法。

该方法允许将 Android 虚拟机分配的内存绑定到本地的 RenderScript。以下代码是为前面代码定义的两个指针生成存取方法的代码：

```

private ScriptField_Point mExportVar_touchPoints;

public void bind_touchPoints (ScriptField_Point v) {
    mExportVar_touchPoints=v;
    if (v==null) bindAllocation (null, mExportVarIdx_touchPoints);
    else bindAllocation (v.getAllocation(), mExportVarIdx_touchPoints);
}

public ScriptField_Point get_touchPoints(){
    return mExportVar_touchPoints;
}

private Allocation mExportVar_intPointer;
public void bind_intPointer (Allocation v) {   
    mExportVar_intPointer=v;
    if (v==null) bindAllocation (null, mExportVarIdx_intPointer);
    else bindAllocation (v, mExportVarIdx_intPointer);
}
public Allocation get_intPointer(){
    return mExportVar_intPointer;
}
```

这些代码应该被定义在 ScriptC_rs_filename 文件中。

#### 3．分配并绑定内存到 RenderScript

当 Build 工具生成反射层类后，就可以使用合适的反射层为指针分配内存。以下代码演示了为 intPointer 和 touchPoints 两个指针分配内存并绑定到 RenderScript 的方法：

```

private RenderScriptGL glRenderer;
private ScriptC_example script;
private Resources resources;

public void init(RenderScriptGL rs,Resources res){

    //get the rendering context and resources from the calling method
    glRenderer=rs;
    resources=res;

    //allocate memory for the struct pointer, calling the constructor
    ScriptField_Point touchPoints=new ScriptField_Point (glRenderer, 2);

    //Create an element manually and allocate memory for the int pointer
    intPointer=Allocation.createSized (glRenderer, Element.132 (glRenderer) , 2);

    //create an instance of the RenderScript, pointing it to the bytecode resource
    mScript=new ScriptC_example (glRenderer, resources, R.raw.example);

    // bind the struct and int pointers to the RenderScript
    mScript.bind_touchPoints (touchPoints);
    script.bind_intPointer (intPointer);

    //bind the RenderScript to the rendering context
    glRenderer.bindRootScript (script);
}
```

#### 4．读写内存

虽然内存是由 Android 虚拟机分配的，但是在本地的 RenderScript 代码和 Android 代码中都可以对内存进行读写。

一旦内存被绑定，本地 RenderScript 代码就可以直接访问内存，反射层类也可以通过读写方法访问内存。

若在 Android 框架层中修改了内存内容，则会自动同步到本地层。若在`.rs`文件中修改了内存内容，则这些改变不会传递回 Android 框架层。

以下代码演示了在 Android 代码中修改 Struct 的方法：

```

int index=0;
boolean copyNow=true;
Float2 point=new Float2(0.0f, 0.0f);
touchPoints.set_point(index, point, copyNow);
```

在本地 RenderScript 代码中读取该内存的代码如下：

rsDebug("Printing out a Point", touchPoints[0].point.x, touchPoints[0].point.y);

## 使用静态分配的内存

在 RenderScript 中声明的非静态全局原始数据类型和结构体很容易使用，因为这些内存是静态分配的。Android Build 工具在生成反射层类时会自动为这些变量生成存取方法，开发者可以通过这些方法来使用静态分配的内存。

例如，在 RenderScript 代码中声明如下变量：

uint32_t unsignedInteger=1;

以下代码会在 ScriptC_script_name.java 文件中被生成：

```

private final static int mExportVarIdx_unsignedInteger=9;
private long mExportVar_unsignedInteger;
public void set_unsignedInteger (long v) {
    mExportVar_unsignedInteger=v;
    setVar(mExportVarIdx_unsignedInteger,v);
}
public long get_unsignedInteger(){
    return mExportVar_unsignedInteger;
}
```

以下代码来自 ScriptField_Point.java，显示的是从 Point 结构体生成的反射层的类：

```

package com.example.renderscript;

import android.renderscript.*;
import android.content.res.Resources;

public class ScriptField_Point extends android.renderscript.Script.FieldBase {
    static public class Item {
        public static final int sizeof = 8;
        Float2 point;

        Item() {
            point = new Float2();
        }
    }

    private Item mItemArray[];
    private FieldPacker mIOBuffer;

    public static Element createElement(RenderScript rs) {
        Element.Builder eb = new Element.Builder(rs);
        eb.add(Element.F32_2(rs), "point");
        return eb.create();
    }

    public ScriptField_Point(RenderScript rs, int count) {
        mItemArray = null;
        mIOBuffer = null;
        mElement = createElement(rs);
        init(rs, count);
    }

    public ScriptField_Point(RenderScript rs, int count, int usages) {
        mItemArray = null;
        mIOBuffer = null;
        mElement = createElement(rs);
        init(rs, count, usages);
    }

    private void copyToArray(Item i, int index) {
        if (mIOBuffer == null) mIOBuffer = new FieldPacker(Item.sizeof * getType().getX()
             /* count */);
        mIOBuffer.reset(index * Item.sizeof);
        mIOBuffer.addF32(i.point);

    }

    public void set(Item i, int index, boolean copyNow) {
        if (mItemArray == null) mItemArray = new Item[getType().getX()/* count */];
        mItemArray[index] = i;
        if (copyNow) {
            copyToArray(i, index);
            mAllocation.setFromFieldPacker(index, mIOBuffer);
        }
    }

    public Item get(int index) {
        if (mItemArray == null) return null;
        return mItemArray[index];
    }

    public void set_point(int index, Float2 v, boolean copyNow) {
        if (mIOBuffer == null) mIOBuffer = new FieldPacker(Item.sizeof * getType().getX()) fnati;
        if (mItemArray == null) mItemArray = new Item[getType().getX()/* count */];
        if (mItemArray[index] == null) mItemArray[index] = new Item();
        mItemArray[index].point = v;
        if (copyNow) {
            mIOBuffer.reset(index * Item.sizeof);
            mIOBuffer.addF32(v);
            FieldPacker fp = new FieldPacker(8);
            fp.addF32(v);
            mAllocation.setFromFieldPacker(index, 0, fp);
        }
    }

    public Float2 get_point(int index) {
        if (mItemArray == null) return null;
        return mItemArray[index].point;
    }

    public void copyAll() {
        for (int ct = 0; ct < mItemArray.length; ct++) copyToArray(mItemArray[ct], ct);
        mAllocation.setFromFieldPacker(0, mIOBuffer);
    }

    public void resize(int newSize) {
        if (mItemArray != null) {
            int oldSize = mItemArray.length;
            int copySize = Math.min(oldSize, newSize);
            if (newSize == oldSize) return;
            Item ni[] = new Item[newSize];
            System.arraycopy(mItemArray, 0, ni, 0, copySize);
            mItemArray = ni;
        }
        mAllocation.resize(newSize);
        if (mIOBuffer ! = null)mIOBuffer = new FieldPacker(Item.sizeof * getType().getX());
    }
}
```