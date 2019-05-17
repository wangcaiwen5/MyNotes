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
   List<String> _abs = ['A', 'B', 'S'];
 
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
 
 AppBar 还有个 bottom 属性
 ```dart
 // 这里需要用 with 引入 `SingleTickerProviderStateMixin` 这个类
 class _HomePageState extends State<HomePage> with SingleTickerProviderStateMixin {
   List<String> _abs = ['A', 'B', 'S'];
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
 
 
 
 
 
 
