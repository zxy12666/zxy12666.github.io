title: Flutter国际化设置在iOS设备上不生效的问题
author: 张翔宇
tags:
  - flutter
categories:
  - flutter
date: 2019-05-16 11:54:00
---
参考官方资料，添加了中文国际化相关配置代码：

    class App extends StatelessWidget {
      // This widget is the root of your application.
      @override
      Widget build(BuildContext context) {
        return new MaterialApp(
          title: '饭点',
          theme: new ThemeData(
            primarySwatch: Colors.blue,
          ),
          home: new MainPage(title: '饭点'),
          localizationsDelegates: [
            GlobalMaterialLocalizations.delegate,
            GlobalWidgetsLocalizations.delegate,
          ],
          supportedLocales: [
            const Locale('zh', 'CH'),
            const Locale('en', 'US'),
          ],
        );
      }
    }

实际运行发现，即便设备已经设置了中文和中国地区，但iOS设备上依旧显示英文，Android设备就没有这个问题。

俺做iOS已经是6年前的事情了，现在可以说开发经验几乎归零了，摸索了一顿找到了解决方案。

使用Xcode打开ios项目：

![](https://stanzhai.site/api/file/getImage?fileId=5bb5d12dba8bc2481f000436)

然后再项目设置里添加一个中文的国际化配置：

![](https://stanzhai.site/api/file/getImage?fileId=5bb5d12dba8bc2481f000437)

然后在重新打包就可以了。

</div>