# MATLAB R2016b 的新增功能

> 原文：[`c.biancheng.net/view/6605.html`](http://c.biancheng.net/view/6605.html)

MathWorks 公司于 2016 年 9 月 19 日推出 Release 2016b（R2016b），其中增加了新的功能以简化 MATLAB 中的大数据处理过程。工程师和科学家可更轻松的在 MATLAB 中进行大数据处理而不必考虑内存的限制。

R2016b 中还包括了 Simulink 的其他新功能、一个新的工具箱 Risk Management Toolbox 以及其他 83 款产品的更新和问题修复。

工程师和科学家可利用 MATLAB 的常用功能和语法，通过 tall 数组轻松处理超出内存限制的数据，而无须学习大数据编程，并且有上百个数学、统计和机器学习算法，实现了对 tall 数组的支持。

采用 tall 数组实现的大数据处理代码可以在 Hadoop 集群上运行或者被直接集成到其他 Spack 应用程序当中。

R2016b 还增加了一个时间表数据容器，用于索引和同步带时间戳的表格数据；增加了字符串数组，用于有效的进行文本数据的操作、比较和存储；还增加了其他用于数据处理的新功能。

MathWorks 公司的 MATLAB 市场营销总监 David Rich 表示：“很多公司拥有海量数据，却很难对其加以利用以创建有效的预测模型及获得更深入的数据洞察。我们通过推出 R2016b，降低了领域专家操作数据的门槛，使其能够更轻松的处理更多的数据，同时使得系统的设计、性能和可靠性得以提升”。

## MATLAB 产品系列更新包括以下方面：

#### 1) MATLAB 

*   引入 tall 数组用于操作超过内存限制的过大数据。
*   引入时间表数据容器用于索引和同步带时间戳的表格数据。
*   增加在脚本中定义本地函数的功能以提高代码的重用性和可读性。
*   通过使用 MATLAB 的 Java API 可以在 Java 程序中调用 MATLAB 代码。

#### 2) MATLAB Mobile

通过在 MathWorks 云端的 iPhone 和 Android 传感器记录数据。

#### 3) Database Toolbox

提供用于检索 Neo4j 数据的图形化数据库界面。

#### 4) MATLAB Compiler

支持将 MATLAB 应用程序（包括 tall 数组）部署到 Spark 集群上。

#### 5) Parallel Computing Toolbox

能够在台式机、装有 MATLAB Distributed Computing Server 的服务器以及 Spark 集群上利用 tall 数组进行大数据并行处理。

#### 6) Statistics and Machine Learning Toolbox

提供不受内存限制的大数据分析算法，包括降维、描述性统计、k- 均值聚类、线性递归、逻辑递归和判别分析。

提供可以自动调整机器学习算法参数的 Bayesian 优化算法以及可以选择机器学习模型特征的近邻成分分析（NCA）。

支持使用 MATLAB Coder 自动生成实现 SVM 和逻辑回归模型的 C 语言/C++ 代码。

#### 7) Image Processing Toolbox

支持使用三维超像素的立体图像数据进行简单线性迭代聚类（SLIC）和三维中值滤波。

#### 8) Computer Vision System Toolbox

使用基于区域的卷积神经网络深度学习算法（R-CNN）进行对象检测。

#### 9) Risk Management Toolbox

一个新的工具箱用于开发风险模型和执行风险模拟。

#### 10) ThingSpeak

能够从联网的传感器采集数据，并使用由 Statistics and Machine LearningToolbox、Signal Processing Toolbox、Curve Fitting Toolbox 和 Mapping Toolbox 提供的函数在云端进行 MATLAB 分析。

## Simulink 产品系列更新包括以下方面：

#### 1) Simulink

*   使用 JIT 编译器提升在加速器模式下运行的仿真的性能。
*   能够初始化、重置并终止子系统，进行动态启动和关闭行为建模。
*   状态读取器和写入器模块可以从模型中的任何位置完全控制重置状态行为。
*   对 Raspberry Pi 3 和 Google Nexus 的硬件支持。

#### 2) Simulink 和 Stateflow

简化参数和数据编辑的属性检查器、模型数据编辑器和符号管理器。

#### 3) Simscape

新增了一个模块库，用于模拟理想气体、半理想气体以及实际气体系统。

## 信号处理和通信更新包括以下方面：

#### 1) Signal Processing Toolbox

可用于执行多时序的时域和频域分析的信号分析仪应用程序。

#### 2) Phased Array System Toolbox

针对空气传播和多路径传播对窄频和宽频信号的影响提供建模支持。

#### 3) WLAN System Toolbox

IEEE 802.11 ah 支持和多用户 MIMO 接收机功能。

#### 4) Audio System Toolbox

音频插件托管功能，可在 MATLAB 中直接运行和测试 VST 插件。

## 代码生成更新包括以下方面：

#### 1) Embedded Coder

*   交叉发布代码集成功能使得可以重用由较早版本生成的代码。
*   能够生成可用于任何软件环境的可插入式代码，包括动态启动和关闭行为。
*   支持仿真 AUTOSAR 基础软件，包括 Diagnostic Event Manager（DEM）和 NVRAM Manager（NvM）。

#### 2) HDL Coder

根据设定的目标时钟频率，以寄存器插入方式自适应流水化，以及可用于显示、分析转换和状态的逻辑分析仪（搭配使用 DSP System Toolbox）。

## 验证和确认更新包括以下方面：

#### 1) Simulink Verification and Validation

Edit-time checking 功能，可帮助在设计时发现并修复标准合规性问题。

#### 2) Simulink Test

用于进行测试评估的自定义标准的定义功能。

#### 3) HDL Verifier

FPGA 数据采功能，用于探测要在 MATLAB 或 Simulink 中进行分析的内部 FPGA 信号。

#### 4) Polyspace Bug Finder

支持 CERT C 编码规范，用于网络安全漏洞检测。