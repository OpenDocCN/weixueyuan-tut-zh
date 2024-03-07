# Android 系统的进程和线程

> 原文：[`c.biancheng.net/view/2950.html`](http://c.biancheng.net/view/2950.html)

当一个应用组件启动，并且该应用没有别的正在运行的组件时，则 Android 系统会为这个应用程序创建一个包含单个线程的 linux 进程。

某个应用组件启动，如果该应用程序的进程已经存在（因为应用程序的其他组件已经在运行了），那么刚刚启动的组件会在已有的进程和线程中启动运行。不过，可以指定组件运行在其他进程中，也可以为任何进程创建其他的线程。

本节主要讨论进程和线程是如何在 Android 应用程序中发挥作用的。

## 进程

默认情况下，同一个应用程序内的所有组件都是运行在同一个进程中的，大部分应用程序也不会去改变它。不过，如果需要指定某个特定组件所属的进程，那么可以利用 manifest 文件来达到目的。

manifest 文件中的每种组件元素（<activity>、 <service>、 <receiver> 和 <provider>）都支持 android:process 属性，用于指定组件所属运行的进程。以下两点是设置 android:process 属性的好处：

*   可实现每个组件在各自的进程中运行，或者某几个组件共享一个进程而其他组件不可以参与。
*   可让来自于不同应用程序的组件运行在同一个进程中，实现多个应用程序共享同一个 Linux 的 user ID，并且提供相同的签名认证。

<application> 元素也支持 android:process 属性，用于指定所有组件的默认值。

如果内存不足，且又有其他为用户提供更紧急服务的进程需要更多内存时，Android 可能会决定关闭一个进程。在此进程中运行着的应用程序组件也会因此被销毁。当需要再次工作时，会为这些组件重新创建一个进程。

在决定关闭哪个进程的时候，Android 系统会权衡它们对于用户的重要程度。比如，相对于一个拥有可视 Activity 的进程，更有可能去关闭一个持有一组不再对用户可见的 Activity 的进程。也就是说，是否终止一个进程，取决于运行在此进程中组件的状态。终止进程的判定规则将在后续内容中讨论。

注：一个进程的关闭级别，按照该进程中最高的级别来定义，如该进程中有 Activity 和 Service，那么该进程的级别为 Service。

Android 系统试图尽可能长时间地保持应用程序进程，但为了新建的或者更为重要的进程，总是需要清除旧的进程以回收内存。

为了决定保留或终止哪个进程，根据进程内运行的组件及这些组件的状态，系统把每个进程都划入一个“importance hierarchy”中。重要性最低的进程首先会被清除，然后是其次低的进程，以此类推，这都是回收系统资源所必需的。

“importance hierarchy”共有 5 级，下面按照重要程度列出了各类进程（第一类进程是最重要的，最后一个被终止）。

#### 1) 前台进程（Foreground Process）

用户正在请求的进程。当以下任何一个条件成立时，该进程被认为是前台进程：

*   持有一个用户正在与之交互的 Activity（Activity 对象的 onResume() 方法已被调用）。
*   持有一个服务（Service），且该服务已被绑定到一个正在与用户交互的 Activity 上。
*   持有一个服务，且该服务在前台运行，即该服务 startForground() 调用。
*   持有一个服务，且该服务正在执行其生命周期的回调方法（onCreate()、onStart()、onDestroy()）。
*   持有一个 BroadcastReceiver，且其正在执行 onReceive() 方法。

通常，在一个给定的时间内，只有很少的前台进程存在。当系统内存匮乏，以至于它们不能全部继续运行时，它们会依序被清除。通常，这时设备已经到了内存分页状态（memory paging state），来清除那些前台进程以确保用户响应。

#### 2) 可视进程（Visible Process）

一个可视进程是没有前台组件的，但仍会影响用户在屏幕上所见内容的进程。当以下任何一个条件成立时，该进程被认为是可视进程：

*   持有一个 Activity，且该 Activity 没有处于前台，但是对于用户而言它仍然可见（onPause() 方法被调用）。这是可能发生的，例如，一个前台 Activity 启动了一个对话框，而之前的 Activity 还允许显示在后面。
*   持有一个服务（Service），且该服务被绑定到一个可视（或一个前台）Activity。

一个可视进程是极其重要的，除非无法维持所有前台进程同时运行了，否则它们是不会被终止的。

#### 3) 服务进程（Service Process）

运行由 startService() 方法启动的服务，它不会升级为上述两个级别。

尽管服务进程不直接和用户所见内容关联，但它们通常在执行一些用户关心的操作（比如在后台播放音乐或从网络下载数据）。因此，除非内存不足以维持所有前台、可视进程同时运行。其余情况，系统则会保持服务进程的运行。

#### 4) 后台进程（Background Process）

一个后台进程持有一个对用户不可见的 Activity（Activity 对象的 onStop() 方法已被调用）。

这些进程对用户体验没有直接的影响，系统可能在任意时间终止它们，以回收内存供前台进程、可视进程及服务进程使用。通常会有许多后台进程运行，所以它们被保存在一个 LRU（Least Recently Used）列表中，以确保最近被用户使用的 Activity 最后一个被终止。

如果一个 Activity 正确实现了生命周期方法，并保存了当前的状态，则终止此类进程不会对用户体验产生显着的影响。因为当用户回到这个 Activity，这个 Activity 会恢复它所有可视的状态。

关于保存和恢复状态的详细信息，可参阅 [`blog.csdn.net/sinat_33921105/article/details/78631823`](https://blog.csdn.net/sinat_33921105/article/details/78631823)。

#### 5) 空进程（Empty Process）

空进程不含任何活动应用程序组件。

保留空进程的唯一目的就是用作缓存，以改善下次在此进程中运行组件的启动时间。为了在进程缓存和内核缓存间平衡系统整体资源，系统经常会终止这种进程。

依据进程中目前活跃组件的重要程度，Android 会给进程评估一个尽可能高的等级。

比如，一个进程拥有一个服务和一个用户可见的 Activity，则此进程会被评定为可视进程，而不是服务进程。

此外，一个进程的等级可能会由于其他进程的依赖而被提高，一个服务于另一个进程的进程永远不能比另一个进程的等级低。

比如，进程 A 中的 content provider 为进程 B 中的客户端提供服务，或进程 A 中的服务被进程 B 中的组件所调用，则进程 A 被认为其重要等级不低于进程 B。

因为运行服务的进程级别高于后台 Activity 进程的等级，所以，如果 Activity 需要启动一个长时间运行的操作，则为其启动一个服务（Service）会比简单地创建一个工作线程更好些，尤其是在此操作时间比 Activity 本身存在时间还要长久的情况下。

比如，一个 Activity 要把图片上传至 Web 网站，就应该创建一个服务来执行，即使用户离开此 Activity，上传还是会在后台继续运行。无论 Activity 发生什么情况，使用服务可以保证操作至少拥有服务进程（service process）的优先级。同理，前面的广播接收器也是使用服务而非简单地启用一个线程。

## 线程

应用程序启动时，系统会为它创建一个名为“main”的主线程。

主线程非常重要，因为它负责分配事件到合适的用户接口，包括绘图事件。它也是应用程序与 Android UI 组件包（来自 android.widget 和 android.view 包）进行交互的线程。因此，主线程有时也被叫作 UI 线程。

系统并不会为每个组件的实例创建单独的线程。

运行于同一个进程中的所有组件都是在 UI 线程中实例化的，对每个组件的系统调用也都是由 UI 线程分配的。因此，对系统回调进行响应的方法（比如报告用户操作的 onKeyDown() 或生命周期回调方法）总是运行在 UI 线程中。

例如，当用户触摸屏幕上的按钮时，应用程序的 UI 线程会把触摸事件分发给 widget，widget 先把自己置为按下（pressed）状态，再发送一个显示区域已失效（invalidate）的请求到事件队列中。UI 线程从队列中取出此请求，并通知 widget 重绘自己。

如果应用程序在与用户交互的同时需要执行繁重密集的任务，单线程模式可能会导致运行性能很低下，除非应用程序的执行时机很合适。

如果 UI 线程需要处理每一件事情，那些耗时很长的操作（诸如访问网络或查询数据库等）将会阻塞整个 UI（线程）。一旦线程被阻塞，所有事件都不能被分发，包括屏幕绘图事件。

从用户的角度来看，应用程序看上去似乎被挂起了。更糟糕的是，如果 UI 线程被阻塞超过一定时间（目前设置大约是 5 秒钟），用户就会被提示那个可恶的“应用程序没有响应”(ANR)。如果引起用户不满，可能就会决定退出并删除这个应用程序。

此外，Android 的 UI 组件包并不是线程安全的，因此不允许从工作线程中操作 UI，只能从 UI 线程中操作用户界面。因此，Android 的单线程模式必须遵守两个规则：

*   不允许阻塞 UI 线程。
*   不允许在 UI 线程之外访问 Android 的 UI 组件包。

根据以上对单线程模式的描述，要想保证程序界面的响应能力，关键是不能阻塞 UI 线程。如果操作不能很快完成，就让它们在单独的线程中运行（“后台”或“工作”线程）。

例如，以下是响应鼠标单击的代码，实现了在单独线程中下载图片并在 ImageView 显示的功能。

```

public void onClick(View v){
    new Thread(new Runnable(){
        public void run(){
            Bitmap b = loadImageFromNetwork("http://example.com/image.png");
            mImageView.setImageBitmap(b);
        }

    }).start();
}
```

首先，因为创建了一个新的线程来处理访问网络的操作，这段代码似乎能运行得很好。可是它违反了单线程模式的第二条规则，即不要在 UI 线程之外访问 Android 的 UI 组件包。 这个例子在工作线程里而不是 UI 线程里修改了 ImageView，这可能导致不明确、不可预见的后果，要跟踪这种情况也是很困难很耗时的。

为了解决以上问题，Android 提供了几种方法，从其他线程中访问 UI 线程。下面列出了有助于解决问题的几种方法：

*   Activity.runOnUiThread（可运行）
*   View.post（可运行）
*   View.postDelayed（Runnable 接口，长）

例如，可以使用 View.post(Runnable) 方法来修正上面的代码：

```

public void onClick(View v) {
    new Thread(new Runnable() {
        public void run() {
            final Bitmap bitmap = loadImageFromNetwork("http://example.com/image.png");
            mImageView.post(new Runnable() {
                public void run() {
                    mImageView.setImageBitmap(bitmap);
                }
            });
        }
    }).start();
}
```

现在，这段代码的执行是线程安全的了。网络相关的操作在单独的线程里完成，而 ImageView 是在 UI 线程里操纵的。

不过，随着操作变得越来越复杂，这类代码也会变得复杂难以维护。

为了用工作线程完成更加复杂的交互处理，可以考虑在工作线程中用 Handler 来处理 UI 线程分发过来的消息。当然，最好的解决方案也许就是继承使用异步任务类 AsyncTask，此类简化了一些工作线程和 UI 交互的操作。

#### 使用异步任务（AsyncTask）

异步任务（AsyncTask）允许以异步的方式对用户界面进行操作。它先阻塞工作线程，再在 UI 线程中呈现结果，在此过程中不需要对线程和 Handler 进行人工干预。

要使用异步任务，必须继承 AsyncTask 类并实现 doInBackground() 回调方法，该对象将运行于一个后台线程池中。

要更新 UI 时，需实现 onPostExecute() 方法来分发 doInBackground() 返回的结果。由于此方法运行在 UI 线程中，因此能够安全地更新 UI。然后就可以在 UI 线程中调用 execute() 来执行任务了。

例如，可以利用 AsyncTask 来实现上面的例子：

```

public void onClick(View v) {
    new DownloadImageTask().execute("http://example.com/image.png");
}
private class DownloadImageTask extends AsyncTask<String, Void, Bitmap> {
    /** The system calls this to perform work in a worker thread and delivers it the parameters given to AsyncTask.execute() */
    protected Bitmap doInBackground(String... urls) {
        return loadImageFromNetwork(urls[0]);
    }
    /** The system calls this to perform work in the UI thread and delivers the result from doInBackground() */
    protected void onPostExecute(Bitmap result) {
        mImageView.setlmageBitmap(result);
    }
}
```

现在的 UI 是安全的，代码也得到了简化，因为任务分解成了工作线程内完成的部分和 UI 线程内完成的部分。

要全面理解这个类的使用，需阅读 AsyncTask 的参考文档。以下是关于其工作方式的概述：

*   可以用 generics 来指定参数的类型、进度值和任务最终值。
*   工作线程中的 doInBackground() 方法会自动执行。
*   onPreExecute()、onPostExecute() 和 onProgressUpdate() 方法都在 UI 线程中调用。
*   doInBackground() 的返回值会传给 onPostExecute()。
*   在 doInBackground() 内的任何时刻，都可以调用 publishProgress() 来执行 UI 线程中的 onProgressUpdate()。
*   可以在任何时刻、任何线程内取消任务。

#### 注意

在使用工作线程时，可能遇到的另一个问题是，由于运行配置的改变（比如用户改变了屏幕方向）导致 Activity 意外重启，这可能会销毁该工作线程。

要了解如何在这种情况下维持任务执行以及如何在 Activity 被销毁时正确地取消任务，请参见 Shelves 例程的源代码（网址： [`github.com/typeimage/shelves`](https://github.com/typeimage/shelves)）。

## 线程安全方法

在一些情况下，实现的方法可能会被多个线程调用，因此应该设计为线程安全的。

真实存在能被远程调用的方法（比如，绑定服务（bound service）中的方法），当一个方法（在一个 IBinder 中实现）的调用发起于同一个进程（IBinder 正运行的）时，这个方法在调用者线程中执行。但是，如果调用发起于其他进程，那么这个方法将运行于线程池中选出的某个线程中（而不是运行于进程的 UI 线程中），该线程池由系统维护且位于 IBinder 所在的进程中。

例如，即使一个服务的 onBind() 方法是从服务所在进程的 UI 线程中调用的，实现了 onBind() 的方法对象（比如，一个子类实现了 RPC 的方法）仍会从线程池中的线程被调用。因为一个服务可以有多个客户端，所以同时可以有多个线程池与同一个 IBinder 方法相关联。因此，IBinder 方法必须实现为线程安全的。

类似地，尽管 ContentResolver 类、ContentProvider 类隐藏了进程间通信管理的细节，但是 content provider 也能接收来自其他进程的数据请求。

ContentProvider 中响应请求的方法有：query()、insert()、delete()、update() 和 getType() 方法，这些方法都会从它所在进程的线程池中被调用，而不是进程的 UI 线程。由于这些方法可能会从很多线程中同时被调用，因此它们也必须实现为线程安全的。

## 进程间的通信

Android 利用远程过程调用（Remote Procedure Call，RPC）提供了一种进程间通信（IPC）机制，通过这种机制，被 Activity 或其他应用程序组件调用的方法将（在其他进程中）被远程执行，而所有的结果将被返回给调用者。

要求把方法调用及其数据分解到操作系统可以理解的程度，并将其从本地的进程和地址空间传输至远程的进程和地址空间，然后在远程进程中重新组装并执行这个调用。执行后的返回值将被反向传输回来。

Android 提供了执行 IPC 事务所需的全部代码，因此只要关注定义和实现 RPC 编程接口即可。

要执行 IPC，应用程序必须用 bindService() 绑定到服务上。详情可参阅 [`www.cnblogs.com/slgkaifa/p/7169947.html`](https://www.cnblogs.com/slgkaifa/p/7169947.html)。