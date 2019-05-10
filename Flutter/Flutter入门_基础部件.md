#### Flutter入门基础部件
**一.简单了解**
新建 flutter 项目后，可以看到 lib 下的 main.dart 中 void main() => runApp(MyApp());这句就是程序的入口了,这里可以简单看下源码
```java
void runApp(Widget app) {
  WidgetsFlutterBinding.ensureInitialized()
    ..attachRootWidget(app)
    ..scheduleWarmUpFrame();
}

///....
static WidgetsBinding ensureInitialized() {
  if (WidgetsBinding.instance == null)
    WidgetsFlutterBinding();
  return WidgetsBinding.instance;
}

///....
void attachRootWidget(Widget rootWidget) {
  _renderViewElement = RenderObjectToWidgetAdapter<RenderBox>(
    container: renderView,
    debugShortDescription: '[root]',
    child: rootWidget
  ).attachToRenderTree(buildOwner, renderViewElement);
}
```
首先会创建一个 WidgetsBinding 单例对象，然后把传入的 App 添加到 rootWidget中，scheduleWarmUpFrame 方法比较长，这边看下对该方法的注释第一句就能了解方法的主要功能了

**二.MaterialApp 的构造函数，常用的参数**
(看下 MyApp 这个类，继承自 StatelessWidget 并在 build 方法返回一个 MaterialApp 实例)
```java
const MaterialApp({
    Key key,
    this.navigatorKey,
    this.home, // 主界面的内容 widget
    this.routes = const <String, WidgetBuilder>{}, // 带 router 和路由跳转有关
    this.initialRoute,
    this.onGenerateRoute,
    this.onUnknownRoute,
    this.navigatorObservers = const <NavigatorObserver>[], 
    this.builder,
    this.title = '', // *类似标题
    this.onGenerateTitle, // 主要用于多语言情况下，需要根据当前语言替换 title，需要使用该值
    this.color, // 主题色，如果该值未设置，取 theme.primaryColor,未设置 theme 则取蓝色
    this.theme, // App 的主题风格，包括主题色，按钮默认颜色等等
    this.locale, // 带 locale 的和多语言适配相关
    this.localizationsDelegates,
    this.localeListResolutionCallback,
    this.localeResolutionCallback,
    this.supportedLocales = const <Locale>[Locale('en', 'US')],
    this.debugShowMaterialGrid = false, 
    this.showPerformanceOverlay = false,
    this.checkerboardRasterCacheImages = false,
    this.checkerboardOffscreenLayers = false,
    this.showSemanticsDebugger = false,
    this.debugShowCheckedModeBanner = true, // debug 模式下，是否显示 DEBUG 标示横幅
  })
```
MaterialApp 继承自 StatefulWidget，它和 MyApp 所继承的类 StatelessWidget，就是日常开发中，自定义部件通常继承的抽象类了。
1. StatelessWidget 是状态不可变部件，通过其构建的部件一般用来展示固定内容，例如需要展示固定的功能按钮列表，不需要根据不同界面状态进行修改其展示内容
2. StatefulWidget 是可改变状态的部件，比如我们需要通过网络或者数据库获取数据，然后修改部件锁展示的数据内容，则需要通过 StatefulWidget 来构建。

