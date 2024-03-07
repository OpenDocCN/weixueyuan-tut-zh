# Android WIFI Direct 开发教程

> 原文：[`c.biancheng.net/view/3192.html`](http://c.biancheng.net/view/3192.html)

创建一个 WIFI Direct 应用程序，包括发现连接点、请求连接、建立连接、发送数据，以及建立对该应用程序广播的 Intent 进行接收的 BroadcastReceiver，需要经过以下步骤。

#### 1\. 创建 BroadcastReceiver

需要注意的是，要在 BroadcastReceiver 的构造方法中传入 WifiP2pManager、WifiP2pManager.Channel 以及注册该 BroadcastReceiver 的 Activity 的对象，以便在 BroadcastReceiver 中访问 WIFI 硬件设备并对 Activity 进行更新。

创建 BroadcastReceiver 的代码如下：

```

public class WiFiDirectBroadcastReceiver extends BroadcastReceiver {

    private WifiP2pManager manager;
    private Channel channel;
    private MyWiFiActivity activity;

    public WiFiDirectBroadcastReceiver (WifiP2pManager manager, Channel channel,MyWifiActivity activity) { 
        super();
        this.manager=manager;
        this.channel=channel;
        this.activity=activity;
    }
    @Override
    public void onReceive(Context context, Intent intent) {
        String action=intent.getAction();

        if (WifiP2pManager.WIFI_P2P_STATE_CHANGED_ACTI0N.equals (action)) {
            //检测 WIFI 功能是否被打开
        } else if (WifiP2pManager.WIFI_P2P_PEERS_CHANGED_ACTION.equals (action)) {
            //获取当前可用连接点的列表
        } else if (WifiP2pManager.WIFI_P2P_CONNECTION_CHANGED_ACTION.equals (action)) {
            //建立或者断开连接
        } else if (WifiP2pManager.WIFI_P2P_THIS_DEVICE_CHANGED_ACTION.equals (action)) {
            //当前设备的 WIFI 状态发生变化
        }
    }
}
```

#### 2\. 初始化操作

**1）修改 AndroidManifest.xml 文件**

指定支持 WIFI Direct 的 Android SDK 的最小版本并增加使用 WIFI Direct 的相应权限，代码如下：

```

<uses-sdk android:minSdkVersion="14" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />
<uses-permission android:name="android.permission.CHANGE_NETWORK_STATE" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

**2）确认当前设备是否支持并且打开了 WIFI Direct 功能**

相关代码应该被放在 BroadcastReceiver 的 onReceive() 方法中。实例代码如下：

```

public void onReceive (Context context, Intent intent) {
    ...
    String action=intent.getAction();

    if (WifiP2pManager.WIFI_P2P_STATE_CHANGED_ACTI0N.equals (action)) {
        int state=intent.getIntExtra (WifiP2pManager.EXTRA_WIFI_STATE, -1);
        if (state==WifiP2pManager.WIFI_P2P_STATE_ENABLED) {
            // Wifi Direct is enabled
        } else {
            // Wi-Fi Direct is not enabled
        }
    }
    ...
}
```

**3）在 Activity 的 onCreate() 方法中创建对象**

创建 WifiP2pManager 和 Channel 对象，并创建 BroadcastReceiver 对象，代码如下：

```

WifiP2pManager mManager;
Channel mChannel;
BroadcastReceiver mReceiver;
...

@Override
protected void onCreate (Bundle savedInstanceState) {
    ...
    mManager= (WifiP2pManager) getSystemService (Context.WIFI_P2P_SERVICE);
    mChannel=mManager.initialize (this, getMainLooper(), null);
    mReceiver=new WiFiDirectBroadcastReceiver (manager, channel, this);
    ...
}
```

**4）创建 BroadcastReceiver 要使用的 IntentFilter 对象**

代码如下：

```

IntentFilter mIntentFilter;
...
@Override
protected void onCreate (Bundle savedInstanceState) {    
    ... 
    mIntentFilter=new IntentFilter();
    mIntentFilter.addAction (WifiP2pManager.WIFI_P2P_STATE_CHANGED_ACTI0N);
    mIntentFilter.addAction (WifiP2pManager.WIFI_P2P_PEERS_CHANGED_ACTI0N);
    mIntentFilter.addAction (WifiP2pManager.WIFI_P2P_C0NNECTI0N_CHANGED_ACTI0N);
    mIntentFilter.addAction (WifiP2pManager.WIFI_P2P_THIS_DEVICE_CHANGED_ACTI0N);
    ...
}
```

**5）在 Activity 的 onResume() 方法中注册 BroadcastReceiver 对象，在 onPause() 方法中注销对象**

代码如下：

```

@Override
protected void onResume(){
    super.onResume();
    registerReceiver(mReceiver, mIntentFilter);
}

/*unregister the broadcast receiver */
@Override
protected void onPause(){
    super.onPause();
    unregisterReceiver(mReceiver);
}
```

#### 3\. 使用 WifiP2pManager.discoverPeers() 方法获取可以连接点的列表

示例代码如下：

```

manager.discoverPeers (channel, new WifiP2pManager.ActionListener() {
    @Override
    public void onSuccess(){
        ...
    }

    @Override
    public void onFailure (int reasonCode) {
        ...
    }
});
```

若成功搜寻到可以连接的点，则 WIFI Direct 系统框架会广播一个带有 WIFI_P2P_ PEERS_CHANGED_ACTION 信息的 Intent，该 Intent 会被之前定义的 BoradcastReceiver 接收，并获得可以连接点的列表。示例代码如下：

```

PeerListListener myPeerListListener;
...
if (WifiP2pManager.WIFI_P2P_PEERS_CHANGED_ACTION.equals (action)) {
    if (manager !=null) {
        manager.requestPeers (channel, myPeerListListener);
    }
}
```

#### 4\. WifiP2pManager.connect() 方法可以与列表中的某个连接点设备建立连接，该方法通过 WifiP2pConfig 对象获得连接设备的相关信息

示例代码如下：

```

WifiP2pDevice device;
WifiP2pConfig config=new WifiP2pConfig();
config.deviceAddress=device.deviceAddress;
manager.connect (channel, config, new ActionListener () {

    @Override
    public void onSuccess(){
        //success logic
    }

    @Override
    public void onFailure (int reason) {
        //failure logic
    }
});
```

#### 5\. 连接建立后，就可以用两个设备直接通过 Socket 进行数据传输

其传输过程与之前讲解的 Socket 通信完全相同，基本步骤如下：

1）在其中一个设备上建立 ServerSocket 对象，监听特定端口，并堵塞应用程序，直到有连接请求。

2）在另一个设备上建立 Socket 对象，通过 IP 地址和端口向 ServerSocket 发出连接请求。

3）ServerSocket 监听到连接请求后，调用 accept() 方法建立连接。

4）连接建立后，Socket 对象可以通过字节流在两个设备间直接进行数据传递。

下面的示例代码演示了通过 ServerSocket 和 Socket 在客户端和服务器间直接传递 JPG 图像的过程。

服务器代码如下：

```

public static class FileServerAsyncTask extends AsyncTask {
    private Context context;
    private TextView statusText;

    public FileServerAsyncTask(Context context, View statusText) {
        this.context = context;
        this.statusText = (TextView) statusText;
    }

    @Override
    protected String doInBackground(Void... params) {
        try {
            //创建 ServerSocket 对象，监听 8888 端口，等待客户连接
            ServerSocket serverSocket = new ServerSocket(8888);
            Socket client = serverSocket.accept();
            //建立连接成功，开始传送数据
            final File f = new File(Environment.getExternalStorageDirectory() + "/"
                    + context.getPackageName() + "/wifip2pshared-" + System.currentTimeMillis() + ".jpg");
            File dirs = new File(f.getParent());
            if (!dirs.exists())
                dirs.mkdirs();
            f.createNewFile();
            InputStream inputstream = client.getlnputStream();
            copyFile(inputstream, new FileOutputStream(f));
            ServerSocket.close();
            return f.getAbsolutePath();
        } catch (IOException e) {
            Log.e(WiFiDirectActivity.TAG, e.getMessage());
            return null;
        }
    }

    //启动用于显示图像的 Activity
    @Override
    protected void onPostExecute(String result) {
        if (result != null) {
            statusText.setText("File copied - " + result);
            Intent intent = new Intent();
            intent.setAction(android.content.Intent.ACTION_VIEW);
            intent.setDataAndType(Uri.parse("file://" + result),"image/*");
            context.startActivity(intent);
        }
    }
}
```

客户端的相关代码如下：

```

Context context=this.getApplicationContext();
String host;
int port;
int len;
Socket socket=new Socket();
byte buf[]=new byte[1024];
...
try{
    //创建 Socket 对象,并请求连接
    socket.bind (null);
    socket.connect((new InetSocketAddress(host,port)),500);

    //连接建立成功，开始传输数据
    OutputStream outputStream=socket.getOutputStream();
    ContentResolver cr=context.getContentResolver();
    Inputstream inputStream=null;
    inputstream=cr.openlnputStream(Uri.parse("path/to/picture.jpg"))while((len=inputStream.read(buf))!=-l){outputStream.write(buf,0,len);
    outputStream.close();
    inputstream.close();
}catch(FileNotFoundException e){
    //catch logic
} catch (IOException e) {
    //catch logic
}
//关闭连接
finally{
    if(socket!=null){
        if(socket.isConnected()){
            try{
                socket.close();
            }catch(IOException e){
                //catch logic
            }
        }
    }
}
```