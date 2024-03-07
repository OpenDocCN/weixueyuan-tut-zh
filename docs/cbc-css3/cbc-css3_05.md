# CSS3 新特性

CSS3 是 CSS 规范的最新版本，在 CSS2.1 的基础上增加了很多强大的新功能，以帮助开发人员解决一些实际面临的问题，并且不再需要非语义标签、复杂的 JavaScript 脚本以及图片。例如，CSS3 支持圆角、多背景、透明度、阴影、动画、图表等功能。

## CSS3 规范是分模块的

CSS1 和 CSS2.1 都是单一的规范，其中 CSS1 主要定义了网页对象的基本样式，如字体、颜色、背景、边框等，CSS2 添加了高级概念，如浮动、定位、高级选择器（如子选择器、相邻选择器和通用选择器等）。

CSS3 被划分成了多个模块，每个模块都有自己的规范，这样做的好处是：

*   整个 CSS3 的规范发布不会因为部分存在争论而影响其他模块的推进。
*   对于浏览器来说，可以根据需要，决定哪些 CSS 功能被支持。
*   对于 W3C 制定者而言，可以根据需要进行针对性的更新，从而使一个整体的规范更加灵活，并能够及时修订，这样更容易扩展新的技术特定。

2001 年 5 月 23 日，W3C 完成了 CSS3 的工作草案，在该草案中制定了 CSS3 发展路线图，路线图详细列出了所有模块，并计划在未来将逐步进行规范。

下面简单地说明一下各个主要模块的发布时间和内容：

*   2002 年 5 月 15 日发布了 CSS3 Line 模块，该模块规范了文本行模型。
*   2002 年 11 月 7 日发布了 CSS3 Lists 模块，该模块规范了列表样式。
*   2002 年 11 月 7 日还发布了 CSS3 Border 模块，新添加了背景边框功能。该模块后来被合并到了背景模块中。
*   2003 年 5 月 14 日发布了 CSS3 Generated and Replaced Content 模块，定义了 CSS3 的生成及更换内容功能。
*   2003 年 8 月 13 日发布了 CSS3 Presentation Levels 模块，定义了演示效果功能。
*   2003 年 8 月 13 日还发布了 CSS3 Syntax 模块，该模块重新的定义了 CSS 语法基础。
*   2004 年 2 月 24 日发布了 CSS3 Hyperlink Presentation 模块，重新定义了超链接表示规则。
*   2004 年 12 月 16 日发布了 CSS3 Speech 模块，该模块重新定义了语音“样式”规则。
*   2005 年 12 月 15 日发布了 CSS3 Cascading and Inheritance 模块，定义了 CSS 层叠和继承规则。
*   2007 年 8 月 9 日发布了 CSS3 Basic Box 模块，该模块重新定义了 CSS 基本盒模型规则。
*   2007 年 9 月 5 日发布了 CSS3 Grid Positioning 模块，定义了 CSS 网格定位规则。
*   2009 年 3 月 20 日发布了 CSS3 Animations 模块，定义了 CSS 动画模型。
*   2009 年 3 月 20 日还发布了 CSS3 3D Transforms 模块，定义了 CSS 3D 转换模型。
*   2009 年 6 月 18 日发布了 CSS3 Fonts 模块，定义了 CSS 字体模式。
*   2009 年 7 月 23 日发布了 CSS3 Image Values 模块，定义了图像内容显示模型。
*   2009 年 7 月 23 日还发布了 CSS3 Flexible Box Layout 模块，定义了灵活的框布局模型。
*   2009 年 12 月 1 日发布了 CSS3 2D Transforms 模块，定义了 2D 转换模型。
*   2010 年 4 月 29 日发布了 CSS3 Generated Conten for Paged Media 模块，定义了分页媒体内容模型。
*   2010 年 10 月 5 日发布了 CSS3 Text 模块，定义了文本模型。
*   2010 年 10 月 5 日还发布了 CSS3 Background and Borders 模块，该模块重新补丁了边框和背景模型。

