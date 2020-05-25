---
title: Flutter与React Native的区别有哪些
description: 对于APP混合开发来说，我们改怎样选择适合自己适合项目的框架，这很重要，这就意味着我们要对当前潮流框架比较熟悉了。下面我们就谈谈当前潮流框架ReactNative与Flutter的区别吧。
categories:
 - ReactNative
tags:
---

> 跨平台一直是老生常谈的话题，cordova、ionic、react-native、weex、kotlin-native、flutter等跨平台框架的百花齐放，颇有一股推倒原生开发者的势头。（事实上更多是共存发展）在这里我下面主要分析下当前最主要的ReactNative与Flutter这个两个框架的区别。



<!-- more -->

## 前言

移动端跨平台在经历数年沉浮之后，如今还能在舞台聚光灯下雀跃的， 也只剩下 React Native 和 Flutter 了，作为沉淀了数年的 “豪门” 与 19 年当红的 “新贵” ，它们之间的 “针锋相对” 也成了开发者们关心的事情。

>过去曾有人问我：“他即写 Java 又会 Object-C ，在 Android 和 IOS 平台上可以同时开发，为什么还要学跨平台呢？”

>而我的回答是：跨平台的市场优势不在于性能或学习成本，甚至平台适配会更耗费时间，但是它最终能让代码逻辑（特别是业务逻辑），无缝的复用在各个平台上，降低了重复代码的维护成本，保证了各平台间的统一性， 如果这时候还能保证一定的性能，那就更完美了。

|类型|ReactNative|Flutter|
|---|---|---|
|语音|JavaScript|dart|
|环境|JSCore|Flutter Engine|
|发布时间|2105|2017|
|star|78k+|67k+|
|对比版本|0.59.9|1.6.3|
|空项目打包大小|Android 20M(可调整至 7.3M) / IOS 1.6M|Android 5.2M / IOS 10.1M|
|GSY项目大小|	Android 28.6M / IOS 9.1M|	Android 11.6M / IOS 21.5M|
|代码产物|	JS Bundle 文件|	二进制文件|
|维护者|	Facebook|	Google|
|风格|	响应式，Learn once, write anywhere|	响应式，一次编写多平台运行|
|支持|	Android、IOS、(PC)|	Android、IOS、(Web/PC)|
|使用代表|	京东、携程、腾讯课堂|	闲鱼、美团B端|


## 环境搭建

无论是 React Native 还是 Flutter ，都需要 Android 和 IOS 的开发环境，也就是 JDK 、Android SDK、Xcode 等环境配置，而不同点在于 ：

1.React Native 需要 npm 、node 、react-native-cli 等配置 。
2.Flutter 需要 flutter sdk 和 Android Studio / VSCode 上的 Dart 与 Flutter 插件。

从配置环境上看， Flutter 的环境搭配相对简单，而 React Native 的环境配置相对复杂，而且由于 node_module 的“黑洞”属性和依赖复杂度等原因，目前在个人接触的例子中，首次配置运行成功率 Flutter 是高于 React Native 的，且 Flutter 失败的原因则大多归咎于网络。

>同时跨平台开发首选 Mac ，没有为什么。




## 实现原理

在 Android 和 IOS 上，默认情况下 Flutter 和 React Native 都需要一个原生平台的
Activity / ViewController 支持，且在原生层面属于一个“单页面应用”， 而它们之间最大的不同点其实在于 UI 构建 ：

* React Native ：
React Native 是一套 UI 框架，默认情况下 React Native 会在 Activity 下加载 JS 文件，然后运行在 JavaScriptCore 中解析 Bundle 文件布局，最终堆叠出一系列的原生控件进行渲染。

简单来说就是 通过写 JS 代码配置页面布局，然后 React Native 最终会解析渲染成原生控件，如 <View> 标签对应 ViewGroup/UIView ，<ScrollView> 标签对应 ScrollView/UIScrollView ，<Image> 标签对应 ImageView/UIImageView 等。

