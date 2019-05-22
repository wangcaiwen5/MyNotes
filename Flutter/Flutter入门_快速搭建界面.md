![flutter图解](https://raw.githubusercontent.com/wangcaiwen5/MyNotes/master/Image/flutter_%E8%84%91%E5%9B%BE.png "flutter图解")

------------


**Appbar**

------------

稍后会用到 StatefulWidget 的属性，所以就直接先使用了，和 StatelessWidget 区别用法可以这么记 需要数据更新的界面用 StatefulWidget，当然也不是绝对的
```dart
class HomePage extends StatefulWidget {
  @override
  _HomePageState createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> {
   List<String> _abs = ["设置", "扫一扫", "付款"];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        centerTitle: true, // 标题内容居中
        automaticallyImplyLeading: false, // 不使用默认
        leading: Icon(Icons.menu, color: Colors.red, size: 30.0), // 左侧按钮
        flexibleSpace: Image.asset('images/app_bar_hor.jpg', fit: BoxFit.cover), // 背景
        title: Text('AppBar Demo', style: TextStyle(color: Colors.red)), // 标题内容
        // 末尾的操作按钮列表
        actions: <Widget>[
          PopupMenuButton(
              onSelected: (val) => print('Selected item is $val'),
              icon: Icon(Icons.more_vert, color: Colors.red),
              itemBuilder: (context) =>
                  List.generate(_abs.length, (index) => PopupMenuItem(value: _abs[index], child: Text(_abs[index]))))
        ],
      ),
    );
  }
}
```
加上如下就可以看到那层丑丑的「半透明蒙层」没有了

```dart
void main() {
  runApp(DemoApp());

  // 添加如下代码，使状态栏透明
  if (Platform.isAndroid) {
    var style = SystemUiOverlayStyle(statusBarColor: Colors.transparent);
    SystemChrome.setSystemUIOverlayStyle(style);
  }
}
```

介绍下 PopupMenuButton 这个部件，还是按照惯例看构造函数

```dart
// itemBuilder
typedef PopupMenuItemBuilder<T> = List<PopupMenuEntry<T>> Function(BuildContext context);
// onSelected
typedef PopupMenuItemSelected<T> = void Function(T value);

const PopupMenuButton({
    Key key,
    @required this.itemBuilder, // 用于定义 menu 列表，需要传入 List<PopupMenuEntry<T>>
    this.initialValue, // 初始值，是个泛型 T，也就是类型和你传入的值有关
    this.onSelected, // 选中 item 的回调函数，返回 T value，例如选中 `s` 则返回 s
    this.onCanceled, // 未选择任何 menu，直接点击外侧使 mune 列表关闭的回调
    this.tooltip, // 长按时的提示
    this.elevation = 8.0,
    this.padding = const EdgeInsets.all(8.0),
    this.child, // 用于自定义按钮的内容
    this.icon, // 按钮的图标
    this.offset = Offset.zero, // 展示时候的便宜，Offset 需要传入 x,y 轴偏移量，会根据传入值平移
  })
```

#### AppBar 还有个 bottom 属性
```dart
// 这里需要用 with 引入 `SingleTickerProviderStateMixin` 这个类
class _HomePageState extends State<HomePage> with SingleTickerProviderStateMixin {
    List<String> _abs = ["设置", "扫一扫", "付款"];
  TabController _tabController; // TabBar 必须传入这个参数

  @override
  void initState() {
    super.initState();
    // 引入 `SingleTickerProviderStateMixin` 类主要是因为 _tabController 需要传入 vsync 参数
    _tabController = TabController(length: _abs.length, vsync: this);
  }

  @override
  void dispose() {
    // 需要在界面 dispose 之前把 _tabController dispose，防止内存泄漏
    _tabController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        centerTitle: true,
        automaticallyImplyLeading: false,
        leading: Icon(Icons.menu, color: Colors.red, size: 30.0),
//        flexibleSpace: Image.asset('images/app_bar_hor.jpg', fit: BoxFit.cover),
        title: Text('AppBar Demo', style: TextStyle(color: Colors.red)),
        actions: <Widget>[
          PopupMenuButton(
              offset: Offset(50.0, 100.0),
              onSelected: (val) => print('Selected item is $val'),
              icon: Icon(Icons.more_vert, color: Colors.red),
              itemBuilder: (context) =>
                  List.generate(_abs.length, (index) => PopupMenuItem(value: _abs[index], child: Text(_abs[index]))))
        ],
        bottom: TabBar(
            labelColor: Colors.red, // 选中时的颜色
            unselectedLabelColor: Colors.white, // 未选中颜色
            controller: _tabController,
            isScrollable: false, // 是否固定，当超过一定数量的 tab 时，如果一行排不下，可设置 true
            indicatorColor: Colors.yellow, // 导航的颜色
            indicatorSize: TabBarIndicatorSize.tab, // 导航样式，还有个选项是 TabBarIndicatorSize.label tab 时候，导航和 tab 同宽，label 时候，导航和 icon 同宽
            indicatorWeight: 5.0, // 导航高度
            tabs: List.generate(_abs.length, (index) => Tab(text: _abs[index], icon: Icon(Icons.android)))), // 导航内容列表
      ),
    );
  }
}
```
放个效果图看看吧,自我感觉还挺好看,哈哈
![图](https://raw.githubusercontent.com/wangcaiwen5/MyNotes/master/Image/%E5%B1%8F2019-05-17%20%E4%B8%8B%E5%8D%884.58.09.png "图")


------------


#### 我们再看看PageView + TabBar

------------
通过 TabBar 切换界面呢，这边我们需要用到 PageView 这个部件，当然还有别的部件，例如 IndexStack 等，小伙伴可以自己尝试使用别的，这边通过 PageView 和 TabBar 进行关联，带动页面切换，PageViede 的属性参数相对比较简单，这边就不贴啦。最终的效果我们目前只展示一个文字即可，我们先定义一个通用的切换界面

```dart
class TabChangePage extends StatelessWidget {
  // 需要传入的参数
  final String content;

  // TabChangePage(this.content); 不推荐这样写构造方法

  // 推荐用这样的构造方法，key 可以作为唯一值查找
  TabChangePage({Key key, this.content}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    // 仅展示传入的内容
    return Container(
        alignment: Alignment.center, child: Text(content, style: TextStyle(color: Theme.of(context).primaryColor, fontSize: 30.0)));
  }
}
```
定义通用界面后，就可以作为 PageView 的子界面传入并展示

```dart
class HomePage extends StatefulWidget {
  @override
  _HomePageState createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> with SingleTickerProviderStateMixin {
  List<String> _abs = ["设置", "扫一扫", "付款"];
  TabController _tabController;
  // 用于同 TabBar 进行联动
  PageController _pageController;

  @override
  void initState() {
    super.initState();
    _tabController = TabController(length: _abs.length, vsync: this);
    _pageController = PageController(initialPage: 0);

    _tabController.addListener(() {
      // 判断 TabBar 是否切换位置了，如果切换了，则修改 PageView 的显示
      if (_tabController.indexIsChanging) {
        // PageView 的切换通过 controller 进行滚动
        // duration 表示切换滚动的时长，curve 表示滚动动画的样式，
        // flutter 已经在 Curves 中定义许多样式，可以自行切换查看效果
        _pageController.animateToPage(_tabController.index,
            duration: Duration(milliseconds: 300), curve: Curves.decelerate);
      }
    });
  }

  @override
  void dispose() {
    _tabController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        centerTitle: true,
        automaticallyImplyLeading: false,
        leading: Icon(Icons.menu, color: Colors.red, size: 30.0),
//        flexibleSpace: Image.asset('images/app_bar_hor.jpg', fit: BoxFit.cover),
        title: Text('AppBar Demo', style: TextStyle(color: Colors.red)),
        actions: <Widget>[
          PopupMenuButton(
              offset: Offset(50.0, 100.0),
              onSelected: (val) => print('Selected item is $val'),
              icon: Icon(Icons.more_vert, color: Colors.red),
              itemBuilder: (context) =>
                  List.generate(_abs.length, (index) => PopupMenuItem(value: _abs[index], child: Text(_abs[index]))))
        ],
        bottom: TabBar(
            labelColor: Colors.red,
            unselectedLabelColor: Colors.white,
            controller: _tabController,
            isScrollable: false,
            indicatorColor: Colors.yellow,
            indicatorSize: TabBarIndicatorSize.tab,
            indicatorWeight: 5.0,
            tabs: List.generate(_abs.length, (index) => Tab(text: _abs[index], icon: Icon(Icons.android)))),
      ),
      // 通过 body 来展示内容，body 可以传入任何 Widget，里面就是你需要展示的界面内容
      // 所以前面留下 Scaffold 中 body 部分的坑就解决了
      body: PageView(
        controller: _pageController,
        children:
            _abs.map((str) => TabChangePage(content: str)).toList(), // 通过 Map 转换后再通过 toList 转换成列表，效果同 List.generate
        onPageChanged: (position) {
          // PageView 切换的监听，这边切换 PageView 的页面后，TabBar 也需要随之改变
          // 通过 tabController 来改变 TabBar 的显示位置
          _tabController.index = position;
        },
      ),
    );
  }
}
```
效果图如下:
![效果图](https://raw.githubusercontent.com/wangcaiwen5/MyNotes/master/Image/44444.gif "效果图")


Scaffold 能够使我们快速去搭建一个界面，但是，并不是所有的界面都需要 AppBar 这个标题，那么我们就不会传入 appBar 的属性，我们注释 _HomePageState 中 Scaffold 的 appBar 传入值，把 body 传入的 PageView 修改成单个 TabChangePage ，然后把 TabChangePage 这个类做下修改，把 Container 的 aligment 属性也注释了，这样显示的内容就会显示在左上角

```dart
// _HomePageState
// ..
@override
  Widget build(BuildContext context) {
    return Scaffold(body: TabChangePage(content: 'Content'));
  }

class TabChangePage extends StatelessWidget {
  final String content;
    
  TabChangePage({Key key, this.content}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Container(child: Text(content, style: TextStyle(color: Theme.of(context).primaryColor, fontSize: 30.0)));
  }
}
```


然后运行下，「**，文字怎么被状态栏给挡了...」
不要慌，静下心喝杯茶，眺望下远方，这里就需要用 SafeArea 来处理了，在 TabChangePage 的 Container 外层加一层 SafeArea

```dart
@override
  Widget build(BuildContext context) {
    return SafeArea(
        child:
            Container(child: Text(content, style: TextStyle(color: Theme.of(context).primaryColor, fontSize: 30.0))));
  }
```

SafeArea 的用途[我用翻译软件翻译的,哈哈]:
翻译过来大概就是「给子部件和系统点击无效区域留有足够空间，比如状态栏和系统导航栏」
SafeArea 可以很好解决刘海屏覆盖页面内容的问题

#### Scaffold - Drawer
drawer 同 endDrawer 属性是一样的，除了滑动的方向，Drawer 这个组件也相对比较简单，只要传入一个 child 即可，在展示之前，先对 appBar 做下处理，设置 leading 为系统默认，点击 leading 的时候 Drawer 就可以滑出来了，当然手动滑也可以

```dart
@override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        centerTitle: true,
//        automaticallyImplyLeading: false,
//        leading: Icon(Icons.menu, color: Colors.red, size: 30.0),
//        flexibleSpace: Image.asset('images/app_bar_hor.jpg', fit: BoxFit.cover),
        title: Text('AppBar Demo', style: TextStyle(color: Colors.red)),
        actions: <Widget>[
          PopupMenuButton(
              offset: Offset(50.0, 100.0),
              onSelected: (val) => print('Selected item is $val'),
              icon: Icon(Icons.more_vert, color: Colors.red),
              itemBuilder: (context) =>
                  List.generate(_abs.length, (index) => PopupMenuItem(value: _abs[index], child: Text(_abs[index]))))
        ],
        bottom: TabBar(
            labelColor: Colors.red,
            unselectedLabelColor: Colors.white,
            controller: _tabController,
            isScrollable: false,
            indicatorColor: Colors.yellow,
            indicatorSize: TabBarIndicatorSize.tab,
            indicatorWeight: 5.0,
            tabs: List.generate(_abs.length, (index) => Tab(text: _abs[index], icon: Icon(Icons.android)))),
      ),
      // body ....  设置抽屉
      drawer: Drawer(
        // 记得要先添加 `SafeArea` 防止视图顶到状态栏下面
        child: SafeArea(
            child: Container(
          child: Text('drawer', style: TextStyle(color: Theme.of(context).primaryColor, fontSize: 20.0)),
        )),
      ),
    );

    //    return Scaffold(body: TabChangePage(content: 'Content'));
  }
```


#### AppBar - bottomNavigationBar

bottomNavigarionBar 可以传入一个 BottomNavigationBar 实例，BottomNavigationBar 需要传入 BottomNavigationBarItem 列表作为 items ，但是这边为了实现一个 bottomNavigationBar 和 floatingActionButton 一个特殊的组合效果，我们不使用 BottomNavigationBar，换做 BottomAppBar，直接上代码吧

```dart
@override
  Widget build(BuildContext context) {
    return Scaffold(
      /// 一样的代码省略....
      bottomNavigationBar: BottomAppBar(
        shape: CircularNotchedRectangle(),
        child: Row(
          mainAxisSize: MainAxisSize.max,
          mainAxisAlignment: MainAxisAlignment.spaceAround,
          children: <Widget>[
            IconButton(icon: Icon(Icons.android, size: 30.0, color: Theme.of(context).primaryColor), onPressed: () {}),
            IconButton(icon: Icon(Icons.people, size: 30.0, color: Theme.of(context).primaryColor), onPressed: () {})
          ],
        ),
      ),
      floatingActionButton:
          FloatingActionButton(onPressed: () => print('Add'), child: Icon(Icons.add, color: Colors.white)),
      // FAB 的位置，一共有 7 中位置可以选择，centerDocked, endDocked, centerFloat, endFloat, endTop, startTop, miniStartTop，这边选择悬浮在 dock
      floatingActionButtonLocation: FloatingActionButtonLocation.centerDocked,
    );
```
最终的效果图：
![效果图](https://raw.githubusercontent.com/wangcaiwen5/MyNotes/master/Image/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-05-22%20%E4%B8%8B%E5%8D%882.48.57.png "效果图")

既然提到了 StatefulWidget，顺带提下两种比较简单的部件，也算是基础部件吧。CheckBox、CheckboxListTile，Switch、SwitchListTile 因为比较简单，就直接上代码了，里面都有完整的注释

```dart
class CheckSwitchDemoPage extends StatefulWidget {
  @override
  _CheckSwitchDemoPageState createState() => _CheckSwitchDemoPageState();
}

class _CheckSwitchDemoPageState extends State<CheckSwitchDemoPage> {
  var _isChecked = false;
  var _isTitleChecked = false;
  var _isOn = false;
  var _isTitleOn = false;

  @override
  void initState() {
    super.initState();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Check Switch Demo'),
      ),
      body: Column(children: <Widget>[
        Row(
          children: <Widget>[
            Checkbox(
              // 是否开启三态
              tristate: true,
              // 控制当前 checkbox 的开启状态
              value: _isChecked,
              // 不设置该方法，处于不可用状态
              onChanged: (checked) {
                // 管理状态值
                setState(() => _isChecked = checked);
              },
              // 选中时的颜色
              activeColor: Colors.pink,
              // 这个值有 padded 和 shrinkWrap 两个值，
              // padded 时候所占有的空间比 shrinkWrap 大，别的原谅我没看出啥
              materialTapTargetSize: MaterialTapTargetSize.shrinkWrap,
            ),

            /// 点击无响应
            Checkbox(value: _isChecked, onChanged: null, tristate: true)
          ],
        ),
        Row(
          children: <Widget>[
            Switch(
                // 开启时候，那个条的颜色
                activeTrackColor: Colors.yellow,
                // 关闭时候，那个条的颜色
                inactiveTrackColor: Colors.yellow[200],
                // 设置指示器的图片，当然也有 color 可以设置
                activeThumbImage: AssetImage('images/ali.jpg'),
                inactiveThumbImage: AssetImage('images/ali.jpg'),
                // 开始时候的颜色，貌似会被 activeTrackColor 顶掉
                activeColor: Colors.pink,
                materialTapTargetSize: MaterialTapTargetSize.shrinkWrap,
                value: _isOn,
                onChanged: (onState) {
                  setState(() => _isOn = onState);
                }),

            /// 点击无响应
            Switch(value: _isOn, onChanged: null)
          ],
        ),
        CheckboxListTile(
          // 描述选项
          title: Text('Make this item checked'),
          // 二级描述
          subtitle: Text('description...description...\ndescription...description...'),
          // 和 checkbox 对立边的部件，例如 checkbox 在头部，则 secondary 在尾部
          secondary: Image.asset('images/ali.jpg', width: 30.0, height: 30.0),
          value: _isTitleChecked,
          // title 和 subtitle 是否为垂直密集列表中一员，最明显就是部件会变小
          dense: true,
          // 是否需要使用 3 行的高度，该值为 true 时候，subtitle 不可为空
          isThreeLine: true,
          // 控制 checkbox 选择框是在前面还是后面
          controlAffinity: ListTileControlAffinity.leading,
          // 是否将主题色应用到文字或者图标
          selected: true,
          onChanged: (checked) {
            setState(() => _isTitleChecked = checked);
          },
        ),
        SwitchListTile(
            title: Text('Turn On this item'),
            subtitle: Text('description...description...\ndescription...description...'),
            secondary: Image.asset('images/ali.jpg', width: 30.0, height: 30.0),
            isThreeLine: true,
            value: _isTitleOn,
            selected: true,
            onChanged: (onState) {
              setState(() => _isTitleOn = onState);
            })
      ]),
    );
  }
}
```











