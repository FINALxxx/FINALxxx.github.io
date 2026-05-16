# Scala

## 函数

1. 无参普通函数可以省去参数的括号，无参匿名函数需要保留括号
   - 一般无副作用的函数省括号，反之保留括号
2. 无返回值函数可以省去返回值类型。
3. 简单方法体函数可以省去花括号。

- 普通函数：
  - def func(a:T1):T2 = {...} 
  - 传名调用格式：def func(a :=> T1):T2 = {...} 
- 匿名函数：
  - (x:T1,y:T2) => ...
- 函数变量：
  - var f = (x:T1) => T2
  - 表示了一个函数变量（与函数名等价），它的入参为T1类型的x，返回类型为T2
  - 可以使用f()调用这个函数



## 类与对象

1. 无参构造方法可以省去类名旁的括号
2. 子类重写父类的方法时，子类方法与父类方法之间，相同的参数要加上override前缀



- 普通类：
  - class cls(...) extends ... {...}
  - 主构造函数：无需显式定义
  - 辅助构造函数：可选，名字固定为this，且第一行必须显式调用主构造函数this()
- 伴生类：
  - object obj extends ... {...}
  - 伴生类定义唯一的对象的信息，因此里面的信息全是对象公有的，保证了创建的对象都是一样的状态（单例模式）
  - 伴生构造方法：可选，固定为apply
  - 当存在伴生类，实例化主类的对象时，会自动跳转到伴生构造方法apply
    - 一般通过这个apply，去new一个主类对象，这个对象也是唯一的（单例模式）
- 匿名类：
  - var cls = new 被继承的类名excls {...}
  - excls可以是父类，也可以是接口





# Chisel

## 赋值

- 初始创建：使用=
- ==硬件重复赋值==：使用:=。
  - Bundle与Vec等软件量必须硬件化后才能被赋值

## 常量

- 类型：UInt()、SInt()、Bool()、Clock、Reset等

  - 互相可以转换，使用asUInt、asSInt、asBool、asBools、asClock
  - 常量类型表示：UInt(x.W)
- 特殊类型：Clock、Reset



- 定义：

  - 常量数值定义：x.U，位宽会自动适配
  - 常量完全定义：x.U(y.W)



- 非二进制常量：
  - 字符串表达法：“hff”.U、“b1010_0011”.S、“A”.U
  - ==注意：第三个例子是ASCII码的表达==



## 运算符

- 加减法进位

  - +&、-&：会扩展位宽，以放入进位

  - +、+%、-、-%：不会扩展位宽

- 比较

  - 等于：===
  - 不等于：=/=
  - 与scala不一样，目的是为了让==和!=有用

- 位规约

  - x.andR、x.orR、x.xorR

- 位索引

  - x(end,start)

- 向量复制Fill

  - Fill(n,x)：将常量x复制n遍

- 向量拼接Cat

  - Cat(x,y,z)：注意，xyz位宽需要一致，==推荐==
  - x ## y ## z：同上，但==不推荐==

- 向量生成Seq

  - Seq(x,y,z)：将生成一个序列，包含xyz，位宽选最大的一个
  - ==Seq包含了Seq、Map、Vec等序列，这些序列有共通的函数，在这里仅列举一个==
    - Seq.fill(num)(x)：将num个x加入序列中
    - 其他函数：map、flatMap、zip、foreach、filter、exists、contains等

- 二选一选择器Mux

  - Mux(sel,a,b)：这里a和b可以是任意的Chisel基本类型或聚合类
  - ==多路选择器见Vec一节==



## 硬件描述

Seq序列（如Seq、Vec等）只有硬件化（即Wire、Reg、IO化）之后，才能被硬件所利用，最终写进网表

### 1. 寄存器（Reg方法）

T表示寄存器要保存的任意类型

VT表示寄存器要保存的常量类型

- Reg
  - Reg(T)
- RegInit：
  - RegInit(init:VT)
- RegNext：
  - RegNext(next:T)
  - RegNext(next:T, init:VT)
- 寄存器组：init或者next传入一个Vec类型即可
- 其他：
  - RegEnable
    - RegEnable(next:T, enable:Bool)
    - RegEnable(next:T, init:VT, enable:Bool)
  - ShiftRegister

### 2. 线网（Wire方法）

- Wire
  - Wire(T)