**三.Scaffold**
Flutter 提供了 Scaffold 来快速构建一个 MaterialDesign 风格的界面，还是先看下 Scaffold 的构造函数
```java
const Scaffold({
    Key key,
    this.appBar, // 界面顶部的那条栏，这边需要返回一个 AppBar 实例
    this.body, // 界面的内容部分
    this.floatingActionButton, // 悬浮部分，可以通过 floatingActionButtonLocation 设置位置
    this.floatingActionButtonLocation,
    this.floatingActionButtonAnimator,
    this.persistentFooterButtons,
    this.drawer, // 侧滑抽屉部分，从左侧滑出(应该和语言有关，和文字方向同向)
    this.endDrawer, // 侧滑抽屉部分，从右侧滑出
    this.bottomNavigationBar, // 底部导航栏，就是通常看到的底部 TAB 切换部件
    this.bottomSheet, // 展示从底部弹出的，起到提示作用的，通过 showModalBottomSheet 展示
    this.backgroundColor, // 界面的背景色
    this.resizeToAvoidBottomPadding = true, // 避免 body 被底部弹出部件填充，例如输入法键盘
    this.primary = true, // 当前的 Scaffold 是否需要被展示在屏幕最上层
  })
```
![简单明了的图](https://raw.githubusercontent.com/wangcaiwen5/Notes_New/master/Image/640.png "简单明了的图")

****

**四.各个 Widget的使用方法**
```java
AppBar({
    Key key,
    this.leading, // 用于设置 AppBar 前置的按钮，例如设置返回我们需要的返回按钮等
    this.automaticallyImplyLeading = true, // 是否使用系统默认生成的按钮，如果替换 leading 的默认按钮，最好将该属性设置成 false
    this.title, // AppBar 所需要展示的组件，传入一个 Widget 实例，通常使用 Text 展示一个标题
    this.actions, // AppBar 末尾悬浮的一些操作组件，例如常见的会在末尾设置一个「...」按钮，点击弹出一个 menue 提供给用户操作选择
    this.flexibleSpace, // AppBar 的背景，可以设置颜色，背景图等等
    this.bottom, // bottom 用于展示顶部导航 TAB
    this.elevation = 4.0,
    this.backgroundColor, // AppBar 的背景色，如果只需要修改颜色，可以不通过 flexibleSpace 修改
    this.brightness,
    this.iconTheme, // 按钮的默认样式
    this.textTheme, // 文字的默认样式
    this.primary = true,
    this.centerTitle, // 是否将展示的 title 居中
    this.titleSpacing = NavigationToolbar.kMiddleSpacing, // AppBar title 两侧的空白间隔
    this.toolbarOpacity = 1.0,
    this.bottomOpacity = 1.0,
  })
```

**五.几个基本的组件 (Text、Image、Icon、Button 分布用于展示文字，图片，图标，按钮)**

*1.Text*
```java
const Text(this.data, { // Text 需要展示的文字
    Key key,
    this.style, // 文字的样式，包括颜色，大小，间距等等属性，这边就不继续展示 TextStyle 构造函数了，不然我怕大家都不想继续看了，稍后通过例子来说明
    this.textAlign, // 文字的对齐方式，包括左对齐，右对齐，居中等，详见 TextAlign 类
    this.textDirection, // 文字方向，ltr(left to right) 或者 rtl(right to left)
    this.locale,
    this.softWrap, // 当文字一行显示不完是否换行
    this.overflow, // 如果超出限制的行数，以哪种方式省略未展示的内容
    this.textScaleFactor, // 文字缩放比例
    this.maxLines, // 最多展示的行数
    this.semanticsLabel,
  })
```
撸代码看看
```java
import 'package:flutter/material.dart';

void main() => runApp(DemoApp());

class DemoApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      debugShowCheckedModeBanner: false,
      theme: ThemeData(primarySwatch: Colors.lightBlue),
      home: HomePage(),
    );
  }
}

class HomePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
        appBar: AppBar(),
        body: Container(
          padding: const EdgeInsets.only(top: 10.0),
          child: Center(
              child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: <Widget>[
              Text('设置字体颜色 大小  字体间的间距  背景颜色',
                  style: TextStyle(
                      color: Colors.black, // 设置文字颜色，不可和 foreground 同时设置
                      fontSize: 24.0, // 字体大小
                      letterSpacing: 2.0, // 每个字符之间的间隔
                      background: Paint()..color = Colors.green)), // 背景色
                      // 文字装饰线，除了 underline 还有 overline, lineThrough，
                      // 不同的样式小伙伴可以通过自己修改代码来查看
                      decoration: TextDecoration.underline,
                      // 文字装饰线的类型，除了 solid 还有 double,dotted,dashed,wavy 可选
                      decorationStyle: TextDecorationStyle.solid,
                      // 装饰线的颜色
                      decorationColor: Colors.red))

            ],
          )),
        ));
  }
}
```
*2.Image*

 Image 的构造函数:
 ```java
const Image({
    Key key,
    // 一个 ImageProvider 实例，但是 ImageProvider 是一个抽象类，Flutter 已经给我们提供如下
    // AssetImage，NetworkImage，FileImage，MemoryImage 这四种图片加载器，为了方便调用
    // 我们可以直接通过 Image.asset, Image.network, Image.file, Image.memory 简化，
    // 通过方法名，可以看出分别从 asset 文件，网络，文件，内存中加载图片
    @required this.image,
    this.semanticLabel,
    this.excludeFromSemantics = false,
    this.width, // 图片宽度
    this.height, // 图片高度
    this.color, // 图片背景色
    this.colorBlendMode, // color 和图片的混合模式(这个值比较多，可以一个个尝试)
    this.fit, // 图片填充方式 fill, cover, contain, fillWidth, fillHeight, scaleDown, none
    this.alignment = Alignment.center, // 对齐方式
    this.repeat = ImageRepeat.noRepeat, // 若未填充满空间，重复展示的方式
    this.centerSlice,
    this.matchTextDirection = false,
    this.gaplessPlayback = false,
    this.filterQuality = FilterQuality.low,
  })
```

撸代码:
















