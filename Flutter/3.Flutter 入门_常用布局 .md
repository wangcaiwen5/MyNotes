# Flutter 入门常用布局
#### 一.Container
为了让我们的界面更容易被扩展，通常会在最外层包裹一层 Container，其构造函数也不是很难理解
```dart
Container({
    Key key,
    this.alignment, // child 的对齐方式，包括左对齐，居中，右对齐，左上对齐..等等
    this.padding, // child 和 Container 的边距
    Color color, // Container 的背景色
    Decoration decoration, // 样式，可以设置背景图，圆角等属性
    this.foregroundDecoration, // child 的样式
    double width, // 宽度
    double height, // 高度
    BoxConstraints constraints, // 默认使用 BoxConstraints.tightFor，可以手动传入
    this.margin, // Container 同上层容器的边距
    this.transform, // 是个 Matrix4 矩阵，(嗯..这个参数基本很少用，没怎么了解 /捂脸)
    this.child, // 需要展示的内容
  })

// ...
const BoxConstraints.tightFor({
    double width,
    double height
  }): minWidth = width != null ? width : 0.0,
      maxWidth = width != null ? width : double.infinity,
      minHeight = height != null ? height : 0.0,
      maxHeight = height != null ? height : double.infinity;
```

让我们写个圆角矩形的外层，内层值显示白色文字
```dart
class HomePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: SafeArea(
          child: Container(
        alignment: Alignment.center,
        // 宽，高度同上层容器相同
        width: MediaQuery.of(context).size.width,
        height: MediaQuery.of(context).size.height,
        margin: const EdgeInsets.all(8.0),
        padding: const EdgeInsets.all(20.0),
        // Container 的样式
        decoration: BoxDecoration(
            borderRadius: BorderRadius.circular(20.0),
            color: Colors.red,
//            shape: BoxShape.circle, // 该属性不可同 borderRadius 一起使用
            backgroundBlendMode: BlendMode.colorDodge, // 背景图片和颜色混合模式
            image: DecorationImage(image: AssetImage('images/ali.jpg'), fit: BoxFit.cover)),
        child: Text('Container Text', style: TextStyle(color: Colors.white, fontSize: 30.0)),
//        color: Theme.of(context).primaryColor, // 该属性不可和 decoration 一起使用
      )),
    );
  }
}
```

上面代码可以看出 margin 和 padding 属性用来和别的部件保持间距，如果不用 Container 呢，当然没问题，有个专门用来设置间距的部件 Padding，看名字就可以看出来作用了，修改下 child 部分代码
```dart
child: Column(
          children: <Widget>[
            Text('Container Text', style: TextStyle(color: Colors.white, fontSize: 30.0)),
            Padding(
                // 需要传入一个间隔值，`Flutter` 提供了很多 EdgeInsets 来设置间隔，
                // 参数也很明确，可以一一尝试
                padding: const EdgeInsets.symmetric(vertical: 12.0),
                // 传入需要间隔的部件
                child: Text('Container Text', style: TextStyle(color: Colors.white, fontSize: 30.0)))
          ],
        ),
```

#### 二.Flex，Row，Column
 Android 应该比较常用 LinearLayout，在 Flutter 中用两个部件，Row Column来代替 Android 中的 LineLayout，其中 Row 是横向布局，Column 是垂直布局，因为 Row 和 Column 都是继承于 Flex 部件，Flex 比他们多了 direction 属性用来指定方向，所以主要拿 Column 来讲解，Flex 、Row 用法相同
```dart
Column({
    Key key,
    // 对齐方式，对于 `Column` start 为顶部，对于 `Row` 需要分语言，和语言同向
    // 3 种比较特殊的对齐方式，前端的小伙伴会了解，
    // spaceAround 两个部件之间的间隔是部件和上层容器间隔的两倍
    // spaceBetween 两侧部件同上层容器间隔为 0，部件之间的间隔相等
    // spaceEvenly 部件之间的间隔同两侧部件与上层容器间隔
    MainAxisAlignment mainAxisAlignment = MainAxisAlignment.start, 
    MainAxisSize mainAxisSize = MainAxisSize.max, // 主轴的大小
    CrossAxisAlignment crossAxisAlignment = CrossAxisAlignment.center, // 副轴对齐方式
    TextDirection textDirection, // 文字方向，决定 start
    VerticalDirection verticalDirection = VerticalDirection.down, // 垂直方向
    TextBaseline textBaseline,
    List<Widget> children = const <Widget>[], // 内部子部件
  })
```

```dart
Row 和 Column 都有主轴和副轴，如何区分呢，布局平行方向为主轴，垂直方向为副轴，我们把 Container 的 child 修改成 Column，然后把 Text 放到 Column 中，多放几个，然后自己设置 mainAxisAlignment 属性，查看布局的变化
```

