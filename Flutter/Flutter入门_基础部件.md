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

三.Scaffold
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















