# CSS

## 基础

- 加载方式：行内式、内嵌式（\<style type="text/css">）、链入式（<link href="" type="text/css" rel="stylesheet">）
- 导入：@import url(“”)



- 文本样式属性
  - 字体
    - font
    - font-size
    - font-family
    - font-weight
    - font-style
    - @font-face{}
  - 文本
    - text-transform：英文大小写控制
    - text-decoration：装饰，underline、overline、line-through
    - text-align：对齐，left、center、right、justify
    - text-indent：首行缩进
    - text-shadow
    - text-overflow
    - color
  - background（背景）
    - background-color：颜色或者rgb()
    - background-image：url(“地址”)
    - background-repeat
  - 其他（换行与间距）
    - word-wrap：自动换行
    - white-space：空白符处理
    - word-spacing：单词间距（英文）
    - letter-spacing：字间距
    - line-height：行间距
- 背景样式属性



## 选择器

- 简单选择器
  - 通配符选择器
  - 标签选择器
  - 类选择器(.)、id选择器(#)
- 复杂选择器
  - 交集选择器（标记选择器+类/id选择器）
  - 并集选择器（选择器1,选择器2,选择器3）
  - 后代选择器（外层标签+空格+内层标签）
- 伪类选择器（单冒号）
  - 状态伪类：选中元素的不同状态
  - 结构性伪类：根据DOM树中的结构关系来过滤元素
- 伪元素选择器（双冒号）
  - 选中元素的特定部分（比如第一行、第一个字等）



- 样式优先级：!important > 行内样式 > id选择器 > 类选择器 > 标签选择器 > 继承样式
  - 当优先级相同时，最后被应用的




## 布局

- 布局分类：固定宽度、流式、响应式
- 盒子模型：margin border padding content
- 元素分类
  - 块元素：div或display:block(可设置宽高，占据一整行)
  - 行内元素：span或display:inline（不可设置宽高，不占据一整行）
  - 行内块元素：display:inlin-block（可设置宽高，不占据一整行）
  - 弹性元素：display:flex（横向/纵向布局）
    - 占用宽度：flex:宽度比例
  - 网格元素：display:grid（网格布局）
    - grid-template-columns: 列长度1 列长度2 ...
    - grid-template-rows: 行长度1 行长度2 ...
    - grid-gap: 分割大小



- 浮动（float）与清除浮动（clear）
  - 这会使得文字流会围绕浮动元素
  - 如果文字流清除了浮动，则不能围绕浮动元素
- 定位（position）
  - static：默认
  - relative：相对于父元素，使用top、right、bottom、left定位
  - absolute：相对于**最近的已经定位的元素**，使用top、right、bottom、left定位
    - 如果发生堆叠，将会使用z-index来确定优先级
    - z-index越低就越在下方，越高就越在上方
  - fixed（固定定位）：相对于**浏览器窗口**，因此页面滚动时不会随页面移动
  - sticky：略
- 溢出（overflow）
  - 用于解决盒子布局的塌陷问题
  - visible（可见溢出）、hidden（隐藏溢出）、scroll（总是滚动）、auto



## 盒子布局属性

- margin: 上 右 下 左
- border: 厚度 样式 颜色
- padding: 上 右 下 左
- content
  - width
  - height