更详细的信息可以参见 [`www.w3c.org/Style/CSS/current-work.html`](https://www.w3c.org/Style/CSS/current-work.html)，其中介绍了 CSS3 具体划分了多少个模块、CSS3 所有模块目前所处的状态，以及将在什么时候发布等。

## CSS3 新特性

CSS3 规范并不是完全另起炉灶，它集成了 CSS2.1 的部分内容，但在其基础上进行了很多的增补与修订。

与 CSS1、CSS2 相比，CSS3 进行了革命性的升级，而不仅限于局部功能的修订和完善，尽管浏览器对 CSS3 诸多新特性的支持还不是很完善，但是它依然让用户看到了未来网页样式的发展方向和使命。

CSS3 的新特性非常多，这里简单列举被浏览器广泛支持的实用特性。

#### 1\. 完善选择器

CSS3 选择器在 CSS2.1 的基础上进行了增强，它允许设计师在标签中指定特定的 HTML 元素，而不必使用多余的类、ID 或者 JavaScript 脚本。

如果希望设计干净、轻量级的网页标签，希望结构与表现更好地分离，高级选择器是非常有用的。他可以减少在标签中增加大量 class 和 id 属性的数量，并让设计师更方便地维护样式表。

#### 2\. 完善视觉效果

网页中最常见的效果包括圆角、阴影、渐变背景、半透明、图片边框等。而这样的视觉效果在 CSS 中都是依赖于设计师制作图片或者 JavaScript 脚本来实现的。

CSS3 的一些新特性可以用来创建一些特殊的视觉效果，后面的章节将为大家展现这些新特性是如何实现这些视觉效果的。

#### 3\. 完善背景效果

如果说 CSS 中的背景给你带来太多的限制，那么 CSS3 将带来革命性的变化。

CSS3 不再局限于背景色、背景图像的运用，新特性中添加了多个新的属性值，如 background-origin、background-clip、background-size；此外，还可以在一个元素上设置多个背景图片。

这样，如果要设计比较复杂的页面效果，就不再需要使用一些多余的标签来辅助实现了。例如，要实现 CSS 中的滑动门效果，在 CSS 中基本上要添加 2、3 个额外的标签来辅助实现，而 CSS3 中的这些新特性能够在一个标签中完成同样的效果。

#### 4\. 完善盒模型

盒模型在 CSS 中是重中之重，CSS2 中的盒模型只能实现一些基本的功能，对于一些特殊的功能需要基于 JavaScript 来实现。而在 CSS3 中，这一点得到了很大的改善，设计师可以直接通过 CSS3 来实现。

例如，CSS3 中的弹性盒子，这个属性将给大家引入一种全新的布局概念，能轻而易举地实现各种布局，特别是在移动端的布局，它的功能更是强大。

#### 5\. 增强背景功能

CSS3 允许背景属性设置多个属性值，如 background-image、background-repeat、background-size、 background-position、background-origin、background-clip 等，这样就可以在一个元素上添加多层背景图片。如果要设计复杂的网页效果（如圆角、背景重叠等），就不用为 HTML 文档添加多个无用的标签，以优化网页文档结构。

#### 6\. 增加阴影效果

阴影主要分为两种：文本阴影（text-shadow）和盒子阴影（box-shadow）。

文本阴影在 CSS 中己经存在，但没有得到广泛运用。CSS3 延续了这个特性，并进行了新的定义，该属性提供了一种新的跨浏览器方案，使文本看起来更醒目。

盒子阴影的实现在 CSS2 中就有点苦不堪言，为了实现这样的效果，需要新增标签、图片，而且效果还不一定完美。CSS3 的 box-shadow 将打破这种局面，可以轻易地为任何元素添加盒子阴影。

#### 7\. 增加多列布局与弹性盒模型布局

CSS3 引入了几个新的模块，用于更方便地创建多列布局。

多列布局（Multi-column Layout）模块描述如何像报纸、杂志那样，把一个简单的区块拆分成多列。

弹性盒模型布局（Flexible Box Layout）模块能让区块在水平、垂直方向对齐，能让区块自适应屏幕大小，相对于 CSS 的浮动布局、inline-block 布局、绝对定位布局来说，它显得更加方便与灵活。

缺点是：这两个模块在一些浏览器中还不被支持，但随着技术的发展，各主流浏览器会主动支持的。

#### 8\. 完善 Web 字体和 Web Font 图标

浏览器对 Web 字体有诸多限制，Web Font 图标对于设计师来说更奢侈。CSS3 重新引入 @font-face，对于设计师来说无疑是件好事。

@font-face 是链接服务器上的字体的一种方式，这些嵌入的字体能变成浏览器的安全字体，不再担心用户没有这些字体而无法正常显示的问题，从此告别用图片代替特殊字体的设计时代。

#### 9\. 增强颜色和透明度功能

CSS3 颜色模块的引入，实现了制作页面效果时不再局限于 RGB 和十六进制两种模式。CSS3 增加了 HSL、HSLA、RGBA 几种新的颜色模式。在网页设计中，能轻松实现使某个颜色变得再亮一点或者再暗一点。其中 HSLA 和 RGBA 还增加了透明通道，能轻松地改变任何一个元素的透明度。

另外，还可以使用 opacity 属性来制作元素的透明度。从此制作透明度不再依赖图片或者 JavaScript 脚本了。

#### 10\. 新增圆角与边框功能

圆角是 CSS3 中使用最多的一个属性，原因很简单：圆角比直线更美观，而且不会与设计产生任何冲突。与 CSS 制作圆角不同之处是，CSS3 无须添加任何标签元素与图片，也不需借用任何 JavaScript 脚本，一个属性就能搞定。

对于边框，在 CSS 中仅局限于对边框的线型、粗细、颜色的设置，如果需要特殊的边框效果，只能使用背景图片来模仿。CSS3 的 border-image 属性使元素边框的样式变得丰富起来，还可以使用该属性实现类似 background 的效果，对边框进行扭曲、拉伸和平铺等。

#### 11\. 增加变形操作

在 CSS2 时代，让某个元素变形是一个可望而不可即的想法，为了实现这样的效果，需要写大量的 JavaScript 代码。CSS3 引进了一个变形属性，可以在 2D 或者 3D 空间里操作网页对象的位置和形状，例如旋转、扭曲、缩放或者移位。

#### 12\. 增加动画和交互效果

CSS3 过渡（transition）特性能在网页制作中实现一些简单的动画效果，让某些效果变得更具流线性、平滑性。

而 CSS3 动画（animation）特性能够实现更复杂的样式变化，以及一些交互效果，而不需要使用任何 Flash 或 JavaScript 脚本代码。

#### 13\. 完善媒体特性与 Responsive 布局

CSS3 媒体特性可以实现一种响应式（Responsive）布局，使布局可以根据用户的显示终端或设备特征选择对应的样式文件，从而在不同的显示分辨率或设备下具有不同的布局效果，特别是在移动端上的实现更是一种理想的做法。

## CSS3 现状

通过对 CSS3 新特性的简单介绍，大家可能会问：这些超炫的特性什么时候才能成为标准并最终发布呢？其实 CSS3 的每一个模块都有它自己的更新时间，如图表 1 所示，从该表可以看到 CSS3 当前发展的详细进度。

注意，该信息是动态更新的，请参考页面：[`www.w3.org/Style/CSS/current-work.html`](http://www.w3.org/Style/CSS/current-work.html)

其中 Current 列表示模块当前的状态，Upcoming 列表示即将进行的状态。各种状态缩写词说明如下：

*   WD：Working Draft，表示工作草案。
*   LC：Last Call，表示最终工作草案。
*   CR：Candidate Recommendation，表示候选推荐标准。
*   PR：Proposed Recommendation，表示建议推荐标准。
*   REC：Recommendation，表示推荐标准。

表 1：CSS3 所有模块进度表

| Com­pleted | Cur­rent | Up­com­ing |
| CSS Snapshot 2017 | NOTE |   |
| CSS Snapshot 2015 | NOTE |   |
| CSS Snapshot 2010 | NOTE |   |
| CSS Snapshot 2007 | NOTE |   |
| CSS Color Level 3 | REC | REC |
| CSS Namespaces | REC | REC |
| CSS Level 2 Revision 1 | REC | REC |
| Media Queries | REC | REC |
| CSS Style Attributes | REC | REC |
| CSS Fonts Level 3 | REC | REC |
| CSS Basic User Interface Level 3 | REC | REC |
| Stable | Cur­rent | Up­com­ing |
| Selectors Level 3 | REC | REC |
| CSS Backgrounds and Borders Level 3 | CR | PR |
| CSS Conditional Rules Level 3 | CR | CR |
| CSS Multi-column Layout Level 1 | WD | CR |
| CSS Values and Units Level 3 | CR | PR |
| CSS Cascading and Inheritance Level 3 | CR | PR |
| CSS Writing Modes Level 3 | CR | CR |
| CSS Counter Styles Level 3 | CR | PR |
| Testing | Cur­rent | Up­com­ing |
| CSS Image Values and Replaced Content Level 3 | CR | CR |
| CSS Speech | CR | CR |
| CSS Flexible Box Layout Level 1 | CR | PR |
| CSS Text Decoration Level 3 | CR | CR |
| CSS Shapes Level 1 | CR | CR |
| CSS Masking Level 1 | CR | CR |
| CSS Fragmentation Level 3 | CR | PR |
| CSS Cascading Variables | CR | PR |
| Compositing and Blending Level 1 | CR | CR |
| CSS Syntax Level 3 | CR | CR |
| CSS Grid Layout Level 1 | CR | PR |
| CSS Display Level 3 | CR | CR |
| CSS Will Change Level 1 | CR | PR |
| Media Queries Level 4 | CR | PR |
| Geometry Interfaces Level 1 | CR | CR |
| CSS Cascading and Inheritance Level 4 | CR | PR |
| CSS Scroll Snap Level 1 | CR | PR |
| CSS Painting API Level 1 | CR | PR |
| CSS Containment Level 1 | CR | PR |
| CSS Writing Modes Level 4 | CR | PR |
| Refining | Cur­rent | Up­com­ing |
| CSS Animations Level 1 | WD | WD |
| Web Animations | WD | WD |
| CSS Text Level 3 | WD | CR |
| CSS Transforms | WD | WD |
| CSS Transitions | WD | CR |
| CSS Box Alignment Level 3 | WD | WD |
| Selectors Level 4 | WD | WD |
| Preview of CSS Level 2 | FPWD | NOTE |
| CSS Easing Functions Level 1 | WD | WD |
| Revising | Cur­rent | Up­com­ing |
| CSS Paged Media Level 3 | WD | WD |
| CSSOM View | WD | WD |
| CSS Intrinsic & Extrinsic Sizing Level 3 | WD | CR |
| CSS Ruby Level 1 | WD | WD |
| CSS Overflow Level 3 | WD | WD |
| CSS Pseudo-Elements Level 4 | WD | WD |
| Exploring | Cur­rent | Up­com­ing |
| CSS Backgrounds and Borders Level 4 |   | FPWD |
| CSS Device Adaptation | WD | WD |
| CSS Exclusions | WD | WD |
| Filter Effects | WD | WD |
| CSS Generated Content for Paged Media | WD | WD |
| CSS Page Floats | FPWD | WD |
| CSS Template Layout | NOTE | NOTE |
| CSS Line Grid | WD | WD |
| CSS Lists Level 3 | WD | WD |
| CSS Positioned Layout Level 3 | WD | WD |
| CSS Regions | WD | WD |
| CSS Table Level 3 | WD | WD |
| CSS Object Model | WD | WD |
| CSS Font Loading | WD | WD |
| CSS Scoping Level 1 | FPWD | WD |
| Non-element Selectors | FPWD | WD |
| CSS Inline Layout Level 3 | WD | WD |
| Motion Path Level 1 | WD | WD |
| CSS Round Display Level 1 | WD | WD |
| CSS Basic User Interface Level 4 | FPWD | WD |
| CSS Text Level 4 | FPWD | WD |
| CSS Properties and Values API Level 1 | FPWD | WD |
| CSS Typed OM Level 1 | WD | WD |
| Worklets Level 1 | FPWD | WD |
| CSS Color Level 4 | FPWD | WD |
| CSS Fonts Level 4 | WD | WD |
| CSS Rhythmic Sizing Level 1 | FPWD | WD |
| CSS Image Values and Replaced Content Level 4 | WD | WD |
| CSS Fill and Stroke Level 3 | FPWD | WD |
| CSS Logical Properties and Values Level 1 | WD | WD |
| CSS Overflow Level 4 | FPWD | WD |
| CSS Grid Layout Level 2 | FPWD | WD |
| CSS Text Decoration Level 4 | FPWD | WD |
| CSS Layout API Level 1 | FPWD | WD |
| CSS Values and Units Level 4 | FPWD | WD |
| CSS Scrollbars Level 1 | FPWD | WD |
| Rewriting | Cur­rent | Up­com­ing |
| CSS Box Model Level 3 | WD | WD |
| CSS Generated Content Level 3 | WD | WD |
| Abandoned | Cur­rent | Up­com­ing |
| CSS Level 1 | SPSD |   |
| CSS Print Profile | NOTE |   |
| CSS Mobile Profile 2.0 | NOTE |   |
| The CSS 'Reader' Media Type | NOTE |   |
| CSS Presentation Levels | NOTE |   |
| CSS TV Profile 1.0 | NOTE |   |
| CSS Marquee | NOTE |   |
| Behavioral Extensions to CSS | NOTE |   |
| CSS Hyperlink Presentation | NOTE |   |
| Fullscreen | NOTE |   |

## 给初学者的建议

很多用户希望在 CSS3 标准规范发布之前就能使用这些新特性，而对它们的使用还受限于不同的浏览器，只有浏览器完全支持了，才能完全使用这些新特性。

目前，CSS3 还不是最终的标准，有很多浏览器支持不够完美，那么现在可以使用 CSS3 吗？

从上面的介绍可以看出，CSS3 还在不断完善中，很多功能还处于草稿阶段，但部分模块进入了候选推荐状态，这说明在 Web 设计中完全可以使用这些模块。即使有一些模块还处于工作草案状态，也可以尝试着使用，只有不断将新的 CSS 技术运用到实际工作中，才能发现应用这些新技术所面临的真正挑战，以便 W3C 更好地完善它们，从而更好地、有效地促使它们成为真正的标准。

初学者应该了解哪些可用，哪些还不能使用。换句话说，在实际工作开发中，可以先运用相对稳定的 CSS3 特性，并确保不会对尚不支持这些特性的浏览器造成影响。做到明智的使用，而不是盲目地滥用 CSS3 新特性。