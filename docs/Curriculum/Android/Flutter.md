## Flutter

### 基础Widget

- widget分类：无状态、有状态
- widget内容：实现一个build()，用来构建自身
  - 一个widget通常由一些较低级别widget组成。
  - Flutter框架将依次构建这些widget，直到构建到最底层的子widget时，这些最低层的widget通常为RenderObject，它会计算并描述widget的几何形状。
- Widget类中的字段往往都会定义为"final"



- 常用的基础Widget：Text、Row/Column/Stack、Container
  - Row/Column：一种**布局Widget**，在Row或者Column方向上排列子Widget
  - Stack/Positioned：一种**布局Widget**，使用position定位（相对于stack的上下左右边的距离），允许子Widget堆叠
  - Flex/Expanded：一种**布局Widget**，用flex来确定子Widget的比例
  - Container：一种**组件Widget**，拥有装饰属性、margin、padding等属性，用于组合出一个更大的视觉元素



- 进阶的基础Widget：ListView、GridView、Sliver



- context查找控件
  - 祖先控件：实现of方法，参数为context
  - 子控件：使用祖先控件.of(context)，通过context找到weiget树上，向上查找最近的祖先实例

### 交互

#### 1. 交互与状态机基础

- 使用有状态widget来实现交互
  - MyWeiget类：
    - 自身管理状态：继承StatefulWeiget类，实现createState()方法
    - 实现交互：实现build()方法，返回可交互控件/GestureDetector包裹的无交互控件，并实现交互回调函数
  - _MyWeightState类（私有类）：继承State类，利用stateState()进行状态机的管理

#### 2. 状态机管理

- 自身管理小部件的状态
  - 自己实现管理状态机的私有类，其类成员就是状态机的所有状态
- 父类管理子类的状态
  - 子类将状态放到共有类中，并且放到构造函数中
  - 父类在weiget树中放置子类时，通过构造函数进行管理（需要更新的时候，会重复执行这个构造函数，使得子类被更新）
- 混合管理：略

#### 3. 各种交互方式

