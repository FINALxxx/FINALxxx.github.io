# Cache概述

## 3C定理

- 影响Cache miss的三种情况
  - Compulsory：一定会发生miss，但是可以通过prefetching（预取指）来减少发生miss的频率
  - Capacity：容量
  - Conflict：冲突，除非一一映射，总会有少数情况会发生两个数据映射到同一个block中，这时候可以使用Victim Cache来减少冲突

## 规格与组成

- Cache由SRAM组成
- 一个Cache有多个Set
- 一个Set有**多路**Cacheline
- 一个Cacheline有多个字段
  - Valid：当前行是否有效
  - Tag：唯一标记一个Cacheline
  - Cache Block：一行数据



## 映射方式

#### 1. 直接映射

- 地址形式：[Tag,SetIndex,BlockOffset]
- Cache形式：
  - 一个Cache有S个Set => Set = Cache[S]
  - 一个Set只有一个Cacheline => Cacheline=Set[0]
  - 所以所有SetIndex相同的，都会访问同Set的同一个Cacheline，去匹配再访存，产生了较大的Conflict



- Set selection：Set = Set[SetIndex]
- Cacheline selection：Cacheline = Cache[S]
  - line matching
    - isValid = Cacheline.Valid
    - isMatch = Cacheline.Tag == Tag
    - if(isValid && isMatch) [Hit] else [Miss]
- word extract



#### 2. 组相连

- 地址形式：[Tag,SetIndex,BlockOffset]
- 一路就是一个Cacheline的意思
- Cache形式：
  - 一个Cache有S个Set => Set = Cache[S]
  - 一个Set有W个Cacheline => Cacheline = Set[W]
  - ==地址中不记录数据存放在哪一路==：所以所有SetIndex相同的，由于不知道存放在哪一路，会先遍历每一路Cacheline，每次匹配Tag，成功再访存，减少了一定的Conflict



- Set selection：Set = Cache[SetIndex]
- Cacheline selection：for(w:0->W) Cacheline = Set[w]
  - line matching
    - isValid = Cacheline[w].Valid
    - isMatch = Cacheline[w].Tag == Tag
    - if(isValid && isMatch) [Hit] else [Miss]
- word extract



#### 3. 全相联

- 地址形式：[Tag,BlockOffset]
- Cache形式：
  - 一个Cache只有一个Set => Set = Cache[0]
  - 一个Set有W个Cacheline => Cacheline = Set[W]
- Set selection：Set = Cache[0]
- Cacheline selection：for(w:0->W) Cacheline = Set[w]
  - line matching
- word extract



## 读Cache

- ReadHit
  - data = Cacheline.block[BlockOffset:End]
- ReadMiss
  - ReadAllocate：当Miss时，内存先同步数据给Cache，然后CPU再读入Cache的数据



## 写Cache

#### 1. WriteHit

- WriteThrough
  - CPU写入Cache，然后Cache立即同步给内存
  - CPU需要该数据时，可以直接在Cache中命中，因此一定可以拿到新数据
  - Cache会占用总线，使得速度较慢
- WriteBack
  - CPU将新数据写入Cache，**并将该cacheline标记为dirty**，Cache需要被替换时才将新数据同步给内存，然后清除dirty
  - 替换前：CPU需要该数据时，可以直接在Cache中命中，因此一定可以拿到新数据
  - 替换后：CPU需要该数据时，Cache一定会Miss，但由于Cache替换后已经将新数据写入内存，因此执行WriteMiss一定可以拿到新数据

#### 2. WriteMiss

- WriteAllocate（内存分配给Cache）
  - 当Miss时，内存先同步旧数据给Cache，然后CPU再修改Cache为新数据（修改旧数据的一部分->新数据）
  - 当CPU下次需要读该数据时，一定会ReadHit，因此一定会读入Cache中的新数据，而不是内存中的旧数据
- NonWriteAllocate（内存不分配给Cache）
  - 当Miss时，CPU直接将新数据写入内存
  - 当CPU下次需要读该数据时，一定会ReadMiss，因此一定会读入内存中的新数据，而不是Cache的旧数据

#### 3. 组合

- 一般WT和NonWriteAllocate连用，WB和WriteAllocate连用，因为可以复用彼此的数据通路



## 替换算法

- 基本概念
  - 什么时候用替换算法：当Cache满，但还需要写入Cache的时候
  - 替换什么：每次替换一个Cacheline

- Random
- LRU
  - 给每一个Cacheline增加一个age-bit，它记录“剩余时间”
  - 当数据进入Cacheline时，age-bit赋初始值为T，每次读写后，未使用的数据age-bit--，使用的数据age-bit=T
  - 当需要替换时，优先替换age-bit最少的



# Cache实现原理

## 流水线拆分

- 实现：ICache、DCache，它们需要并行访问或者串行访问，因此需要在地址计算之后，增加一个相关性检查的阶段



#### 1. 并行读（assume Set-Associated Cache）

- Addr Calculation
  - 计算地址，地址包含Tag、SetIndex、Offset
- Disambiguation
  - 将SetIndex进行decode，进行相关性检查
- Cache(Tag&Data) Access
  - 选中SetIndex的组，取出每一个Cacheline的Block部分，作为一个Array
  - 选中SetIndex的组，取出每一个Cacheline的Tag部分，作为一个Array
  - 将Block和Tag部分进行Mux，最终得到Hit/Miss，并给出取出的数据
- Result Drive
  - 输出Hit/Miss
  - 将得到的数据，进行Data Align（即根据Offset选中数据的部分作为输出）

#### 2. 串行读

- 和并行访问差不多，只是必须先计算Tag再计算Block，而不能同时计算
- Addr Calculation
- Disambiguation
- Tag Access
- Data Access
- Result



## 多端口Cache

- 目的：超标量cpu有时会在1clk中同时发出多个load和store请求，如果cache支持1clk接收多个ls，可以加快处理速度

#### 1. True Port

- 注：这个实现太慢且面积太大，不建议使用。
- 需要cache中的每一个SRAM cell都支持dual-port read/write
- 还需要设计两个Mux、Decoder等

#### 2. Multiple Cache Copies

- 在一个Cache中设计两个Cache(=2tag+2data)，支持1个store和2个load
- 仍然需要设计两个Mux、Decoder等
- 这种方法就不需要sram支持dual-port read/write了
- 但是这种方法在store时需要维护两个cache之间的一致性，每次写入都相当于同时写两次。

#### 3. Multi-banking

- 将cache分为两个区域bank0、bank1
- 当端口之间访问的bank不同时（no-bank-conflict），两个bank可以并行响应两个端口
- 当端口之间访问的bank相同时（bank-conflict），此时一个bank只能响应一个端口

