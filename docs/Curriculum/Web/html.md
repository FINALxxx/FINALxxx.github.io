# HTML

## 基础

- head
  - ==meta==（http-equiv、name、content、charset）
  - title、link、script等标签
  
- 文本标签
  - h1、p、格式化标签
  - br、hr（两者为闭合标签）
  - a（herf）
    - 超链接、锚点链接
  
- 图像标签
  - img（src、alt）

- 表格
  - table
    - 表头：thead
      - 标题：caption
    - 主体
      - 行：tr
      - 单元格：td、th
    - 表尾：tfoot
  
- 列表

  - 无序/有序列表：ul/ol（type）
    - 列表项：li

  - 定义列表：dl
    - 列表项定义：dt
    - 列表项描述：dd

- 表单

  - <img src="C:\Users\FINALx\AppData\Roaming\Typora\typora-user-images\image-20250622172335295.png" alt="image-20250622172335295" style="zoom:80%;" />

  - form（action、method、name）
    - method属性：为“get”时，提交信息会出现在URL中；为“post”时，会分开发送
    - name属性：定义表单名
    - 表单元素label（for）
      - for属性：规定与什么表单绑定
    
    
    
    - 表单元素：input（name、type、value、size、src、checked、disabled、maxlength）
      - name属性：定义控件名
      - ==type属性==：text、password、radio、checkbox、hidden、file、button、submit、image、reset
      - value属性：输入的默认值
      - src属性：图像地址
      - button和submit的区别：button结合js使用，而submit直接提交表单到action处
    
    
    
    - 表单元素：下拉框select（name、size、value）
      - multiple属性：实现多选
      - **选项option**（selected）
      - 选项分组optgroup（label）
    - 表单元素：文本域textarea（name、value、cols、rows）




## HTML5新标签

- 结构化标签
  - 是语义化的html网页框架，本质上和div没有区别
  - ![image-20250622165955299](C:\Users\FINALx\AppData\Roaming\Typora\typora-user-images\image-20250622165955299.png)
- 分组标签
  - figure：带标题的独立框，可以放图片、代码之类的
    - figcaption：框的标题
  - hgroup：将多个\<hn>标签组合到一块
- 行内语义性标签
  - time（datetime、pubtime）：搜索引擎需要获取的网站信息
  - mark：文本高亮
- 页面交互标签
  - details：一个隐藏下拉框
    - summary：下拉框的标题
  - progress（value、max）：一个进度条，需要js控制value属性
  - meter（value、min、max、low、high、optimum、title）：定义分数测量，value在[min,low]和[low,high]和[high,max]有不同颜色
- ==全局属性==
  - contenteditable：标签是否可编辑
    - 当未指定属性时，它继承父元素的属性
    - 使用前提：该元素必须可以获得焦点，且内容不是只读的
    - 编辑完成后，只能由js通过innerHtml来获取内容
  - hidden：元素是否隐藏
  - spellcheck：拼写检查，主要针对于文本框、输入框等
  - dragable：元素是否可拖动
    - 链接和图片默认可拖动
    - 如果想实现拖动，需要使用js
- ==表单input类型==
  - email、url
  - number、range
  - 日期类型（Date、Month、Week、Time、Datetime、Datetime-local）
  - search
  - color
  - tel
- ==表单input属性==
  - autofocus：是否自动获取焦点
  - placeholder：输入框为空时的提示信息（适用于text类型）
  - required：规定不能为空
  - multiple：输入框可以多选（适用于select、email和file）
  - list：绑定一个datalist，并从中获取输入值
- 表单新属性
  - autocomplete：可选on/off，启动输入自动补全
  - novalidate：提交时不进行任何前端验证
- 表单新标签
  - datalist（需要与input的list属性绑定，类似于select）
    - 在datalist中选中值后，会传送到input框中
  - keygen



## 画布与多媒体

- 画布

  - html元素：canvas（width，height）
  - 获取画布：const ctx = document.getElementById(“”).getContext(“2d”)
  - 绘制矩形：
    - fillRect(x,y,w,h)：填充矩形
    - strokeRect(x,y,w,h)：矩形框
    - clearRect(x,y,w,h)：透明矩形（让一块区域完全透明）
  - 绘制路径：
    - beginPath：新建一个路径
    - closePath：如果路径未闭合，则将起点和终点相连，使路径闭合
    - 标出轮廓（不会实际画出）
      - moveTo(x,y)
      - lineTo(x,y)
      - arc(x,y,r,起始角,结束角,是否顺时针绘制)
      - arcTo(x1,y1,x2,y2,r)
    - 设置样式
      - strokeStyle：画出路径的样式
      - fillStyle：填充时的样式
    - 绘制
      - stroke：画出路径
      - fill：填充闭合路径，形成图形
- 多媒体
  - audio（src、autoplay、controls、muted、loop）
    - src属性：播放的url
    - autoplay：无值，如果出现，就启用自动播放
    - controls：无值，如果出现，就启用控制台（比如播放暂停）
    - \<source>：可以指定多个src
  - video（src、autoplay、controls、muted、loop）
    - 同上
