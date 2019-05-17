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

**五.几个基本的组件 (Text、Image、Icon、Button 分布用于展示文字，图片，图标，按钮)
**
###### 1.Text
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
######  Image 的构造函数:
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
在这之前，需要你先准备一张本地图片，然后在项目的根目录，也就是 lib 文件夹同层，创建一个新的文件夹，命名为 images，把你准备好的图片放到这个目录下。放好之后打开 pubspec.yaml 把图片资源文件注册下
    # The following section is specific to Flutter.
    flutter:
    
      # The following line ensures that the Material Icons font is
      # included with your application, so that you can use the icons in
      # the material Icons class.
      uses-material-design: true
    
      # 这边注册资源文件，以后有图片文件也可以只注册 images 文件夹，会自动读取内部的文件
      assets:
        - images/ali.jpg
撸代码:
```dart
class HomePage extends StatelessWidget {
  final String _assetAli = 'images/ali.jpg';
  final String _picUrl =
      'https://timg05.bdimg.com/timg?wapbaike&quality=60&size=b1440_952&cut_x=143&cut_y=0&cut_w=1633&'
      'cut_h=1080&sec=1349839550&di=cbbc175a45ccec5482ce2cff09a3ae34&'
      'src=http://imgsrc.baidu.com/baike/pic/item/4afbfbedab64034f104872baa7c379310b551d80.jpg';

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
              // 这种展示图片方式和下一种会有相同的效果
              Image(image: AssetImage(_assetAli), width: 80.0, height: 80.0),
              // 接下来加载图片都会使用这些比较方便的方法
              Image.asset(_assetAli, width: 80.0, height: 80.0),
              // 加载一张网络图片
              Image.network(_picUrl,
                  height: 80.0,
                  // 横向重复
                  repeat: ImageRepeat.repeatX,
                  // MediaQuery.of(context).size 获取到的为上层容器的宽高
                  width: MediaQuery.of(context).size.width),
              // 通过设置混合模式，可以看到图片展示的样式已经修改
              Image.asset(_assetAli,
                  width: 80.0, height: 80.0, color: Colors.green, colorBlendMode: BlendMode.colorDodge),
              // 会优先加载指定的 asset 图片，然后等网络图片读取成功后加载网络图片，会通过渐隐渐现方式展现
              // cover 方式按照较小的边布满，较大的给切割
              // contain 会按照最大的边布满，较小的会被留白
              // fill 会把较大的一边压缩
              // fitHeight, fitWidth 分别按照长宽来布满
              FadeInImage.assetNetwork(
                  placeholder: _assetAli, image: _picUrl, width: 120.0, height: 120.0, fit: BoxFit.cover),
              // Icon 相对属性少了很多，需要传入一个 IconData 实例，flutter 提供了很多图标，
              // 但是实际情况我们需要加入我们自己的图标，这边再埋坑【坑3】
              // size 为图标显示的大小，color 为图标的颜色，这边通过 Theme 获取主题色调
              Icon(Icons.android, size: 40.0, color: Theme.of(context).primaryColorDark)
            ],
          )),
        ));
  }
}
```

Flutter 提供了各种类型的 Button 几乎是大同小异的，这边就抽取一些比较常用的展示下效果，常用的主要有 RaisedButton 、FlatButton、IconButton、OutlineButton、MaterialButton、FloatActionButton、FloatingActionButton.extended


Button 都有一个 onPress 参数，是 VoidCallback 类型的参数，通过查看源码可以知道 VoidCallback 是无参无返回值的一种类型参数。如果该参数传入的值为 null 那么这个按钮的就不可点击状态，无点击效果，等会可以在例子中查看。还有就是 child 参数，这里就是传入你需要展示的内容，比如 Text、Icon 等等。别的参数基本可以通过参数名了解:

```dart
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
            RaisedButton(
              onPressed: () {
                print('This is a Rased Button can be clicked');
              },
              child: Text('Raised Enable'),
            ),
            RaisedButton(onPressed: null, child: Text('Raised Disable')),
            FlatButton(
              onPressed: () => print('This is a Flat Button can be clicker'),
              child: Text('Flat Enable'),
            ),
            FlatButton(onPressed: null, child: Text('Flat Disable')),
            IconButton(icon: Icon(Icons.android), onPressed: () {}),
            IconButton(icon: Icon(Icons.android), onPressed: null),
            MaterialButton(onPressed: () {}, child: Text('Material Enable')),
            MaterialButton(onPressed: null, child: Text('Material Disable')),
            OutlineButton(onPressed: () {}, child: Text('Outline Enable')),
            OutlineButton(onPressed: null, child: Text('Outline Enable')),
          ],
        )),
      ),
      floatingActionButton:
          FloatingActionButton.extended(onPressed: () {}, icon: Icon(Icons.android), label: Text('Android')),
      floatingActionButtonLocation: FloatingActionButtonLocation.centerDocked,
    );
  }
}
```

















