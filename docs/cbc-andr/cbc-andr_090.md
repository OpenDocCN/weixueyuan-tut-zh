# Android SIP 开发教程

> 原文：[`c.biancheng.net/view/3217.html`](http://c.biancheng.net/view/3217.html)

Android 系统提供了支持 SIP（Session Initiation Protocol）的 API，允许开发者添加基于 SIP 的因特网电话特性到自己的应用程序中。

Android 包含一个完整的 SIP 协议栈，整合了允许轻松创建来电和去电的电话管理服务，而不必开发者直接参与管理会话、传输层通信、音频录制等工作。

目前 SIP 已经被成功应用于视频会议和即时消息中，其应用程序开发需要基于 Android 2.3（API Level 9）以上的系统。

SIP 运行于无线数据连接，通过 AVD 无法调试。在 SIP 应用程序通信会话中，每一个参与者都必须拥有一个 SIP 账号。

## 相关 API

Android SDK 中与 SIP 开发相关的类和接口被放置在 android.net.sip 包中，相关类和接口介绍如下。

| 类/接口 | 作用 |
| --- | --- |
| SipAudioCall | 用于处理基于 SIP 的因特网音频呼叫。 |
| SipAudioCall.Listener | 用于处理 SIP 呼叫事件，如接收到呼叫和对外呼叫事件。 |
| SipErrorCode | 定义了 SIP 行为的错误代码。 |
| SipManager | 提供了 SIP 任务的相关 API，例如初始化 SIP 连接，提供对相关 SIP 服务的访问等。 |
| SipProfile | 定义了一个 SIP 配置文件，包括 SIP 账户、域和服务器信息等。 |
| SipProfile.Builder | 创建 SIP 配置信息的帮助类。 |
| SipSession | 代表一个与 SIP 对话框相关联的 SIP 会话或者一个单独的无对话框的事务。 |
| SipSession.Listener | 针对 SIP 会话事件的监听器，例如会话被注册或者一个电话正在呼出事件。 |
| SipSession.State | 定义了 SIP 会话的状态信息，例如注册、呼出、呼入等。 |
| SipRegistrationListener | 一个用于监听 SIP 注册事件的接口。 |

## Manifest 文件配置

要开发基于 SIP 的应用程序，必须使用 Android 2.3 以上版本的设备，但是并不是所有 Android 2.3 以上版本的设备都支持 SIP 应用程序。

为应用程序添加 SIP 支持需要在应用程序的配置文件 AndroidManifest.xml 中添加如下内容。

**1）添加使用 SIP 和因特网的权限：**

<uses-permission android:name="android.permission.USE_SIP" />
<uses-permission android:name="android.permission.INTERNET" />

**2）确保应用程序只可以被安装在支持 SIP 的设备上，在 Manifest 文件中添加以下代码：**

<uses-sdk android:minSdkVersion="9" />
<uses-feature android:name="android.hardware.sip.voip" />

**3）如果应用程序被设计为接收呼叫，则必须定义一个 receiver:**

<receiver android:name=".IncomingCallReceiver" android:label="Call Receiver"/>

应用程序的 Manifest 文件示例代码如下：

```

<?xml version="l.0" encoding="utf-8"?>

<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="com.example.android.sip">
    ...
    <receiver android:name=".IncomingCallReceiver" android:label="Call Receiver"/>
    ...
    <uses-sdk android:minSdkVersion="9" />
    <uses-permission android:name="android.permission.USE_SIP" />
    <uses-permission android:name="android.permission.INTERNET" />
    ...
    <uses-feature android:name="android.hardware.sip.voip" android:required="true" />
    <uses-feature android:name="android.hardware.wifi" android:required="true" />
    <uses-feature android:name="android.hardware.microphone" android:required="true" />
</manifest>
```

## 创建 SipManager 对象

要使用 SIP API，必须创建 SipManager 示例。SipManager 用于处理：

*   初始化 SIP 会话。
*   初始化并接收呼叫。
*   对 SIP 提供者进行注册和注销。
*   核实会话连接。

创建 SipManager 对象的代码如下：

```

public SipManager mSipManager = null;
...
if(mSipManager == null){
    mSipManager = SipManager.newInstance(this);
}

```

## 注册 SIP 服务器

在典型的 Android SIP 应用程序中包含一个或多个用户，每个用户都必须有一个 SIP 账户。在 SIP 应用程序中，SIP 账户用 SipProfile 对象表示。

SipProfile 定义了 SIP 配置简表，包括 SIP 账户以及域和服务器信息。与运行应用程序的设备上的 SIP 账户相关联的配置简表叫作本地简表，会话连接到的简表叫作对等简表。

当 SIP 应用程序使用本地 SipProfile 登录到 SIP 服务器时，SipProfile 帮助 SIP 服务器高效地将当前设备注册为 SIP 呼叫的目的地。

创建 SipProfile 对象的代码如下：

```

public SipProfile mSipProfile = null;
...

SipProfile.Bulider bulider = new SipProfile(username,domain);
bulider.setPassword(password);
mSipProfile = builder.bulid();
```

下面代码中的 SipManager 打开本地简表，用于拨打或者接收 SIP 呼叫：

```

Intent intent - new Intent();
intent.setAction("android.SipDemo.INCOMING_CALL");
PendingIntent pendingIntent = PendingIntent.getBroadcast(this,0,intent,Intent.FILL_IN_DATA);
mSipManager.open(mSipProfile,pendingIntent,null);
```

下面的代码为 SipManager 注册了 SipRegistrationListener 接口，该接口用于跟踪 SipProfile 是否在 SIP 服务提供者处成功注册。

```

SipManager.setRegistrationListener (mSipProfile.getUriString(), new SipRegistrationListener(){

    public void onRegistering (String localProfileUri) {
        updateStatus ("Registering with SIP Server...");
    }

    public void onRegistrationDone (String localProfileUri, long expiryTime){
        updateStatus ("Ready");
    }

    public void onRegistrationFailed (String localProfileUri, int errorCode, String errorMessage) {
        updateStatus ("Registration failed. Please check settings.");
    }
```

下面的代码演示了配置简表使用结束后，如何关闭简表，并从服务器注销设备信息。

```

public void closeLocalProfile(){
    if (mSipManager==null) {
        return;
    }
    try {
        if (mSipProfile !=null) {
            mSipManager.close (mSipProfile.getUriString());
        }
    } catch (Exception e) {
        Log.d ("WalkieTalkieActivity/onDestroy", "Failed to close local profile.", e);
    }
}
```

## 拨打音频电话

要使用 SIP 拨打语音电话，需要满足如下条件：

*   一个 SipProfile 对象，用于拨打电话；一个有效的 SIP 地址，用于接收电话。
*   一个 SipManager 对象。

为了拨打音频电话，需要创建 SipAudioCall.Listener 对象。

大部分的客户端与 SIP 栈之间的交互都是通过接口进行的。下面的代码演示了呼叫建立后接口如何进行处理：

```

SipAudioCall.Listener listener=new SipAudioCall.Listener(){

    @Override
    public void onCallEstablished (SipAudioCall call) {
        call.startAudio();
        call.setSpeakerMode (true);
        call.toggleMute();
        ...
    }

    @Override
    public void onCallEnded (SipAudioCall call) {
        // Do something.
    }
};
```

SipAudioCall.Listener 接口创建后，通过 SipManager.makeAudioCall() 方法进行音频呼叫。该方法有 4 个参数，分别是：

*   本地 SIP 配置简表（呼叫者）。
*   对等 SOP 配置简表（被呼叫者）。
*   SipAudioCall.Listener 接口。
*   超时时间，单位是秒。

进行音频呼叫的代码如下：

call=mSipManager.makeAudioCall(mSipProfile.getUriString(),sipAddress, listener, 30);

## 接收呼叫

为了接收呼叫，SIP 应用程序必须包含一个 BroadcastReceiver 的子类，以便当有来电时用于对 Intent 对象进行处理。为此，需要在应用程序中完成以下几步：

1）在 AndroidManifest.xml 文件中声明 <receiver>，例如：

<receiver android:name=".IncomingCallReceiver" android:label="Call Receiver"/>2）

2）实现声明的 BroadcastReceiver 的子类，例如 IncomingCallReceiver。
3）使用 PendingIntent 对象初始化本地 SipProfile。当有来电时，该 PendingIntent 会启动 BroadcastReceiver 的子类。
4）设置 Intent Filter，用于过滤来电时产生的 Intent。

下面的代码定义了一个用于处理来电的 BroadcastReceiver：

```

/*** Listens for incoming SIP calls, intercepts and hands them off to WalkieTalkieActivity. */

public class IncomingCallReceiver extends BroadcastReceiver {

    /**
     *Processes the incoming call, answers it, and hands it over to the
     *WalkieTalkieActivity.
     *@param context The context under which the receiver is running.
     *Sparam intent The intent being received.
     */
    @Override
    public void onReceive (Context context, Intent intent) {
        SipAudioCall incomingCall=null;
        try {
            SipAudioCall.Listener listener=new SipAudioCall.Listener(){
            @Override
            public void onRinging (SipAudioCall call, SipProfile caller) {
            try {
                call.answerCall (30) ;
            } catch (Exception e) {
                e.printStackTrace ();
            }
        }
    };

    WalkieTalkieActivity wtActivity= (WalkieTalkieActivity) context; 
    incomingCall=wtActivity.mSipManager.takeAudioCall (intent, listener);

    incomingCall.answerCall (30);
    incomingCall.startAudio();
    incomingCall.setSpeakerMode (true);
    if (incomingCall.isMuted()) {
        incomingCall.toggleMute();
    }
    wtActivity.call=incomingCall; wtActivity.updateStatus (incomingCall);
    } catch (Exception e) {
        if (incomingCall !=null) {
            incomingCall.close();
        }
    }
}
```

设置用于接收来电的 Intent Filter 的相关代码如下：

```

public SipManager mSipManager=null;
public SipProfile mSipProfile=null;
...

Intent intent=new Intent();
intent.setAction ("android.SipDemo.INCOMING_CALL"); PendingIntent pendingIntent=PendingIntent.getBroadcast (this, 0,intent,Intent.FILL_IN_DATA);
mSipManager.open (mSipProfile, pendingIntent, null);
```

Intent Filter 信息可以在应用程序的 Manifest 文件中被注册，也可以像下面的代码演示的那样在 Activity 的 onCreate() 方法中被注册。相关代码如下：

```

public class WalkieTalkieActivity extends Activity implements View.OnTouchListener {
    ...
    public IncomingCallReceiver callReceiver;
    ...
    @Override
    public void onCreate (Bundle savedInstanceState) {
        IntentFilter filter=new IntentFilter();
        filter.addAction ("android.SipDemo.INCOMING_CALL");
        callReceiver=new IncomingCallReceiver();
        this.registerReceiver (callReceiver, filter);
        ...
    }
    ...
}
```