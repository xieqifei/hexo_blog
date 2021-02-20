---

title: Flutter学习记录
top: false
cover: false
toc: true
mathjax: false
date: 2020-02-15 17:32:32
password:
summary: Flutter是一个基于手机编程的样式库，和网站前端类似，我们可以利用flutter设计好看的手机软件样式，并将它运用到安卓端或者IOS端。这极大的避免了，重复开发。
tags: Flutter
categories: 学习笔记
---

# 1：Flutter介绍

- 市面上有很多混合开发框架，比如ReactNative、lonic，但都是针对前端开发。

- Flutter针对全部开发者的混合框架。

- 需要Dart语言基础。

# 2：Flutter目录结构、入口、自定义Widget、Center等组件

- 目录：ios，android，build，lib，text，pubspec.yaml（配置依赖），main.dart

- 入口文件、方法：再main.dart中

```dart
import 'package:flutter/material.dart';

void main(){
    runApp(new MyApp())//new 再Dart中可以省略
}
//void main()=>runApp(MyApp());
//自定义组件
class MyApp extends StatelessWidget{
    @override
    Widget build(BuildContext context){
        return MaterialApp(
        	home:Scaffold(
        		appBar:AppBar(
                	title:Text('Flutter Demo')
                ),
                body:HomeContent(),
        	),
            theme:ThemeData(
            	primarySwatch:Colors.blue
            ),
        )
    }
}

class HomeContent extends StatelessWidget{
    @override
    Widget build(BuildContext context){
        return Center(//内容居中的LinearLayout
    		child:Text(
            	'Hello Flutter'，
            	textDirection:TextDirection.ltr,//文字方向从左到右
                style:TextStyle(
                	fontSize:40.0,
                    color:Colors.yellow,
                    //color:Color.formRGBO(244,233,121,0.5),
                ),
        	)
    	);
    }
}
```

# 3：Container组件、Text组件

```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home:Scaffold(
        appBar: AppBar(
          title: Text("flutter demo"),
        ),
        body: HomeContent(),
      )
    );
  }
}

class HomeContent extends StatelessWidget{
  @override
  Widget build(BuildContext context) {
    // TODO: implement build
    return Center(
      child: Container(
        child: Text('各位同学大家好，我是珠江老师阿发是否你好吗',
        textAlign: TextAlign.left,
        style: TextStyle(
          fontSize: 16,
          color: Colors.red,
          fontWeight: FontWeight.w800,//加粗
          fontStyle: FontStyle.italic,//斜线
          decoration: TextDecoration.lineThrough,
          decorationColor: Colors.white,
          decorationStyle: TextDecorationStyle.dashed,
          letterSpacing: 5
        ),
        overflow: TextOverflow.ellipsis,
        maxLines: 2,
        textScaleFactor: 1.2,//size scale 2 times
        ),
        height: 300,
        width: 300,
        decoration: BoxDecoration(
          color: Colors.yellow,
          border: Border.all(
            color: Colors.blue,
            width: 2
          ),
          borderRadius: BorderRadius.all(
            Radius.circular(20)
          )
        ),
        padding: EdgeInsets.all(10),
        margin: EdgeInsets.all(10),
        //transform: Matrix4.translationValues(100, 0, 0),
        transform: Matrix4.rotationZ(0.3),
        alignment: Alignment.bottomLeft,
      ),
    );
  }
  
}
```

# 4：图片组件Image、本地图片、远程图片、图片裁剪

## 4.1 网络图片和样式

- 网络图片：

```dart
class HomeContent extends StatelessWidget{
  @override
  Widget build(BuildContext context) {
    // TODO: implement build
    return Center(
      child: Container(
        child: Image.network("https://i0.hdslb.com/bfs/archive/9377fda78c8a873085bd133e4fc54699ab56746d.jpg@336w_190h.webp",
          alignment: Alignment.center,
          //color: Colors.yellow,
          //colorBlendMode: BlendMode.screen,
          fit: BoxFit.cover,//cover：拉伸剪切，但不变形。最常用
          //repeat: ImageRepeat.repeatX,//X轴平铺
        ),
        width: 300,
        height: 300,
        decoration: BoxDecoration(
          color: Colors.lightBlue
        ),
      ),
    );
  }
```

- 容器装饰图片

```dart
class HomeContent extends StatelessWidget{
  @override
  Widget build(BuildContext context) {
    // TODO: implement build
    return Center(
      child: Container(
        width: 300,
        height: 300,
        decoration: BoxDecoration(
          color: Colors.lightBlue,
          borderRadius: BorderRadius.circular(150),
          image: DecorationImage(
            image: NetworkImage("https://i0.hdslb.com/bfs/archive/b13bfd82fdea672001554eca19c6bf7135e2f692.jpg@336w_190h.webp"),
            fit: BoxFit.cover
          )
        ),
      ),
    );
  }
```

- ClipOval

