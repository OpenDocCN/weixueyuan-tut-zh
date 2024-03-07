# 浏览器对 CSS3 的支持（CSS3 浏览器兼容性一览表）

CSS3 带来众多全新的设计体验，但有一个问题值得考虑：浏览器对 CSS3 特性的兼容情况如何？

因为页面最终离不开用浏览器来渲染，并不是所有浏览器都完全支持 CSS3 的特性。有时花时间写的效果只能在特定的浏览器下有效，这意味着只有部分用户才能欣赏到，这样的工作变得没有什么意义。例如，使用 CSS3 制作背景仅在 Webkit 内核的浏览器下有效果。

想知道用户能够体验到哪些 CSS3 的 新特性，必须了解当前浏览器对 CSS3 特性的支持程度如何。

幸运的是，CSS3 特性大部分都已经有了很好的浏览器支持度。各主流浏览器对 CSS3 的支持越来越完善，曾经让多少前端开发人员心碎的 IE 也开始挺进 CSS3 标准行列。

当然，即使 CSS3 标准制定完成，现代浏览器要普及到大部分用户也是一个相当漫长的过程。如果现在就要使用 CSS3 来美化站点，有必要对各大主流浏览器对其新技术的支持情况有一个全面的了解。

本节分别在 Mac 和 Windows 两个平台介绍 Chrome、Firefox、Safari、Opera 和 IE 五大主流浏览器对 CSS3 新特性和 CSS3 选择器的支持情况。

CSS3 属性支持情况如图 1 所示。可以看出，完全支持 CSS3 属性的浏览器有 Chrome 和 Safari，而且不管是 Mac 平台还是 Windows 平台全支持。

![主流浏览器对 CSS3 属性的支持列表](img/a56957941d4b7afb4411bad8b30d327e.jpg)
图 1：CSS3 属性兼容列表
CSS3 选择器支持情况如图 2 所示。除了 IE 家族和 Firefox3，其他几乎全部支持，Chrome、Safari、Firefox 3.6、Opera 10.5 最好。

![主流浏览器对 CSS3 选择器的支持列表](img/9eb1f193229ea584712270feb9840954.jpg)
图 2：CSS3 选择器兼容列表
各主流浏览器都定义了私有属性，以便让用户体验 CSS3 的新特性。例如，

*   Webkit 类型浏览器（如 Safari、Chrome）的私有属性是以`-webkit-`前缀开始，
*   Gecko 类型的浏览器（如 Firefox）的私有属性是以`-moz-`前缀开始，
*   Konqueror 类型的浏览器的私有属性是以`-khtml-`前缀开始，
*   Opera 浏览器的私有属性是以`-o-`前缀开始，
*   而 Internet Explorer 浏览器的私有属性是以`-ms-`前缀开始（目前只有 IE 8+ 支持`-ms-`前缀）。

附录：主流浏览器对 CSS 属性支持的详细列表

