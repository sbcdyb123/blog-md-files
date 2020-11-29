# 第一个*flutter*

```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'flutter',
      home: Scaffold(
        appBar: AppBar(
          title: Text('bar title'),
        ),
        body: Center(
          child: Text('welcome flutter'),
        ),
      ),
    );
  }
}
```

# 基础组件

## *Text Widget*组件

```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'flutter',
      home: Scaffold(
        appBar: AppBar(
          title: Text('bar title'),
        ),
        body: Center(
          child: Text(
            '测试!!  ' * 12,
            textAlign: TextAlign.left,
            maxLines: 1,
            overflow: TextOverflow.ellipsis, //省略三点
            style: TextStyle(
                fontSize: 25,
                color: Color.fromARGB(255, 255, 125, 125),
                decoration: TextDecoration.underline,
                decorationStyle: TextDecorationStyle.solid),
          ),
        ),
      ),
    );
  }
}

```



## *Container widget*

```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'flutter',
      home: Scaffold(
        appBar: AppBar(
          title: Text('bar title'),
        ),
        body: Center(
            child: Container(
                child: Text(
                  '你好世界!!',
                  style: TextStyle(fontSize: 40),
                ),
                alignment: Alignment.topLeft,
                width: 500,
                height: 400,
                decoration: BoxDecoration(
                    border: Border.all(width: 2, color: Colors.red),
                    gradient: LinearGradient(
                        colors: [Colors.lightBlue, Colors.lightGreen])),
                padding: EdgeInsets.all(10),
                margin: EdgeInsets.all(10))),
      ),
    );
  }
}
```

## *image widget*

```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'flutter',
      home: Scaffold(
        appBar: AppBar(
          title: Text('bar title'),
        ),
        body: Center(
            child: Container(
          width: 300,
          height: 300,
          color: Colors.lightGreen,
          child: Image.network(
            'https://ss1.bdstatic.com/70cFuXSh_Q1YnxGkpoWK1HF6hhy/it/u=3033478454,2405957767&fm=26&gp=0.jpg',
            fit: BoxFit.fitWidth, //图片填充模式
            color: Colors.green, //混合模式的颜色
            colorBlendMode: BlendMode.softLight, //混合模式
            repeat: ImageRepeat.noRepeat, //图片重复
          ),
        )),
      ),
    );
  }
}

```

## *listview widget*

### 横向

```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'flutter',
      home: Scaffold(
        appBar: AppBar(
          title: Text('bar title'),
        ),
        body: ListView(
          children: <Widget>[
            ListTile(
              leading: Icon(Icons.save),
              title: Text('保存'),
            ),
            ListTile(
              leading: Icon(Icons.save),
              title: Text('保存'),
            ),
            ListTile(
              leading: Icon(Icons.save),
              title: Text('保存'),
            )
          ],
        ),
      ),
    );
  }
}

```

### 纵向

```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'flutter',
      home: Scaffold(
          appBar: AppBar(
            title: Text('bar title'),
          ),
          body: Center(
            child: Container(height: 200, child: MyListView()),
          )),
    );
  }
}

class MyListView extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return ListView(
      scrollDirection: Axis.horizontal,
      children: <Widget>[
        Container(
          width: 180,
          color: Colors.pink,
        ),
        Container(
          width: 180,
          color: Colors.yellow,
        ),
        Container(
          width: 180,
          color: Colors.red,
        ),
        Container(
          width: 180,
          color: Colors.green,
        )
      ],
    );
  }
}

```

### 动态

```dart
import 'package:flutter/material.dart';

void main() =>
    runApp(MyApp(items: List<String>.generate(1000, (index) => 'item $index')));

class MyApp extends StatelessWidget {
  final List<String> items;
  MyApp({Key key, @required this.items}) : super(key: key);
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'flutter',
      home: Scaffold(
          appBar: AppBar(
            title: Text('bar title'),
          ),
          body: ListView.builder(
            itemCount: items.length,
            itemBuilder: (context, index) {
              return ListTile(
                title: Text('${items[index]}'),
              );
            },
          )),
    );
  }
}

```

## *Gridview widget (网格列表)*

```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'flutter',
      home: Scaffold(
          appBar: AppBar(
            title: Text('bar title'),
          ),
          body: GridView.count(
            padding: EdgeInsets.all(20),
            crossAxisCount: 2,
            crossAxisSpacing: 2,
            mainAxisSpacing: 2,
            childAspectRatio: 0.7,
            children: <Widget>[
              new Image.network(
                  'http://img5.mtime.cn/mt/2018/10/22/104316.77318635_180X260X4.jpg',
                  fit: BoxFit.cover),
              new Image.network(
                  'http://img5.mtime.cn/mt/2018/10/10/112514.30587089_180X260X4.jpg',
                  fit: BoxFit.cover),
              new Image.network(
                  'http://img5.mtime.cn/mt/2018/11/13/093605.61422332_180X260X4.jpg',
                  fit: BoxFit.cover),
              new Image.network(
                  'http://img5.mtime.cn/mt/2018/11/07/092515.55805319_180X260X4.jpg',
                  fit: BoxFit.cover),
              new Image.network(
                  'http://img5.mtime.cn/mt/2018/11/21/090246.16772408_135X190X4.jpg',
                  fit: BoxFit.cover),
              new Image.network(
                  'http://img5.mtime.cn/mt/2018/11/17/162028.94879602_135X190X4.jpg',
                  fit: BoxFit.cover),
              new Image.network(
                  'http://img5.mtime.cn/mt/2018/11/19/165350.52237320_135X190X4.jpg',
                  fit: BoxFit.cover),
              new Image.network(
                  'http://img5.mtime.cn/mt/2018/11/16/115256.24365160_180X260X4.jpg',
                  fit: BoxFit.cover),
              new Image.network(
                  'http://img5.mtime.cn/mt/2018/11/20/141608.71613590_135X190X4.jpg',
                  fit: BoxFit.cover),
            ],
          )),
    );
  }
}

```

# 布局

## 水平

```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'ListView widget',
      home: Scaffold(
          appBar: new AppBar(
            title: new Text('水平方向布局'),
          ),
          body: new Row(
            children: <Widget>[
              new RaisedButton(
                  onPressed: () {},
                  color: Colors.redAccent,
                  child: new Text('红色按钮')),
              new RaisedButton(
                onPressed: () {},
                color: Colors.orangeAccent,
                child: new Text('黄色按钮'),
              ),
              new RaisedButton(
                  onPressed: () {},
                  color: Colors.pinkAccent,
                  child: new Text('粉色按钮'))
            ],
          )),
    );
  }
}

```

```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'ListView widget',
      home: Scaffold(
          appBar: new AppBar(
            title: new Text('水平方向布局'),
          ),
          body: new Row(
            children: <Widget>[
              Expanded(
                  child: new RaisedButton(
                      onPressed: () {},
                      color: Colors.redAccent,
                      child: new Text('红色按钮'))),
              Expanded(
                  child: new RaisedButton(
                      onPressed: () {},
                      color: Colors.blueAccent,
                      child: new Text('蓝色按钮'))),
              Expanded(
                  child: new RaisedButton(
                      onPressed: () {},
                      color: Colors.greenAccent,
                      child: new Text('绿色按钮'))),
            ],
          )),
    );
  }
}

```

## 垂直

```dart
import 'package:flutter/material.dart';
void main () => runApp(MyApp());

class MyApp extends StatelessWidget{
  @override
  Widget build(BuildContext context ){
      return MaterialApp(
        title:'ListView widget',

        home:Scaffold(
          appBar:new AppBar(
            title:new Text('垂直方向布局'),
          ),
          body:Column(
            mainAxisAlignment: MainAxisAlignment.center,
            crossAxisAlignment: CrossAxisAlignment.start,
            children: <Widget>[
             Text('I am JSPang'),
             Text('my website is jspang.com'),
             Text('I love coding')
            ],
          )
        ),
      );
  }
}
```

```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'ListView widget',
      home: Scaffold(
          appBar: new AppBar(
            title: new Text('垂直方向布局'),
          ),
          body: Center(
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.center,
              mainAxisAlignment: MainAxisAlignment.center,
              children: <Widget>[
                Text('123111111111111111111'),
                Text('123'),
                Text('123123123'),
                Expanded(
                  child: Text('123'),
                ),
                Text('123'),
                Text('121232133'),
                Text('123'),
                Text('1241243'),
                Text('123'),
                Text('121233'),
              ],
            ),
          )),
    );
  }
}
```

## stack 层叠

```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    var stack = new Stack(
      alignment: const FractionalOffset(0.5, 0.8),
      children: <Widget>[
        new CircleAvatar(
          backgroundImage: new NetworkImage(
              'http://blogimages.jspang.com/blogtouxiang1.jpg'),
          radius: 100.0,
        ),
        new Container(
          decoration: new BoxDecoration(
            color: Colors.lightBlue,
          ),
          padding: EdgeInsets.all(5.0),
          child: new Text('JSPang 技术胖'),
        )
      ],
    );

    return MaterialApp(
      title: 'ListView widget',
      home: Scaffold(
        appBar: new AppBar(
          title: new Text('垂直方向布局'),
        ),
        body: Center(child: stack),
      ),
    );
  }
}

```

## position 定位组件

```dart
import 'package:flutter/material.dart';
void main () => runApp(MyApp());

class MyApp extends StatelessWidget{
  @override
  Widget build(BuildContext context ){
      var stack = new Stack(

        children: <Widget>[
          new CircleAvatar(
            backgroundImage: new NetworkImage('http://jspang.com/static//myimg/blogtouxiang.jpg'),
            radius: 100.0,
          ),
          new Positioned(
            top:10.0,
            left:10.0,
            child: new Text('JSPang.com'),
          ),
          new Positioned(
            bottom:10.0,
            right:10.0,
            child: new Text('技术胖'),
          )
        ],
      );


      return MaterialApp(
        title:'ListView widget',
        home:Scaffold(
          appBar:new AppBar(
            title:new Text('层叠布局'),
          ),
          body:Center(child:stack),
        ),
      );
  }
}
```

## card布局

```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    var card = Card(
      child: Column(
        children: <Widget>[
          ListTile(
            title: Text(
              '浙江省台州市玉环市',
              style: TextStyle(fontWeight: FontWeight.bold),
            ),
            subtitle: Text('fanglong:18892624594'),
            leading: Icon(Icons.account_circle),
          ),
          Divider(),
          ListTile(
            title: Text(
              '浙江省宁波市海曙区',
              style: TextStyle(fontWeight: FontWeight.bold),
            ),
            subtitle: Text('fanglong:18892624594'),
            leading: Icon(Icons.account_circle),
          ),
          Divider(),
          ListTile(
            title: Text(
              '浙江省杭州市余杭区',
              style: TextStyle(fontWeight: FontWeight.bold),
            ),
            subtitle: Text('fanglong:18892624594'),
            leading: Icon(Icons.account_circle),
          )
        ],
      ),
    );

    return MaterialApp(
      title: 'ListView widget',
      home: Scaffold(
        appBar: new AppBar(
          title: new Text('垂直方向布局'),
        ),
        body: Center(child: card),
      ),
    );
  }
}

```

# 导航

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(home: HomePage());
  }
}

class HomePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('导航页面'),
      ),
      body: Center(
        child: RaisedButton(
          onPressed: () {
            Navigator.push(
                context, MaterialPageRoute(builder: (context) => AboutPage()));
          },
          child: Text('查看商品详情页'),
        ),
      ),
    );
  }
}

class AboutPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('商品详情页'),
      ),
      body: Center(
        child: RaisedButton(
          onPressed: () {
            Navigator.pop(context);
          },
          child: Text('返回'),
        ),
      ),
    );
  }
}

```

## 导航传参

```dart
import 'package:flutter/material.dart';

class Product {
  final String name; //商品标题
  final String description; //商品描述
  Product(this.name, this.description);
}

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
        home: ProductList(
            products: List.generate(
                20, (index) => Product('商品名称:$index', '商品描述:$index'))));
  }
}

class ProductList extends StatelessWidget {
  final List<Product> products;

  const ProductList({Key key, @required this.products}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('商品列表'),
      ),
      body: ListView.builder(
        itemCount: products.length,
        itemBuilder: (BuildContext context, int index) {
          return ListTile(
            title: Text(products[index].name),
            onTap: () {
              Navigator.push(context, MaterialPageRoute(builder: (context) {
                return ProductDetail(product: products[index]);
              }));
            },
          );
        },
      ),
    );
  }
}

class ProductDetail extends StatelessWidget {
  final Product product;
  ProductDetail({Key key, @required this.product}) : super(key: key);
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('${product.name}'),
      ),
      body: Center(
        child: Text('${product.description}'),
      ),
    );
  }
}

```

## 返回参

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(home: HomePage());
  }
}

class HomePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('首页'),
      ),
      body: Center(
        child: RaisedButton(
          onPressed: () {
            _navigate2meimei(context);
          },
          child: Text('去搭讪'),
        ),
      ),
    );
  }

  _navigate2meimei(BuildContext context) async {
    final result = await Navigator.push(
        context, MaterialPageRoute(builder: (context) => XiaoJieJie()));
     print("路由返回值: $result");
  }
}

class XiaoJieJie extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('我是小姐姐')),
      body: Center(
          child: Column(
        children: <Widget>[
          RaisedButton(
            child: Text('大长腿小姐姐'),
            onPressed: () {
              Navigator.pop(context, '大长腿:1511008888');
            },
          ),
          RaisedButton(
            child: Text('小蛮腰小姐姐'),
            onPressed: () {
              Navigator.pop(context, '大长腿:1511009999');
            },
          ),
        ],
      )),
    );
  }
}
```

## 静态资源和项目图片

如果想配置项目资源文件，就需要使用`pubspec.yaml`文件，需要把资源文件在这里声明。

比如在项目根目录下新建了一个`images`文件夹，文件夹下面放了一个图片，图片的名称叫做`blogtouxiang.jpg`，那我们在`pubspec.yaml`文件里就要写如下代码进行声明。

```dart
assets:
  - images/blogtouxiang.jpg
```

有了声明后，我们就可以直接在项目中引用这个文件了。这里使用最简单的代码结构，只用了一张图片。代码如下:

```dart
import 'package:flutter/material.dart';

void main()=>runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Container(
      child: Image.asset('images/blogtouxiang.jpg'),
    );
  }
}
```

这时候已经在项目中引入成功了，可以预览看一下效果。当然样子很丑，这里主要时学知识，就不做过多的修饰了。