- WireDefault
  - WireDefault(init:VT)



### 输入输出端口（IO方法）

- IO
  - IO(T)：一般传入一个bundle，里面使用INPUT()、OUTPUT()声明多个IO端口



## 复合描述

### 1. 捆绑（Bundle类）

- 普通捆绑类：class cls extends Bundle { 捆绑 }

- 匿名捆绑类：new Bundle { 捆绑 }

- ==整体连接==：

  - chisel3不支持<>语法
  - ==以下是需要翻转端口和不需要翻转端口的例子==

  

  - 假如要建立IF阶段到ID阶段的io连接，可以这么做：
    - 建立IF2ID的bundle，假设端口类型均为OUTPUT
      - ==注意==：IF2ID中的OUTPUT端，在IF阶段是输出端口，但是在ID阶段是输入端口
    - IF阶段的io端口中，实例化IF2ID作为io端口
    - ID阶段的io端口中，实例化IF2ID作为io端口，并将IF2ID对象翻转化（套一个Flipped()）

  

  - 假如要建立A阶段到顶层模块的io连接，可以这么做：
    - 建立A2TOP的bundle，假设端口类型均为OUTPUT
      - ==注意==：A2TOP中的OUTPUT端，在A阶段是输出端口，在TOP处也是输出端口
      - 这是因为TOP可以直接把A的输出当作自己的输出，不需要输入到TOP内部



### 2. 向量（Vec方法）

Vec中的数据类型可以任意，从UInt到Bundle、IO再到Module都可以

- Vec
  - Vec(num,T)：存放num个T类型的常数
- VecInit
  - VecInit(Seq)：接收一个Seq并转化为Vec
- MixedVec
- Vec与UInt可以互相转化：
  - UInt -(asBools)-> Vec
  - Vec  -(asUInt )-> UInt
- 高维Vec的提取
  - Vec(2)(end,start)：取第三行的(end,start)范围
- ==Vec部分赋值==
  - 方法一：先将Vec进行Wire化，再通过多次提取进行赋值，最后asBools变回Vec
  - 方法二：先将Vec进行Wire化，再将Bundle类进行Wire化，Vec赋值给Bundle就能进实现拆解赋值，然后改变Bundle的成员值即可
- ==多路Mux==
  - 实际上就是用向量构造一个lookup表
  - 代码上就是将Vec给Wire化



## 3. 硬件化

软件量必须硬件化后才能赋值

- Bundle-Wire化
  - Wire(new Bundle{})
- Bundle-IO化
  - IO(new Bundle{})

- Bundle-Reg化

  - Reg(new Bundle{})

- Bundle-硬件化时初始化

  - 只能先将Bundle通过一个中介Wire来硬件化，再赋值，最后再硬件化

  - 以RegInit举例：
    - val wb = Wire(new Bundle{})
    - wb.xxx := ...
    - RegInit(wb)

# Chisel判断、匹配与循环

## 判断

- when - .elsewhen - .otherwise

  - 不要使用scala中的if-else

  - 注意.符号

## 匹配

- switch - is
  - 不要使用scala中的match-case

## 循环

- for、while、dowhile










# Verilog生成

## 主函数入口

- 形式：object Main extends App{}

  - 其中，伴生类的类名任意，chisel会自动生成一个同名的主类

- 主函数可以进行Verilog显示、文件生成

  - 显示：getVerilogString(new ModuleName())
  - 文件生成：emitVerilog(new ModuleName())
    - 指定生成位置：emitVerilog(new Hello(), Array("--target-dir", "从根路径开始的生成位置"))

  





# ScalaTest & ChiselTest

## ScalaTest

- maven引入依赖库：

  - libraryDependencies += "org.scalatest" %% "scalatest" % "3.1.4" % "test"

  

- 测试形式：“A” should “B” in { 测试体 }

- 判断形式（测试体中）：实际量表达式 should be (预计量)



- 显示格式：
  - 当测试题中所有的==实际量表达式的结果\=\=\=预计量==时，那么将显示：测试“A”中，可以正常执行项目“B”



## ChiselTest

- maven引入依赖库：
  - libraryDependencies += "edu.berkeley.cs" %% "chiseltest" % "0.5.1" % "test"
- 导入chisel测试包：
  - import chiseltest._
  - import org.scalatest.flatspec.AnyFlatSpec