| 属性 | IE | Firefox | Chrome | Safari | Opera |
| alignment-adjust |   |   |   |   |   |
| alignment-baseline |   |   |   |   |   |
| @keyframes | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 10 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 16 | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 12.1 |
| animation | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 10 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 16 | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 12.1 |
| animation-name | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 10 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 16 | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 12.1 |
| animation-duration | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 10 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 16 | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 12.1 |
| animation-timing-function | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 10 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 16 | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 12.1 |
| animation-delay | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 10 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 16 | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 12.1 |
| animation-iteration-count | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 10 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 16 | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 12.1 |
| animation-direction | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 10 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 16 | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 12.1 |
| animation-play-state | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 10 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 16 | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 12.1 |
| appearance |   | ![](img/863d2246f070499476b70ea995ea9b01.jpg) 3 | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) |   |
| backface-visibility | ![](img/9869a06f43feeedb2514387e050a1262.jpg) 10 | ![](img/863d2246f070499476b70ea995ea9b01.jpg) | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) |   |
| background-clip | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 9 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 4 | ![](img/725f1439824ef3f034dcffb8187d89fd.jpg) 4 | ![](img/80b1451e63a2baefb628d3117321cb4b.jpg) 5 | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 10.5 |
| background-origin | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 9 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 4 | ![](img/725f1439824ef3f034dcffb8187d89fd.jpg) 4 | ![](img/80b1451e63a2baefb628d3117321cb4b.jpg) 5 | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 10.5 |
| background-size | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 9 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 4 | ![](img/725f1439824ef3f034dcffb8187d89fd.jpg) 4 | ![](img/80b1451e63a2baefb628d3117321cb4b.jpg) 5 | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 10.5 |
| baseline-shift |   |   |   |   |   |
| bookmark-label |   |   |   |   |   |
| bookmark-level |   |   |   |   |   |
| bookmark-target |   |   |   |   |   |
| border-bottom-left-radius | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 9 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 4 | ![](img/725f1439824ef3f034dcffb8187d89fd.jpg) 5 | ![](img/80b1451e63a2baefb628d3117321cb4b.jpg) 5 | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 10.5 |
| border-bottom-right-radius | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 9 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 4 | ![](img/725f1439824ef3f034dcffb8187d89fd.jpg) 5 | ![](img/80b1451e63a2baefb628d3117321cb4b.jpg) 5 | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 10.5 |
| border-image |   | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 15 | ![](img/725f1439824ef3f034dcffb8187d89fd.jpg) 16 | ![](img/80b1451e63a2baefb628d3117321cb4b.jpg) 6 | ![](img/4af0f3471c520a7c3111c7aa09578a30.jpg) 11 |
| border-image-outset |   |   |   |   |   |
| border-image-repeat |   |   |   |   |   |
| border-image-slice |   |   |   |   |   |
| border-image-source |   |   |   |   |   |
| border-image-width |   |   |   |   |   |
| border-radius | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 9 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 4 | ![](img/725f1439824ef3f034dcffb8187d89fd.jpg) 5 | ![](img/80b1451e63a2baefb628d3117321cb4b.jpg) 5 | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 10.5 |
| border-top-left-radius | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 9 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 4 | ![](img/725f1439824ef3f034dcffb8187d89fd.jpg) 5 | ![](img/80b1451e63a2baefb628d3117321cb4b.jpg) 5 | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 10.5 |
| border-top-right-radius | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 9 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 4 | ![](img/725f1439824ef3f034dcffb8187d89fd.jpg) 5 | ![](img/80b1451e63a2baefb628d3117321cb4b.jpg) 5 | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 10.5 |
| box-decoration-break |   |   |   |   |   |
| box-align |   | ![](img/863d2246f070499476b70ea995ea9b01.jpg) | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) |   |
| box-direction |   | ![](img/863d2246f070499476b70ea995ea9b01.jpg) | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) |   |
| box-flex |   | ![](img/863d2246f070499476b70ea995ea9b01.jpg) | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 12.1 |
| box-flex-group |   |   |   |   |   |
| box-lines |   |   |   |   |   |
| box-ordinal-group |   | ![](img/863d2246f070499476b70ea995ea9b01.jpg) | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) |   |
| box-orient |   | ![](img/863d2246f070499476b70ea995ea9b01.jpg) | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) |   |
| box-pack |   | ![](img/863d2246f070499476b70ea995ea9b01.jpg) | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) |   |
| box-shadow | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 9 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 4 | ![](img/725f1439824ef3f034dcffb8187d89fd.jpg) 10 | ![](img/80b1451e63a2baefb628d3117321cb4b.jpg) 5.1 | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 10.5 |
| box-sizing | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 8 | ![](img/863d2246f070499476b70ea995ea9b01.jpg) | ![](img/725f1439824ef3f034dcffb8187d89fd.jpg) 10 | ![](img/80b1451e63a2baefb628d3117321cb4b.jpg) 5.1 | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 9.5 |
| color-profile |   |   |   |   |   |
| column-fill |   |   |   |   |   |
| column-gap | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 10 | ![](img/863d2246f070499476b70ea995ea9b01.jpg) | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 11.1 |
| column-rule | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 10 | ![](img/863d2246f070499476b70ea995ea9b01.jpg) | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 11.1 |
| column-rule-color | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 10 | ![](img/863d2246f070499476b70ea995ea9b01.jpg) | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 11.1 |
| column-rule-style | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 10 | ![](img/863d2246f070499476b70ea995ea9b01.jpg) | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 11.1 |
| column-rule-width | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 10 | ![](img/863d2246f070499476b70ea995ea9b01.jpg) | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 11.1 |
| column-span | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 10 |   | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 11.1 |
| column-width | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 10 | ![](img/863d2246f070499476b70ea995ea9b01.jpg) | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 11.1 |
| columns | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 10 | ![](img/863d2246f070499476b70ea995ea9b01.jpg) | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 11.1 |
| column-count | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 10 | ![](img/863d2246f070499476b70ea995ea9b01.jpg) | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 11.1 |
| crop |   |   |   |   |   |
| dominant-baseline |   |   |   |   |   |
| drop-initial-after-adjust |   |   |   |   |   |
| drop-initial-after-align |   |   |   |   |   |
| drop-initial-before-adjust |   |   |   |   |   |
| drop-initial-before-align |   |   |   |   |   |
| drop-initial-size |   |   |   |   |   |
| drop-initial-value |   |   |   |   |   |
| fit |   |   |   |   |   |
| fit-position |   |   |   |   |   |
| float-offset |   |   |   |   |   |
| @font-face | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 9 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 3.6 | ![](img/725f1439824ef3f034dcffb8187d89fd.jpg) 4 | ![](img/80b1451e63a2baefb628d3117321cb4b.jpg) 3 | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 10 |
| font-size-adjust |   | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 2 |   |   |   |
| font-stretch |   |   |   |   |   |
| grid-columns |   |   |   |   |   |
| grid-rows |   |   |   |   |   |
| hanging-punctuation |   |   |   |   |   |
| hyphenate-after |   |   |   |   |   |
| hyphenate-before |   |   |   |   |   |
| hyphenate-characters |   |   |   |   |   |
| hyphenate-lines |   |   |   |   |   |
| hyphenate-resource |   |   |   |   |   |
| hyphens |   |   |   |   |   |
| icon |   |   |   |   |   |
| image-orientation |   |   |   |   |   |
| image-resolution |   |   |   |   |   |
| inline-box-align |   |   |   |   |   |
| line-stacking |   |   |   |   |   |
| line-stacking-ruby |   |   |   |   |   |
| line-stacking-shift |   |   |   |   |   |
| line-stacking-strategy |   |   |   |   |   |
| mark |   |   |   |   |   |
| mark-after |   |   |   |   |   |
| mark-before |   |   |   |   |   |
| marks |   |   |   |   |   |
| marquee-direction |   |   | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) |   |
| marquee-play-count |   |   | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) |   |
| marquee-speed |   |   | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) |   |
| marquee-style |   |   | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) |   |
| move-to |   |   |   |   |   |
| nav-down |   |   |   |   | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 11.5 |
| nav-index |   |   |   |   | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 11.5 |
| nav-left |   |   |   |   | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 11.5 |
| nav-right |   |   |   |   | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 11.5 |
| nav-up |   |   |   |   | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 11.5 |
| opacity | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 9 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 2 | ![](img/725f1439824ef3f034dcffb8187d89fd.jpg) 4 | ![](img/80b1451e63a2baefb628d3117321cb4b.jpg) 3.1 | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 9 |
| outline-offset |   | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) | ![](img/725f1439824ef3f034dcffb8187d89fd.jpg) | ![](img/80b1451e63a2baefb628d3117321cb4b.jpg) | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 11 |
| overflow-style |   |   |   |   |   |
| overflow-x | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 9 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 2 | ![](img/725f1439824ef3f034dcffb8187d89fd.jpg) | ![](img/80b1451e63a2baefb628d3117321cb4b.jpg) | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) |
| overflow-y | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 9 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 2 | ![](img/725f1439824ef3f034dcffb8187d89fd.jpg) | ![](img/80b1451e63a2baefb628d3117321cb4b.jpg) | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) |
| page |   |   |   |   |   |
| page-policy |   |   |   |   |   |
| perspective |   |   | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) 5 |   |
| perspective-origin |   |   | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) |   |
| punctuation-trim |   |   |   |   |   |
| rendering-intent |   |   |   |   |   |
| resize |   | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 5 | ![](img/725f1439824ef3f034dcffb8187d89fd.jpg) 4 | ![](img/80b1451e63a2baefb628d3117321cb4b.jpg) 4 |   |
| rest |   |   |   |   |   |
| rest-after |   |   |   |   |   |
| rest-before |   |   |   |   |   |
| rotation |   |   |   |   |   |
| rotation-point |   |   |   |   |   |
| ruby-align | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) |   |   |   |   |
| ruby-overhang | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) |   |   |   |   |
| ruby-position | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) |   |   |   |   |
| ruby-span |   |   |   |   |   |
| size |   |   |   |   |   |
| string-set |   |   |   |   |   |
| target |   |   |   |   |   |
| target-name |   |   |   |   |   |
| target-new |   |   |   |   |   |
| target-position |   |   |   |   |   |
| text-align-last |   |   |   |   |   |
| text-emphasis |   |   |   |   |   |
| text-height |   |   |   |   |   |
| text-justify | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 5 |   |   |   |   |
| text-outline |   |   |   |   |   |
| text-overflow | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 6 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 7 | ![](img/725f1439824ef3f034dcffb8187d89fd.jpg) 4 | ![](img/80b1451e63a2baefb628d3117321cb4b.jpg) 3.1 | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 11 |
| text-shadow | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 10 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 3.5 | ![](img/725f1439824ef3f034dcffb8187d89fd.jpg) 4 | ![](img/80b1451e63a2baefb628d3117321cb4b.jpg) 4 | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 9.5 |
| text-wrap |   |   |   |   |   |
| transform | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 10 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 16 | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 12.1 |
| transform-origin | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 10 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 16 | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 12.1 |
| transform-style |   |   | ![](img/a37b6d985ee69eb638b0f57614f8ce37.jpg) | ![](img/245d497c109431dec529611d55b87914.jpg) 5 |   |
| transition | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 10 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 16 | ![](img/725f1439824ef3f034dcffb8187d89fd.jpg) 26 | ![](img/245d497c109431dec529611d55b87914.jpg) | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 12.1 |
| transition-property | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 10 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 16 | ![](img/725f1439824ef3f034dcffb8187d89fd.jpg) 26 | ![](img/245d497c109431dec529611d55b87914.jpg) | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 12.1 |
| transition-duration | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 10 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 16 | ![](img/725f1439824ef3f034dcffb8187d89fd.jpg) 26 | ![](img/245d497c109431dec529611d55b87914.jpg) | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 12.1 |
| transition-timing-function | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 10 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 16 | ![](img/725f1439824ef3f034dcffb8187d89fd.jpg) 26 | ![](img/245d497c109431dec529611d55b87914.jpg) | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 12.1 |
| transition-delay | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 10 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 16 | ![](img/725f1439824ef3f034dcffb8187d89fd.jpg) 26 | ![](img/245d497c109431dec529611d55b87914.jpg) | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 12.1 |
| word-break | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 5.5 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 15 | ![](img/725f1439824ef3f034dcffb8187d89fd.jpg) 4 | ![](img/80b1451e63a2baefb628d3117321cb4b.jpg) 3 |   |
| word-wrap | ![](img/31fd11b1f16cca3416f8ab0dcddb971f.jpg) 5 | ![](img/1df69d099b4a92cb3be30cae6f3d86ec.jpg) 3.5 | ![](img/725f1439824ef3f034dcffb8187d89fd.jpg) 4 | ![](img/80b1451e63a2baefb628d3117321cb4b.jpg) 3.1 | ![](img/d7600f3f0720a9c5190806a3fb592be2.jpg) 10.5 |

对于某些属性，你会看到浏览器的图标和一个数字，数字表示是第一次支持该属性的浏览器版本。