![image](https://upload-images.jianshu.io/upload_images/3673902-389ce07b6f871017.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

所以相较于如 Ionic 等框架而言， React Native 让页面的性能能得到进一步的提升。

* Flutter ：
如果说 React Native 是为开发者做了平台兼容，那 Flutter 则更像是为开发者屏蔽平台的概念。

>Flutter 中只需平台提供一个 Surface 和一个 Canvas ，剩下的 Flutter 说：“你可以躺下了，我们来自己动”。

Flutter 中绝大部分的 Widget 都与平台无关， 开发者基于 Framework 开发 App ，而 Framework 运行在 Engine 之上，由 Engine 进行适配和跨平台支持。这个跨平台的支持过程，其实就是将 Flutter UI 中的 Widget “数据化” ，然后通过 Engine 上的 Skia 直接绘制到屏幕上 。

![image-1](https://upload-images.jianshu.io/upload_images/3673902-fbfa6ab912b44913.png?imageMogr2/auto-orient/strip|imageView2/2/w/809/format/webp)

所以从以上可以看出：React Native 的 Learn once, write anywhere 的思路，就是只要你会 React ，那么你可以用写 React 的方式，再去开发一个性能不错的App；而 Flutter 则是让你忘掉平台，专注于 Flutter UI 就好了。

* DOM：

额外补充一点，React 的虚拟 DOM 的概念相信大家都知道，这是 React 的性能保证之一，而 Flutter 其实也存在类似的虚拟 DOM 概念。

>看过我 Flutter 系列文章可能知道，Flutter 中我们写的 Widget ， 其实并非真正的渲染控件，这一点和 React Native 中的标签类似，Widget 更像配置文件， 由它组成的 Widget 树并非真正的渲染树。

Widget 在渲染时会经过 Element 变化， 最后转化为 RenderObject 再进行绘制， 而最终组成的 RenderObject 树才是 “真正的渲染 Dom” ， 每次 Widget 树触发的改变，并不一定会导致RenderObject 树的完全更新。

所以在实现原理上 React Native 和 Flutter 是完全不同的思路，虽然都有类似“虚拟 DOM 的概念” ，但是React Native 带有较强的平台关联性，而 Flutter UI 的平台关联性十分薄弱。



## 关于性能

跨平台开发第一个考虑的就是性能问题 
RN的效率由于是将View编译成了原生View,所以效率上要比基于Cordova的HTML5高很多,但是它也有效率问题,RN的渲染机制是基于前端框架的考虑,复杂的UI渲染是需要依赖多个view叠加.比如我们渲染一个复杂的ListView,每一个小的控件,都是一个native的view,然后相互组合叠加.想想此时如果我们的list再需要滑动刷新,会有多少个对象需要渲染.所以也就有了前面所说的RN的列表方案不友好; 

Flutter 吸收了前两者的教训之后,在渲染技术上,选择了自己实现(GDI),由于有更好的可控性,使用了新的语言Dart,避免了RN的那种通过桥接器与Javascript通讯导致效率低下的问题,所以在性能方面比RN更高一筹;有经验的开发者可以打开Android手机开发者选项里面的显示边界布局,发现Flutter的布局是一个整体.说明Flutter的渲染没用使用原生控件进行渲染 

## 关于开发体验

Hot Reload(热重载),这个对于原生开发者是一个福音,特别是Android开发者,Android原生改一个东西运行需要好久时间;对于Cordova和RN,差别不大,两个基本都可以支持热重载 

## Widget(组件) 

这点和RN的component有些类似,但是比RN更彻底,Flutter自身提供了很多基于 Material Design and Cupertino (iOS-flavor) 风格Widget,这点也和Google对它的定位有关(Flutter is Google’s mobile UI framework),大家在开发的时候更简单,特别对于美工UI设计师薄弱中小公司更是福音;​ 其他 

Dart Flutter使用了新的开发语言,这个语言相信大家都没听说过,这个google推出的新的编程语言,属于动态语言,”结构化的web编程”语言,语法与Javascript 的ES6标准有些类似,有Java和C#语言基础,理解起来不难.缺点是程序猿们又要学习一门新语言了; 
除了效率和Widget之外,最期待的还是Flutter的渲染方式,不说跨平台的开发语言,就单单Android原生开发,不同的手机对不同控件的渲染可能就有不同,希望Flutter自己的渲染平台能解决这个问题 

## Flutter 和 ReactNative 的区别 

从实现原理上来讲 ReactNative 提供的组件都是继承自原生 Native 的 View 组件，比如ReactNative 中的 ListView 在 Android 中就是继承自 ListView ，还有 RecycleView。然而 Flutter 则不同，它的所有 UI 组件都是一帧一帧画出来的。这样也能够很准确，也很灵活的做出你想要的 UI 。其次它还非常人性化的贴近了平台的特性，比如 Android 的 Material Design 在 Flutter 就默认支持了进去。其实话说回来，在开发者角度来讲这两个跨平台都是一样的使用效果，毕竟都是通过一套语言来搭建可运行不同平台的应用。然而，Flutter 使用 Dart 语言开发而 ReactNative 则使用 JS 结合 XML 来开发的。这就有问题了。

## React Native VS Flutter评测 

React Native较小的安装包带来的优势不仅是降低了下载门槛、减少了磁盘占用, 而且对于混合开发的压力也更小. 试想一个原生App因为几个页面使用了Flutter, 一行代码没写安装包就凭空增加了30MB, 这样的结果不是开发人员希望看到的. 

启动速度RN依然与原生不相上下, 并且要比Flutter好上一个等级. RN虽然使用js来构建应用程序, 但最终RN会将js转化成原生代码. 这就让RN的运行效率高出Flutter好几倍. 

内存占用方面, RN显然没有原生做的好, 与Flutter的差距也不大, 而且经测试发现, RN的内存占用不是很稳定, 页面刚生成的时候内存占用会高一点, 之后缓慢回落. 

## 高频数据交换下Flutter与ReactNative的对比 

ReactNative在满帧的情况下实际是卡住的，不能切换路由，后端服务关闭后仍然会执行一段时间，证明有事件堆积。 

而Flutter不会影响路由切换，因为Flutter的路由切换在ui主线程上，而react-navigation跑在js线程上。Flutter虽不满帧但可以实时更新数据。