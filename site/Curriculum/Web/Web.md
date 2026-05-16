# ASP.NET概念

## web结构

#### 1. B/S结构

- 优点：
  1. 不用装专门的软件
  2. 客户端零维护
  3. 拓展容易，能上网就行

#### 2. C/S结构

- 优点：
  1. 客户端响应速度快

- 缺点：
  1. 需要装专门的软件
  2. 对操作系统有限制
  3. 局域网内使用

#### 3. Web三层结构

- 内容：页面显示层、业务逻辑层、数据访问层

#### 4. HTML

- 定义：为**网页创建**、浏览器**可见的信息**所设计的**标记语言**

#### 5. XML

- 定义：可扩展标记语言，依赖于标签，比HTML功能强大

- 作用：在不同系统中传递结构化数据

## .NET开发

#### 1. 框架简介

- 内容
  - 默认开发语言：C#
  - 四大部分：VS.NET集成开发环境（通用语言开发环境 + .NET基础类库 + .NET开发语言）
- 主流开发框架
  - ==ASP/ASP.NET/PHP/JSP之间的比较==

#### 2. .NET配置文件

- ASP.NET配置文件：
  1. 创建网站时根目录自动生成，web.config，子目录继承它的设置
  2. 子目录web.config手动创建，可以修改除了父目录配置以外的配置信息，也可以重写或修改从父目录继承来的配置

#### 3. .NET控件概述

- ==四种控件==：包括**HTML**、**HTML服务器**、**Web服务器**、Web自定义控件
  1. 运行在客户端，**不能被服务器控制**，只受js、vbs等语言控制
  2. 运行在服务器端**（runat=server）**
  3. ASP.NET特有，是Web Froms编程的基本元素，**又称ASP.NET服务器控件**，不直接描述html，而是根据不同客户端生成html

#### 4. Web服务器常用控件

- 普通控件
- 登录控件

  - ==各控件名称及作用==
  - 概述：是一种**可靠的、无需编程**的登录方案；默认情况下，**登录控件、ASP.NET成员资格、Froms身份验证集成**，以实现**身份验证自动化**。
  - web.config配置文件中的相关属性：authentication和authorization
- 验证控件
  - ==各控件名称及作用==
- 导航控件

  - ==各控件名称及作用==
- 数据绑定控件

  - GridView控件

  - ==各控件名称及作用==
- 主题控件
  
  - 母版页文件：
  
    - 扩展名为.master
  
    - 控件：ContentPlaceHolder、Content
  
  - 外观文件：扩展名为.skin
- 用户控件
  - 用户控件文件和页面文件的区别
    1. 扩展名：.ascx；.aspx
    2. 指令：@Control；@Page
    3. 独立运行：不能；能
    4. 元素：没有html、body或form元素；有

#### 5. .NET对象编程

- 数据持久性对象：**Session**、**Cookies**、**Application**、ViewState
  1. 在**服务器端**保存登录信息，时长短（默认20min），空间小，数据量小，速度快；关闭相关页面/注销用户时被删除。
  2. 在**客户端**保存**更多的登录信息**，时长短（默认20min），空间大，数据量大，速度慢；关闭相关页面/注销用户时被删除。
  3. 在服务器端保存**任意信息**，**时长长**（即网站生命周期），空间任意，数据量任意，速度任意。
     - 由于是共享资源，容易造成死锁，所以必须：先lock，再使用，最后release。
     - 在根目录下的web.config定义
- 数据访问对象：**Server**、**Request**、**Response**
  1. 服务器信息（使用绝对路径）
  2. 客户端提交信息
  3. 服务器响应信息



#### 6. .NET数据库编程

- 访问Access数据库：略
- 访问SQL server数据库（IDO.NET编程）
  - SQL包含的四个部分：数据查询语言（SELECT类）、数据操纵语言（UPDATE类）、数据定义语言（CREATE类）、数据控制语言（COMMIT WORK类）
  - System.Data.SqlClient命名空间：一个“描述SQL server数据库”的类集合
  - ==SQL语句概述==：
    1. SELECT col FROM table
    2. INSERT INTO table(cols) VALUES(values)
    3. UPDATE table SET col = value [通常跟where子句来限定到特定记录，默认是对所有记录做update]
    4. DELETE FROM table [通常跟where子句来限定到特定记录，默认是对所有记录做delete]