- 形式：





# chisel常用硬件原语

## 多路选择器

- 二选一：

  - Mux(sel,a,b)

  - chisel3

- 多选一：

  - MuxCase(default,Array(条件1 -> 输出1, 条件2 -> 输出2, ...))

  - chisel3.util

- 多选一：

  - MuxLookup(索引, default, Array(0.U -> a, 1.U -> b, ...))

  - 其实就是Vec进行wire化后的查找表
  - chisel3.util

- One-Hot多选一：

  - Mux1H有三种形式：

    ```scala
    val hotValue = Mux1H(selector,Seq(2.U,4.U,8.U,11.U))
    
    val hotValue = Mux1H(Seq(selector(0),selector(1),selector(2),selector(3)),Seq(2.U,4.U,8.U,11.U))
    
    val hotValue = Mux1H(Seq(
        selector(0) -> 2.U,
        selector(1) -> 4.U,
        selector(2) -> 8.U,
        selector(3) -> 11.U
    ))
    ```

  - chisel3.util

## 优先编码器

- 普通：

  - PriorityEncoder有两种等价形式：

    ```scala
    PriorityEncoder("b1010".U)
    PriorityEncoder(Seq(true.B, false.B, true.B, false.B))
    ```

  - 返回值类型都是UInt，值为1.U。

- 独热：

  - PriorityEncoderOH有两种==非等价==形式：

    ```scala
    PriorityEncoderOH("b1010".U)
    PriorityEncoderOH(Seq(false.B, true.B, true.B, false.B))
    ```

  - 第一种形式返回一个UInt的数据2.U。

  - 第二种形式返回一个Seq：Seq(false.B, true.B, false.B, false.B)。

## ROM只读存储器

- 给读取ROM的wire或reg，赋予常量值(注意理解这句话)。
  - 因此，只要能初始化数据，并能取出，就能叫ROM
- vecinit，vec，mixedvec，mixedvecinit，reg组都可以构成ROM

## RAM随机存储器

- 实际上是在写一个与内存交互的接口

- 异步存储器Mem

  - ==不要使用==，它会给出fpga中无法实现的结构

- 同步存储器syncReadMem

  - syncReadMem(size,VT)

  - 写入：

    ```scala
    when(wen) {
         mem.write(waddr, wdata)
         out := DontCare
    }
    //其中DontCare告诉Chisel的未连接线网检测机制，写入RAM时读端口的行为无需关心。
    ```

  - 读取：

    ```scala
    out := mem.read(raddr, en)
    ```

- 同步存储器的单双端口推断

  - 如果mem读时不能写，写时不能读（即读写互斥），则推断为单端口
  - 如果mem读写可同时进行（即读写不互斥），则推断为双端口

- 同步存储器的写入mask推断

  - 如果syncReadMem(size,VT)定义时，传入的类型是一个Vec，那么推断mem为可以写掩模的存储器
  - ==此时的写入==：mem.write(waddr, wdata, wmask)





# scala集合

## 可变与不可变

- 不可变集合，就是指该集合对象不可修改，每次修改就会返回一个新对象，而
  不会对原对象进行修改。类似于java中的String对象
- 可变集合，就是这个集合可以直接对原对象进行修改，而不会返回新的对象。类似
  于java中StringBuilder对象

- 以下为不可变集合

## Seq、Set、Map

- Seq常用：

  - Seq

  - IndexedSeq：Array(顺序表，索引查找快)、String
  - LinearSeq：List(链表，节点插入快)、Queue、Stack

- Set常用：
  - Set(无序、去重)
  - HashSet(无序、去重)、TreeSet(数值顺序，去重)、ListSet(插入顺序，去重)
- Map常用：
  - Map
  - HashMap、TreeMap、ListMap



## 常用高阶方法

- 执行：foreach forall map flatmap reverseMap
- 组合：zip
- 过滤：filter
- 子串：exists segmentLength
- 变换：sorted sortBy sortWith
- 聚合：reduce fold
- 查询：indexWhere



## Seq方法

- 非高阶函数：

  - take drop

  - indexOf
  - indexOfSclice
  - startWith
  - contains

  

  - padTo patch updated
  - reverse

  

  - intersect diff union
  - permutations combinations

- 操作符：

  - ::

  - :+
  - +:
  - ++
  - ++:

