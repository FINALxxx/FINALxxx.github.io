# JavaScript

## 基础

### 1. 内置对象和方法和属性

- 常见的Browser对象
  - window：一个浏览器页面有一个window对象

- 常见的DOM对象
  - document：通过DOM树，获取html节点
  - console

- 内置方法：isFinite()等
- 内置属性：Infinity等

### 2. 基础操作

- 获取html元素：document.getElementById(“元素名”)
- 输入
  - prompt(“提示信息”,默认值)

- 输出
  - window.alert()：输出到一个弹窗
  - document.write()：输出到整个html页面
  - 元素.innerHtml：输出到元素
  - 元素.innerText：输出到元素的文本内容
  - console.log()：输出到控制台



### 3. 基础语法

- 变量赋值：var、let
  - 默认值为undefined
  - var和let的区别：var声明的变量会将作用域提升到顶部，而let和其他语言类似。

- 常量赋值：const

- 基本类型

  - 内置类型：string、number、boolean、symbol、undefined、null
  - 引用类型：object（对象和数组）
  - 内置类型的包装类：String、Number、Boolean、Symbol、Undefined、Null

  - 引用类型的包装类：Object（对象）、Array（数组）

- 对象

  - 类型为object

  - 格式为：

   ```js
    let obj = {
        k1:”str”, 		// 对象属性k1
        k2:num,   		// 对象属性k2
        k3:function(){ // 对象方法k3
            return "func"
        }
    };
   ```

  - 访问对象属性和方法：略

- 函数
  - js与html的交互：
    - html元素的事件属性：参考[HTML DOM 事件对象 | 菜鸟教程](https://www.runoob.com/jsref/dom-obj-event.html)
    - 调用函数：`<某元素 事件属性="myFunc()"></某元素>`
    - 函数返回：return无法直接返回给html，只能使用innerHTML

