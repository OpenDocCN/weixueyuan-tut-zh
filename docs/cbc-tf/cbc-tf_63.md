# 深度学习在移动端的应用

本节将介绍一些用于移动端深度学习的实例。它的学习情况与桌面学习或云端深度学习完全不同（在这两种学习中，GPU 和电力通常是可利用的）。实际上，在移动设备上，节省电量和减少 GPU 的使用是非常重要的。

无论如何，深度学习在很多情况下都是非常有用的。现在回顾一下：

*   图像识别：现代手机拥有强大的摄像头，用户渴望尝试对图像和图片进行处理。通常情况下，理解图片中的内容也是很重要的，正如前面章中讨论的那样，有多个预设的训练模型适用于处理此类问题。使用图像识别模型的一个好的示例见链接[`github.com/TensorFlow/models/tree/master/official/resnet`](https://github.com/TensorFlow/models/tree/master/official/resnet)。
*   目标定位：移动目标识别非常重要，是视频和图像处理所必需的。例如，想象一下，如果要在图像中将多个人识别出来，那么照相机将会使用多个焦点。相关的示例见链接[`github.com/TensorFlow/models/tree/master/research/object_detection`](https://github.com/TensorFlow/models/tree/master/research/object_detection)。
*   光学字符识别：手写字符识别是许多工作（如文本分类和文本推荐）的基础，深度学习可以为执行这些工作提供基本的帮助。前面章节中已经看到了几个 MNIST 识别的例子。有关 MNIST 的信息也可参考：[`github.com/TensorFlow/models/tree/master/official/mnist`](https://github.com/TensorFlow/models/tree/master/official/mnist)。
*   语音识别：语音识别是访问现代手机的通用交互接口。在此，深度学习被用于识别声音和口头指令。在最近几年里，关于这方面的进步是令人印象深刻的。
*   翻译：处理多种语言是现代多元文化世界的一部分。手机在跨语言即时翻译方面正在变得越来越准确，这在前些年几乎是不可想象的，深度学习对于打破这些障碍提供了一定的帮助。
*   手势识别：手机开始使用手势作为接收命令的接口，有相应的模型。
*   压缩：压缩是手机的一个关键方面。可以想象，在通过网络发送图像或视频之前，压缩空间是非常有好处的。类似地，在进行本地设备存储前进行压缩也非常方便。在所有这些情况下，深度学习都可以提供帮助。用于压缩的一个 RNN 模型可参考[`github.com/TensorFlow/models/tree/master/research/compression`](https://github.com/TensorFlow/models/tree/master/research/compression)。

#### TensorFlow、移动端和云端

正如所讨论的，手机通常没有 GPU，而且节省电量是很重要的。因此，很多代价高的计算需要借助于云端以减小其代价。当然，它需要根据多种因素折中，如在移动设备上执行深度学习模型的代价、将数据传递到云端的代价、传递的电量代价以及云计算的代价。没有单一的解决方案，最佳的策略取决于你的具体情况。