# UI设计

## PS基础

### 1. 概念

- 位图与矢量图
- 文件格式：PSD、JPG、PNG、GIF、PDF
- 色彩模式：RGB、CMYK、HSB、Lab、灰度

### 2. 图层

- 图层种类
  - 智能对象（可以高保真，可以进行蒙版修改）：背景图层、图像图层、形状图层、文字图层
    - 蒙版口诀：黑色遮挡、白色显示
    - 蒙版种类：透明蒙版、剪切蒙版
  - 栅格化对象（无法高保真，可以进行任意修改）
- 图层调整：调节亮度、对比度、fx、变换、修改等
- 图层混合
  - 图层混合选项（对一个图层）：设置浮雕、阴影等
  - 图层混合模式（对两个图层）：变暗模式、变亮模式、叠加模式、差值模式



## PS工具

- 移动
  - 移动工具
- 选区
  - 选区工具（规则的选区）
  - 套索工具（任意的闭合选区）
  - 魔棒工具（根据容差选区）
  - 快速选择工具
- 裁剪
  - 裁剪工具
  - 透视裁剪工具（只能处理栅格化对象）



- 画笔
  - 吸管工具
  - 画笔工具
  - 颜色替换工具
- 钢笔（可以建立**路径**，而路径还可以辅助建立**形状、描边、选区**）
- 形状

- 渐变
  - 渐变工具（根据选中的方向、起点和终点生成渐变颜色）
    - 为了让图形（假设是智能对象）拥有渐变颜色，那么会搭配**蒙版**
  - 油漆桶工具
- 滤镜
  - 模糊工具-锐化工具
  - 液化工具-涂抹工具（带模糊的液化）



- 修补
  - 污点修复画笔工具（根据周围的相近色来修补）
  - 修补画笔工具（根据自己选中的修补源来修补）
- 克隆（可以起到修补的作用）
  - 仿制图章（根据自己选中的克隆源来克隆）

## Android UI设计

### 1. 布局

- 通用属性
  - layout_width、layout_height：fill_parent/wrap_content
  - layout_gravity：vertical/horizontal
  - layout_marginXXX、layout_paddingXXX：像素值
- LinearLayout
  - (布局属性)orientation
  - (布局属性)gravity
    - 位置对齐：top、bottom、left、right、center_vertical、center_horizontal、center
    - 大小填充：fill_vertical、fill_horizontal、fill
- TableLayout
  - (布局属性)继承LinearLayout的所有属性
  - (布局属性)shrinkColumns、stretchColumns（注意对应的Java方法名有特殊变化）：收缩拉伸列
  - (布局属性)collapsedColumns：隐藏列
- RelativeLayout
  - (控件属性)按**父控件**位置对齐
    - layout_alignParentXXX、layout_alignWithParentIfMissing
    - layout_centerInParent、layout_centerHorizontal、layout_centerVertical
  - (控件属性)按**兄弟控件**位置对齐
    - layout_toRightOf、layout_toLeftOf
    - layout_above、layout_below
  - (控件属性)按**任意控件**边缘位置对齐
    - layout_alignXXX
- AbsoluteLayout
  - (控件属性)layout_x、layout_y


### 2. 控件

#### 基础控件

- TextView
  - autoLink、gravity、hint、text(XML属性)

- EditView
  - 继承TextView
  - cursorVisible、password、phoneNumber(XML属性)




- Button
- ToggleButton
  - 继承Button
  - checked、textOff、textOn(XML属性)




- ImageView（显示任何Drawable）
  - scaleType、src(XML属性)

- ImageButton
  - 继承Button和ImageView



- RadioGroup
  - checkedButton：初始时按下的RadioButton(XML属性)

- RadioButton、CheckBox
  - 注意：RadioButton要和RadioGroup一起使用
  - 继承Button
  - checked(XML属性)
  - isChecked()、setChecked()(JAVA方法)




- ProgressBar
  - 风格：@android:style/Widget.ProgressBar.xxx
  - max、progress、style(XML属性)
  - setProgress()(JAVA方法)

- SeekBar
- RatingBar

#### 选择控件

- DatePicker
- TimePieker

#### 容器控件

- ScrollView：不使用Adapter构成列表
- ListView：使用Adapter构成列表
  - setAdapter()(JAVA方法)

- GridView：使用Adapter构成矩阵
- 注：Adapter的创建方法
  - 列表式：new ArrayAdapter(MainActivity.this,android.R.layout.simple_list_item_1,字符串列表);
  - 矩阵式：略


### 3. 组件

- 组件不需要在布局文件中设置，只需要实例化对象，然后在需要弹窗的时候调用即可。
- Menu（菜单）
  - 菜单类型：SubMenu、ContextMenu
  - 菜单项：MenuItem
- AlertDialog（对话框）



## Android监听

### 1. 内置监听

- onKeyDown(int keycode,KeyEvent e)
- onTouchEvent()
- onFocusChanged()

### 2. 注册监听器

- OnClickListener、OnLongClickListener
  - 需要继承接口，并实现onClick(View v)方法
- OnFocusChangeListener
  - 需要继承接口，并实现onFocusChange(View v,boolean hasFocus)方法
- OnKeyListener
  - 需要继承接口，并实现onTouch(View v,int keycode,KeyEvent e)方法
- OnTouchListener
  - 需要继承接口，并实现onTouch(View v,MotionEvent e)方法

## Android 项目结构

### 1. 资源结构

- res
  - drawable：图片文件或XML文件，XML通过@drawable访问，JAVA通过R.drawable访问
    - mipmap：图标文件，受系统分辨率影响而改变
  - layout：存放布局，JAVA通过R.layout访问
  - values：简单值/数组/整数/字符串/样式资源
  - raw：音频和视频文件，需要调用Resource.openRawResource()来打开raw文件
- assets：放置字体字符
- AndroidManifest.xml：存放配置文件

### 2. XML中引用资源

- id：@+id/<id值>
- 资源：@<res下的资源文件夹>/<资源的name属性>

### 3. Java中引用资源

- id：R.id.<id名>
- 资源：R.<res下的资源文件夹>.<资源的name属性>



## 概念

- UI设计的概念
- UI、UE、UX的区别和协同作用
- UI设计的三大核心
- UI设计的原则
- UI设计流程



- 交互设计的概念
- 交互设计的特点
- UI设计和交互设计的区别

- 交互平台与硬件的关系
- 交互规范
- 交互设计的原则



- 快捷键