- 拖动界面元素：[`LongPressDraggable`](https://api.flutter-io.cn/flutter/widgets/LongPressDraggable-class.html?_gl=1*1sh2czn*_ga*MjAzMTg4OTAzNy4xNzQ3NjUxNDQ5*_ga_HPSFTRXK91*czE3NDc4Nzg2NjkkbzMkZzEkdDE3NDc4Nzg4ODkkajU0JGwwJGgw)
- 点按涟漪：[`InkWell`](https://api.flutter-io.cn/flutter/material/InkWell-class.html?_gl=1*1i0mgyj*_ga*MjAzMTg4OTAzNy4xNzQ3NjUxNDQ5*_ga_HPSFTRXK91*czE3NDc4Nzg2NjkkbzMkZzEkdDE3NDc4Nzg5NDEkajIkbDAkaDA.)
- 跨应用拖放对象：[`Draggable`](https://api.flutter-io.cn/flutter/widgets/Draggable-class.html?_gl=1*14jskgi*_ga*MjAzMTg4OTAzNy4xNzQ3NjUxNDQ5*_ga_HPSFTRXK91*czE3NDc4Nzg2NjkkbzMkZzEkdDE3NDc4NzkwNzUkajQ3JGwwJGgw)
- 可交互提醒：[`SnackBar`](https://api.flutter-io.cn/flutter/material/SnackBar-class.html?_gl=1*1xbvkqn*_ga*MjAzMTg4OTAzNy4xNzQ3NjUxNDQ5*_ga_HPSFTRXK91*czE3NDc4Nzg2NjkkbzMkZzEkdDE3NDc4ODA0ODgkajQzJGwwJGgw)





### 资源

- 资源位置：在yaml内配置
- 加载资源
  - 加载图像：`const AssetImage('icons/heart.png', package: 'my_icons');`
  - 淡入图像：FadeInImage
  - 加载网络资源：`const AssetNetWork();`
- 平台相关的资源（以Android为例）
  - 应用图标设置
  - 启动画面设置



### 路由与导航

#### 1. 路由方式[`Router`](https://api.flutter.dev/flutter/widgets/Router-class.html)

- 路由适用于：有复杂的深层链接需求
- 用`MaterialApp` 或 `CupertinoApp` 上的 `router` 构造函数，并提供routerConfig配置
- 一个router就是一个页面，实际上它是一个Weiget

#### 2. 导航方式[`Navigator`](https://api.flutter.dev/flutter/widgets/Navigator-class.html)

- 导航适用于：没有复杂的深层链接需求
- 使用堆栈来管理
- 调用push()、pop()，并在其中调用MaterialPageRoute()/CupertinoPageRoute()，来管理切换的页面
  - push时传递数据（方式1）：在push()内的MaterialPageRoute()中，在控件的构造函数中需要传递的数据
  - push时传递数据（方式2）：RouteSettings方式略
  - pop时返回数据：在pop()中放置result，当push()结束时会将result作为返回值


#### 3. 选项卡方式`TabController`

- 用TabBar和TabView（两者必须一一对应）

#### 4. 抽屉方式[`Drawer`](https://api.flutter-io.cn/flutter/material/Drawer-class.html?_gl=1*s79jkr*_ga*MjAzMTg4OTAzNy4xNzQ3NjUxNDQ5*_ga_HPSFTRXK91*czE3NDc4Nzg2NjkkbzMkZzEkdDE3NDc4ODM3MTAkajYwJGwwJGgwJGRyZ3p6WWVwZkRXX1dNZFRlNDlMa085YUZINm01aWdGX1Jn)

- 内部使用ListView：使用 [`DrawerHeader`](https://api.flutter-io.cn/flutter/material/DrawerHeader-class.html) 和两个 [`ListTile`](https://api.flutter-io.cn/flutter/material/ListTile-class.html) widget 填充 `ListView`。



### 输入与表单

- 文本框：[`TextField`](https://api.flutter-io.cn/flutter/material/TextField-class.html?_gl=1*w3s334*_ga*MjAzMTg4OTAzNy4xNzQ3NjUxNDQ5*_ga_HPSFTRXK91*czE3NDc4Nzg2NjkkbzMkZzEkdDE3NDc4NzkxMjAkajIkbDAkaDA.)
- 表单文本框：[`TextFormField`](https://api.flutter-io.cn/flutter/material/TextFormField-class.html?_gl=1*w3s334*_ga*MjAzMTg4OTAzNy4xNzQ3NjUxNDQ5*_ga_HPSFTRXK91*czE3NDc4Nzg2NjkkbzMkZzEkdDE3NDc4NzkxMjAkajIkbDAkaDA.)
- 文本框控制：[`TextEditingController`](https://api.flutter-io.cn/flutter/widgets/TextEditingController-class.html?_gl=1*h9vtbh*_ga*MjAzMTg4OTAzNy4xNzQ3NjUxNDQ5*_ga_HPSFTRXK91*czE3NDc4Nzg2NjkkbzMkZzEkdDE3NDc4Nzk1MDkkajYwJGwwJGgw)
  - 使用方法：传入文本框的构造函数来绑定控件、结束时在dispose()中释放
  - 监听器方法：addListener(hook_func())，在文本框发生变化的时候调用hook_func()
- 控件聚焦：[`FocusNode`](https://api.flutter-io.cn/flutter/widgets/FocusNode-class.html?_gl=1*1xswoyb*_ga*MjAzMTg4OTAzNy4xNzQ3NjUxNDQ5*_ga_HPSFTRXK91*czE3NDc4Nzg2NjkkbzMkZzEkdDE3NDc4ODAwMDEkajM2JGwwJGgw)
  - 使用方法：传入文本框的构造函数来绑定控件、结束时在dispose()中释放
  - 聚焦方法：[`requestFocus()`](https://api.flutter-io.cn/flutter/widgets/FocusNode/requestFocus.html?_gl=1*1xq3ze1*_ga*MjAzMTg4OTAzNy4xNzQ3NjUxNDQ5*_ga_HPSFTRXK91*czE3NDc4Nzg2NjkkbzMkZzEkdDE3NDc4ODAwMDEkajM2JGwwJGgw)
- 表单校验：略



## Flutter进阶

### 组件

- 布局类组件
- 容器类组件
- 可滚动组件
- 功能性组件

### 事件

- 指针事件（触摸、鼠标和触控笔等）
- 手势事件（一个或多个指针事件的组合）
- 事件通知机制（Notification）

### 动画