```dart
class HomeContent extends StatelessWidget{
  @override
  Widget build(BuildContext context) {
    // TODO: implement build
    return Center(
      child: Container(
        child: ClipOval(
          child: Image.network("https://i0.hdslb.com/bfs/archive/b13bfd82fdea672001554eca19c6bf7135e2f692.jpg@336w_190h.webp",
            height: 100,
            width: 100,
            fit: BoxFit.cover,
          ),
        ),
      ),
    );
  }
  
}
```

## 4.2 引入本地图片

1. 在项目根目录下新建文件夹images

- build
- images
  - 2.0x
  - 3.0x
  - 4.0x

2. 在文件夹里放入图片，会根据屏幕分辨率配置不同图片
3. 在pubspec.yaml中配置

```yaml
assets:
- images/2.0x/a.jpeg
- images/3.0x/a.jpeg
```

4. 在代码中使用

```dart
class HomeContent extends StatelessWidget{
  @override
  Widget build(BuildContext context) {
    // TODO: implement build
    return Center(
      child: Container(
        child: Image.asset('images/a.jpeg'),
      ),
    );
  }
```

# 5：ListView组件

## 5.1 基本列表

- 垂直列表 ListTile组件

```dart

class HomeContent extends StatelessWidget{
  @override
  Widget build(BuildContext context) {
    // TODO: implement build
    return ListView(
      padding: EdgeInsets.all(10),
      children: <Widget>[
        ListTile(
          leading: Icon(Icons.settings,color: Colors.red,),//前置图标
          title: Text("1你好列表组件",style: TextStyle(
            fontSize: 24
          ),),
          subtitle: Text("1你好我是二级标题"),
        ),
        ListTile(
          title: Text("1你好列表组件"),
          subtitle: Text("1你好我是二级标题"),
          trailing: Icon(Icons.home,size: 30,),//后置图标
        ),
        ListTile(
          leading: Image.network("https://i0.hdslb.com/bfs/sycp/creative_img/202002/849753c8234f501092c0b8ae6af5b679.jpg"),
          title: Text("你好列表组件"),
          subtitle: Text("你好我是二级标题"),
        ),
      ],
    );
  }
  
}
```

- 垂直图文列表

```dart
class HomeContent extends StatelessWidget{
  @override
  Widget build(BuildContext context) {
    // TODO: implement build
    return ListView(
      padding: EdgeInsets.all(10),
      children: <Widget>[
        Image.network("https://i0.hdslb.com/bfs/sycp/creative_img/202002/849753c8234f501092c0b8ae6af5b679.jpg"),
        Container(
          child: Text("i am title",textAlign: TextAlign.center,style: TextStyle(fontSize: 18),),
          height: 40,
          padding: EdgeInsets.fromLTRB(0, 10, 0, 10),
        ),
        Image.network("https://i0.hdslb.com/bfs/sycp/creative_img/202002/849753c8234f501092c0b8ae6af5b679.jpg"),
        Container(
          child: Text("i am title",textAlign: TextAlign.center,style: TextStyle(fontSize: 18),),
          height: 40,
          padding: EdgeInsets.fromLTRB(0, 10, 0, 10),
        ),
      ],
    );
  }
}
```

- 水平列表

```dart
class HomeContent extends StatelessWidget{
  @override
  Widget build(BuildContext context) {
    // TODO: implement build
    return Container(
      height: 180,
      child: ListView(
        padding: EdgeInsets.all(10),
        scrollDirection: Axis.horizontal,
        children: <Widget>[
          Container(
            width: 180,
            height: 180,
            color: Colors.red,
          ),
          Container(
            width: 180,
            height: 180,
            color: Colors.orange,
            child: ListView(
              children: <Widget>[
                Image.network("https://i0.hdslb.com/bfs/sycp/creative_img/202002/849753c8234f501092c0b8ae6af5b679.jpg"),
                Text("i am text")
              ],
            ),
          ),
          Container(
            width: 180,
            height: 180,
            color: Colors.blue,
          ),
        ],
      ),
    );

  }
  
}
```

**scrollDirection**

## 5.2 ListView列表组件、动态列表

- 展示一个列表中的数据

```dart
class HomeContent extends StatelessWidget{
  //自定义方法
  List<Widget> _getData(){
    List<Widget> list=new List();
    for(var i=0;i<20;i++){
      list.add(ListTile(
        title: Text("iam $i list"),
      ));
    }
    return list;
  }
  @override
  Widget build(BuildContext context) {
    // TODO: implement build
    return ListView(
      children: _getData()
    );
  }
  
}
```

- ListView.builder

```dart
class HomeContent extends StatelessWidget{

  List list=new List();
  HomeContent(){
    for(var i=0;i<20;i++) {
      list.add(ListTile(
        title: Text("iam $i list"),
      ));
    }
  }
  @override
  Widget build(BuildContext context) {
    // TODO: implement build
    return ListView.builder(
        itemCount: this.list.length,
        itemBuilder: (context,index){
          return ListTile(
            title: Text(this.list[index]),
          );
        }
    );
  }
}
```

