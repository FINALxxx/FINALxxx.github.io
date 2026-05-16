## 基础

### 0. 通用头文件

```java
import java.util.*;
```



### 1. 数组

- 形式：
  - int[\][\] a = {} / int a[\][\] = {}
  - int[\][\] a = new int[n\][m\] / int a[\][\] = new int[n\][m\]

- length：形式为a.length
- 行不可以省略，列可以省略：new int[n\][\]
- Array工具类提供的static方法（**方法不属于数组，所以把数组当参数传入**）
  - equals(a1[],a2[])
  - fill(a[],val)/fill(a[],fromIndex,toIndex,val)：注意是个左开右闭区间
  - sort(a[])/sort(a[],fromIndex,toIndex)
  - toString(a[])

### 2. 字符串

- ==String==

  - length()：形式为str.length()，注意和数组的区别

  

  - concat()：str1.concat(str2)，连接字符串
  - spilt()：str1.split(“分隔符1|分隔符2|...”)，分割字符串

  

  - equals()：str1.equals(str2)，==内容比较==，返回true/false
  - compareTo()：str1.compareTo(str2)，==字典序比较==，返回0/正(str1大)/负(str1小)
  - ==：str1\=\=str2比较的是两者的对象（引用地址）

  

  - indexOf()/lastIndexOf()：用子串找起始索引
  - charAt()：用索引找对应字符
  - startsWith()/endsWith()/contains()：判断字符串是否以子串开头/结尾/包含，返回true/false

  

  - replace()/replaceFirst/replaceAll：str.replace(目标串，替换串)，替换目标串

  

  - toLowerCase()/toUpperCase()
  - trim()：去除首尾空格
  - toCharArray()
  - format()：str.format(format,args)

- ==StringBuffer==

  - new StringBuffer(int)：不带字符，但有初始大小的字符串
  - new StringBuffer(String)：有初始内容的字符串
  - append(str)
  - insert(index,str)
  - delete(startIndex,endIndex)

### 3. Collection

- add/addAll
- remove/removeAll
- clear
- contains
- isEmpty
- size
- toArray
- 迭代器



### 4. List

- add
- indexOf/lastIndexOf
- get/set
- subList

## 类与对象

### 1. 权限

- 变量/函数/类成员的权限：public>protected>default>private
- 类的权限：public>default
- 重写时的权限：重写的权限可以相同或更大



### 2. 重写(override)与重载(overload)

- 重写：其他完全一致，只是方法体不一样（权限只能更宽松）
- 重载：参数不同，==对返回值和权限无要求==，方法体也不一样（不能通过返回值是否一致来判断重载）

### 2. 多态

- instanceof关键字，返回bool
- 向上转型：Father f = new Child()，Child转型为Father，==不能再使用Child的类成员==
- 向下转型：Child c = (Child)new Father()，Father转型为Child，不仅能使用Father的成员变量，还能使用Child的方法

### 3. 泛型

- 泛型接口：interface IFName<T\> {}
- 泛型类：class clsName<T\> {}
- ==泛型方法==：public static <T\> T funcName(T x){} （返回值和参数都泛型的例子）
- 泛型变量：T x

### 4. 异常

- try-catch-finally



## 文件操作

- 标准IO流：System.in/System.out/System.err
- 字节流：InputStream、OutputSteam
- 字符流：Reader、Writer
- 随机文件流：RandomAccessFile

## Multi-Thread

### 1. 继承类Thread

- 继承并重写run方法

- 创建线程：Thread(String name).start()



### 2. 自定义类MyThread（实现接口Runnable）

- 自定义类MyThread需要实现接口Runnable的run方法
- 创建线程：Thread(Runnable t, String name).start()



### 3. 线程状态

- 状态：创建（Thread类对象被创建）、就绪、运行（调用run()，自动发生）、阻塞、死亡（run()被终止时）
  - 具体转换表，见书p211
- 优先级：1(MIN)~10(MAX)，默认为5，setPriority()可修改



### 4. 线程控制

- 进程睡眠sleep()、等待子进程结束join()、进程中断interrupt()、进程让步给其他进程yield()

### 5. 线程同步

- 同步块：synchronized (同步资源){}
  - syn-chron-ized
- 同步方法：synchronized void func{}
  - 同步**静态**方法：synchronized static void func{}，==由于static方法与整个类绑定，因此这里的同步对象是这个的**所有**对象==

### 6. 同步控制

- wait()、notify()、notifyAll()

## GUI

### 1. 重要的容器与控件

- Frame/JFrame：默认是BorderLayout
- Panel/JPanel：默认是FlowLayout



### 2. 布局

- 使用setLayout(布局或null)更换布局

- FlowLayout()：上到下、左到右
- BorderLayout()：东西南北中
- GridLayout()：网格布局，==格子的大小无法自定义==
- CardLayout()、CardBagLayout()：卡片、网格袋布局



### 3. 事件

给组件附加事件监听器的两种方法：

1. 继承类xxxAdapter(事件适配器类)
   - 继承并重写对应的事件响应方法
   - 控件.addActionListener(事件适配器类的对象)

2. 自定义类MyAdapter(实现事件侦听器接口xxxListener)
   - 自定义类MyAdapter需要实现接口xxxListener中对应的事件响应方法
   - 控件.addActionListener(MyAdapter类的对象)



## network

- TCP安全但慢，UDP快但不安全
- Socket：TCP流式Socket、UDP数据报Socket

- InetAddress类：描述IP地址
  - 常用：getLocalHost、getHostName、getHostAddress

### 1. TCP-Socket类

- 服务端：ServerSocket，创建一个中继的服务器，代替服务端进行直接连接
- 用户端：Socket，建立到ServerSocket服务器的连接



### 2. UDP-DatagramSocket类

- 没有中继服务器，直接连接
- 发送：
  - 运输车DatagramSocket：规定发送IP和端口
  - 包裹DatagramPacket：被装进DatagramSocket发送出去
- 接收：
  - 用DatagramSocket直接receive，就能得到一个Packet

## Database

- DriverManager、Connection
- Statement、PreparedStatement、ResultSet