```dart
// ... 省略相同代码
child: Column(
          mainAxisAlignment: MainAxisAlignment.spaceAround,
          children: <Widget>[
            Text('Container Text 1', style: TextStyle(color: Colors.white, fontSize: 30.0)),
            Text('Container Text 2', style: TextStyle(color: Colors.white, fontSize: 30.0)),
            Text('Container Text 3', style: TextStyle(color: Colors.white, fontSize: 30.0)),
            Text('Container Text 4', style: TextStyle(color: Colors.white, fontSize: 30.0)),
            Text('Container Text 5', style: TextStyle(color: Colors.white, fontSize: 30.0)),
          ],
        )
```

这边 Column 内部的子部件因为高度相同，如果不同还需要等分空间的话，就不可以通过设置 mainAxisAlignment 属性来实现了，这里介绍一个等分的部件 Expanded

```dart
const Expanded({
    Key key,
    int flex = 1, // 所占比例
    @required Widget child, // 子部件
  })
```

直接给 Text 外层加一个 Expanded 即可实现效果，当然可以按照需求来设置 flex 来修改比例值。
```dart
 child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            Expanded(flex: 1,child: Text('Container Text 1', style: TextStyle(color: Colors.green, fontSize: 30.0))),
            Expanded(flex: 2,child: Text('Container Text 1', style: TextStyle(color: Colors.green, fontSize: 30.0))),
            Expanded(flex: 3,child: Text('Container Text 1', style: TextStyle(color: Colors.green, fontSize: 30.0))),
            Expanded(flex: 4,child: Text('Container Text 1', style: TextStyle(color: Colors.green, fontSize: 30.0))),
            Expanded(flex: 5,child: Text('Container Text 1', style: TextStyle(color: Colors.green, fontSize: 30.0))),
          /*  Text('Container Text 1', style: TextStyle(color: Colors.green, fontSize: 30.0)) const Expanded(child: Text(data)),
            Text('Container Text 2', style: TextStyle(color: Colors.green, fontSize: 30.0)),
            Text('Container Text 3', style: TextStyle(color: Colors.green, fontSize: 30.0)),
            Text('Container Text 4', style: TextStyle(color: Colors.green, fontSize: 30.0)),
            Text('Container Text 5', style: TextStyle(color: Colors.green, fontSize: 30.0)),*/
          ],
        ),
```

这里记录一个坑,我们修改下代码，把 child 的代码修改成如下:
```dart
child: Row(
          children: <Widget>[
            Text('ABC' * 5, style: TextStyle(color: Colors.white, fontSize: 16.0)),
            Text('ABC' * 5, style: TextStyle(color: Colors.white, fontSize: 16.0)),
            Text('ABC' * 5, style: TextStyle(color: Colors.white, fontSize: 16.0)),
            Text('ABC' * 5, style: TextStyle(color: Colors.white, fontSize: 16.0)),
            Text('ABC' * 5, style: TextStyle(color: Colors.white, fontSize: 16.0))
          ],
        )
```

然后运行下，你的屏幕就提示你 RIGHT OVERFLOWED BY XXX PIXELS 「**,  *****」
我们把 Row 换成另一个布局 Wrap 然后再运行，Prefect，Wrap 和 Row 的参数基本类似
#### 三.Wrap

```dart
Wrap({
    Key key,
    this.direction = Axis.horizontal,
    this.alignment = WrapAlignment.start,
    this.spacing = 0.0, // 两个子部件之间的间隔，默认 0.0，如果值过大，可能导致原来同行的两个部件分行
    this.runAlignment = WrapAlignment.start, 
    this.runSpacing = 0.0, // 排布方向上 两个子部件的间隔
    this.crossAxisAlignment = WrapCrossAlignment.start,
    this.textDirection,
    this.verticalDirection = VerticalDirection.down,
    List<Widget> children = const <Widget>[],
  })
```

接下来介绍一个堆叠的部件 Stack，源码比较简单，就不贴了，直接上效果代码
```dart
class HomePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
          child: Stack(
        // 内部子部件的对齐方式
        alignment: Alignment.center,
        children: <Widget>[
          // 圆形头像，指定半径，指定背景图为头像即可
          CircleAvatar(backgroundImage: AssetImage('images/ali.jpg'), radius: 100.0),
          Text(
            'Kuky', style: TextStyle(color: Colors.white, fontSize: 34.0)),
        ],
      )),
    );
  }
}
```

如果我们需要第三个部件，底部距离圆形头像10px，那么只靠 alignment 是不可能实现了

所以，另外一个灰常流弊的部件就出来了 Positioned，其源码也比较简单，我还是不贴了吧~，还是直接上代码，直接修改

```dart
class HomePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
          child: Stack(
        alignment: Alignment.center,
        children: <Widget>[
          CircleAvatar(backgroundImage: AssetImage('images/ali.jpg'), radius: 100.0),
          Text(
            'Kuky',
            style: TextStyle(color: Colors.white, fontSize: 34.0),
          ),
          Positioned(child: Text('另外一段文字', style: TextStyle(color: Colors.white, fontSize: 20.0)), bottom: 10.0), // left, right, top, bottom 分别表示和 stack 的间距
        ],
      )),
    );
  }
}
```




