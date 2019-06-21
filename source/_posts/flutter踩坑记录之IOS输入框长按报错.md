title: flutter踩坑记录之IOS输入框长按报错
author: 张翔宇
tags:
  - flutter
categories:
  - flutter
date: 2019-03-08 11:25:00
---
<div data-note-content class="show-content">
    <div class="show-content-free">

开发过程中发现在国际化过程中ios输入框长按会报错 flutter: Another exception was thrown: NoSuchMethodError: The getter 'pasteButtonLabel' was called on null. 本篇说明解决方法:

1.首先在pubspec.yaml的dependencies下加入这个

flutter_localizations:

 sdk: flutter

如图：

![Markdown](http://i1.fuimg.com/691375/b153c965a4884a04.jpg)

2.然后在MaterialApp设置一下localizationsDelegates如图:

![Markdown](http://i1.fuimg.com/691375/6de2850a818dbe7cs.jpg)

3.写一个类继承一下CupertinoLocalizations，在项目中
            我这个类名叫ChineseCupertinoLocalizations(没错，就是localizationsDelegates中的第三个)，如图：
![Markdown](http://i1.fuimg.com/691375/7b468a74ab809741s.jpg)
![Markdown](http://i1.fuimg.com/691375/c607b43c8b0d861c.jpg)
 ![Markdown](http://i1.fuimg.com/691375/7c1017e35c4a584cs.jpg)
![Markdown](http://i1.fuimg.com/691375/7c1017e35c4a584cs.jpg)
上次提到报错的原因就是因为cutButtonLabel，copyButtonLabel，pasteButtonLabel，selectAllButtonLabel
            这几个按钮没有实现，所以继承CupertinoLocalizations一定要为这几个按钮赋值，这里是那种语言，那么，赋值就对应那种语言，同时要注意locale.languageCode也要填写，如果你是中文，那么locale.languageCode
            =='zh';

4.在ios工程中，在项目的info设置语言环境

添加 Localization native development region---&gt;china

添加一个Localizations 为array类型的，并且设置值为 Chinese (simplified)

![Markdown](http://i1.fuimg.com/691375/2492a8342b78c39es.jpg)

效果图：（**注意：请把手机环境调试成中文的语言环境**）

Android 
![Markdown](http://i1.fuimg.com/691375/a7c124b30c10fe42s.jpg)

ios 效果图
![Markdown](http://i1.fuimg.com/691375/89a99290c4de687as.jpg)

github地址

https://github.com/hxxsocket/flutter_lg_demo