- LINQ语法
  - LINQ包括的五个部分：
    1. 数据集：LINQ to Datasets、LINQ to SQL
    2. 实体：LINQ to Objects、LINQ to Entities
    3. 标签：LINQ to XML



## 网站优化

- 2个优化方向（性能、用户体验），4个优化方面（数据库、C#代码、ASP.NET、AJAX技术）
- 数据库方面的4点优化
  1. ADO.NET调用存储过程
  2. LINQ调用存储过程
  3. 合理使用连接池
  4. 优化查询语句
- AJAX技术：是“异步JS和XML”的简称，利用了客户端的技术。





## 控件

- 导航控件比较
  - Menu：动态和静态显示+横向和纵向显示，不占空间（适合各种类型网页）
  - TreeMenu：树形显示，项目展开时占空间（适合空间大的信息页和栏目页）
  - siteMapPath：导航路径显示，适合深层次的多个类似页面



- 验证控件基类BaseValidate的属性：

  - ControlToValidate：指定需要验证的控件
  - Display：如何显示错误信息。值为Static和Dynamic
  - ErrorMessage：验证失败时，显示在ValidationSummary的信息
  - Text：验证失败时显示的信息
  - IsValid：验证是否通过

- 常用验证控件（3R 2C 1V）

  - RequiredFieldValidator：必填验证控件
  - RangeValidator：范围验证控件
  - CompareValidator：比较验证控件
  - RegularExpressionValidator：正则验证控件
  - CustomValidate：自定义验证控件
  - ValidationSummary：验证汇总控件

  

- 登录控件

  - 注册：

    - CreateUserWizard：收集潜在用户提供的信息，并将新用户添加到ASP.NET成员资格系统中
      - CreateUserWizard控件在注册新用户时，如果需要配合找回密码功能使用，==则必须注册时填写安全问题和答案，且在web.config中将requireQuestionAndAnswer设为True==
  - 登录：

    - Login：用户身份验证界面

    - LoginView：登录用户和匿名用户显示不同的界面

    - LoginName：登录用户显示用户名，如果集成了Windows身份验证，则显示Windows账户名、
    - LoginStatus：登录用户显示”注销“，匿名用户显示”登录“
  - 恢复密码：
  
    - PasswordRecovery：用创建用户时输入的电子邮件地址，来找回用户密码
      - PasswordRecovery控件中要想实现发送”找回密码“的邮件，==需要配置SMTP邮箱==
    - ChangePassword提供原始密码，然后更改为新密码



- 数据绑定控件（GDFL）
  - GridView：多条（显示+分页）+不能增+外观多+无模板
    - 优点：行形式显示**多条**记录；列形式支持**删、改、查**（排序+分页显示）；支持外观格式化。
    - 缺点：不支持增（插入）；需自行设计模板
  - DetailsView：单条（显示+分页）+可以增+外观多+模板少
    - 优点：行形式显示**单条**记录；列形式支持**增、删、改、查**（分页显示也只显示一条记录）；支持外观格式化。
    - 缺点：不能显示多条记录；模板功能有限
  - FormView：单条（显示+分页）+可以增+外观多+模板多
    - 优点：行形式显示**单条**记录；列形式支持**增、删、改、查**（分页显示也只显示一条记录）；模板灵活，提供增删改查的模板。
    - 缺点：不能显示多条记录；外观格式化单一
  - ListView：多条（显示+分页）+可以增+外观多+模板兼容性差
    - 优点：行形式显示**多条**记录；列形式支持**增、删、改、查**（排序+分页显示）；支持外观格式化。
    - 缺点：模板兼容性差
- GridView绑定数据的选项：
  - DataSourceID：将GV绑定到**数据源控件**上，并内置了**排序、分页、更新**功能
  - DataSource：将GV绑定到**ADO.NET数据集和数据读取器**上，其他功能需要自己写代码



## 代码

- 实验二上传文件代码