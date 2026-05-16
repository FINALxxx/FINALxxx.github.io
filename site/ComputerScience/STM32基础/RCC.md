# RCC

## RCC功能

- 设置系统时钟
- 控制总线时钟开关，设置总线分频因子
- 设置外设分频因子



## RCC高速时钟树

#### 主要时钟图

![image-20251230204046559](C:\Users\FINALx\AppData\Roaming\Typora\typora-user-images\image-20251230204046559.png)

- SYSCLK相关
  - 系统时钟SYSCLK的时钟源：HSI、HSE、PLLCLK三选一

- HCLK相关
  - HCLK是高性能时钟，为内核/内存/DMA/AHB提供时钟
  - HCLK默认为AHB的时钟，所以DMA的时钟要挂载到AHB时钟上

- APB相关
  - APB中的倍频与分频：若分频选择/1，则倍频必须选择\*1；反之，倍频必须选择\*2（保证APB定时器的时钟频率尽可能大于APB外设时钟频率）
  - APB1外设：外设的低速端口
  - APB1定时器：**内置分频器**的通用/基本定时器
  - APB2外设：外设的高速端口
  - APB2定时器：**内置分频器**的高级定时器


#### FCLK部分

![image-20251230204157229](C:\Users\FINALx\AppData\Roaming\Typora\typora-user-images\image-20251230204157229.png)

- 在STM32处于休眠模式时，FCLK可提供时钟中断来唤醒

#### 其他部分

![image-20251230204549006](C:\Users\FINALx\AppData\Roaming\Typora\typora-user-images\image-20251230204549006.png)

- FLITFCLK：FLASH接口的时钟，其时钟源为HSI
- USBCLK：USB接口的时钟，其时钟源为PLLCLK
- 时钟安全系统CSS：若HSE不可靠，会导致ClockMux中的HSE和PLLCLK均不可靠，此时CSS负责强制选中HSI

## RCC低速时钟树

![image-20251230205055236](C:\Users\FINALx\AppData\Roaming\Typora\typora-user-images\image-20251230205055236.png)

- RTCCLK：主要使用LSE
- 看门狗CLK：主要使用LSI

## RCC输出

![image-20251230205539525](C:\Users\FINALx\AppData\Roaming\Typora\typora-user-images\image-20251230205539525.png)

- PA8引脚可复用功能，以输出MCO时钟
- MCO时钟的时钟源：HSE、HSI、PLLCLK、SYSCLK

## 定时器TIM

- 基本定时器：TIM6-7
- 通用定时器：TIM2-5
- 高级定时器：TIM1、TIM8

#### 基本定时器计数

- 以下以启用影子寄存器为例：

  - 将分频因子放置在预分频器PSC中，将计数值放在自动重装载寄存器ARR中

  - 当上一轮计数完毕时，将PSC赋给对应的**影子PSC**，将ARR赋给对应的**影子ARR**

  - 由影子PSC作为计数频率，进行计数，计数后将计数器的值与影子ARR比较

  - 直到相等时，计数完毕，==同时产生更新中断和更新事件==

- ==基本TIM的计数模式==：向上计数

- ==启用影子寄存器的副作用==
  - 会使得上电初始化时，手动生成一个更新中断，使得ARR和PSC放入对应影子寄存器。导致了上电时多余的更新中断。
  - 解决：在开启中断前，将更新中断标志位清除。

<img src="C:\Users\FINALx\AppData\Roaming\Typora\typora-user-images\image-20251230211342946.png" alt="image-20251230211342946" style="zoom:50%;" />

#### 通用定时器计数

![image-20251230212906743](C:\Users\FINALx\AppData\Roaming\Typora\typora-user-images\image-20251230212906743.png)

- 给TIM加入了外部输入信号**TI1-TI4**，通过**滤波消抖+边沿检测**，产生**TI_ED、TIFP**信号

  - TI_ED只能双边沿检测，TIFP支持上升/下降/双边沿
  - 具体如下：（TI1FP由TI1产生，TI2FP由TI2产生）

  <img src="C:\Users\FINALx\AppData\Roaming\Typora\typora-user-images\image-20251230213756832.png" alt="image-20251230213756832" style="zoom:33%;" />

- 给TIM加入了外部时钟信号**ETR**，通过**滤波消抖+边沿检测+分频**，产生**ETR**信号

  - PA0引脚可复用功能，以输入ETR

- 图中的触发器逻辑上等价于一个MUX，它选中的源在不同模式下可以作为计数时钟/触发信号来使用



- ==通用TIM的两大时钟模式==

  - 主模式==由定时器去控制外设==
    - **使用内部时钟作为计数时钟**
    - 外部时钟模式2：**使用ETR作为计数时钟**
  - 从模式==由外设去控制定时器的时基单元==
    - 外部时钟模式1：**使用MUX作为计数时钟**
    - 复位模式：**使用内部时钟/ETR作为计数时钟，使用MUX作为复位信号**
      - 触发器MUX的复位信号**会产生更新中断、触发器中断**
    - 门模式：**使用内部时钟/ETR作为计数时钟，使用MUX作为使能信号**
      - 触发器MUX的信号有效时，则开始计数；反之，暂停计数
      - 触发器MUX的有效信号**不会产生更新中断，会产生触发器中断**
    - 触发模式：**使用内部时钟/ETR作为计数时钟，使用MUX作为使能信号**
      - 触发器MUX的信号有效时，则开始计数；反之，不会暂停计数
      - 可以配合单脉冲的PWM方式：即重装载后自动停止计数
  - ==注意==：不管内部时钟是否作为计数时钟，都要开启它，因为它除了用于计数，还用于给定时器基础操作提供时钟。



- ==通用TIM的三大计数模式==：向上计数、向下计数、中央对齐计数



- ==通用TIM的输入输出==

  - **输入捕获**4个通道TI1-TI4，**输出比较**4个通道TO1-TO4
    - 由于TI和TO共用4个引脚和一个CCR，所以一个TIM不能同时输入输出
    - 输入捕获（IC）可用于测量**0\~3.3V的方波**，输出比较（OC）可用于产生**PWM方波**
    - 整个输入捕获+输出比较的模块称为捕获比较模块（CC），其寄存器称为CCR
  - 输出比较
    - 比较CNT和CCR的值的不同关系，来对输出置1/置0/翻转，用于输出一定频率和占空比的PWM波形
    - TO1-T04可以连接到GPIO（通过GPIO复用）



#### 高级定时器计数

- 重复次数计数器：重装载多次之后，才触发一次更新中断/更新事件
- 高级TIM的输入输出
  - 刹车输入TIM_BKIN：当TIM_BRIN或CSS检测到时钟失效时，会强行关闭输出信号
  - 前三个通道的IC具有交叉输入的功能（用于接收三相无刷电机的霍尔传感器）
  - 前三个通道的OC具有死区生成和互补输出的功能（用于驱动三相无刷电机）





## 通用定时器流程

![image-20251230224910696](C:\Users\FINALx\AppData\Roaming\Typora\typora-user-images\image-20251230224910696.png)

- 将TIM挂载到RCC树
- 初始化TIM：计数时钟选择、时基单元、OC、IC等
- 初始化NVIC（可选）
- 运行TIM：运行控制



## 输出比较（OC）流程

![image-20251231144647893](C:\Users\FINALx\AppData\Roaming\Typora\typora-user-images\image-20251231144647893.png)

![image-20251231144551471](C:\Users\FINALx\AppData\Roaming\Typora\typora-user-images\image-20251231144551471.png)

- 该PWM采用OC模式中的**PWM模式1**
- 黄线是ARR的值，红线是CCR的值，蓝线是CNT的计数值



## 输入捕获（IC）流程

![image-20260101160207407](C:\Users\FINALx\AppData\Roaming\Typora\typora-user-images\image-20260101160207407.png)

- 只能对==0\~3.3V的方波==进行测量，若测量波不满足方波或电压范围，需要先转换为0\~3.3V方波
- 测频法适合测高频信号，测周法适合测低频信号
  - 在测量之前定一个T和$f_c$，算出中界频率（它与待测波形无关，至于测量方法的参数有关）
  - 高于中界频率算高频，低于中界频率算低频
  - 测频法和测周法的误差表现在**±1误差**上

![image-20260101162441467](C:\Users\FINALx\AppData\Roaming\Typora\typora-user-images\image-20260101162441467.png)

![image-20260101162500708](C:\Users\FINALx\AppData\Roaming\Typora\typora-user-images\image-20260101162500708.png)

- PWMI中CCR1记录全周期，CCR2记录有效周期，即可同时算出PWM频率和占空比

## 定时器计算

- 定时器频率$f_t$=PCLK
- 更新频率$f_u$=计数频率=$f_t/\frac{PSC+1}{ARR+1}$
- 更新周期$T_u$=$1/f_u$==（计数频率/分频系数/重装值）==
  - 它表示定时器**更新1次或计1个数**需要花费$T_u$秒
  - 所以计(ARR+1)个数，会花费$(ARR+1)\times T_u$秒
  - 注：因为PSC和ARR都从0开始计数，计算时要+1



- PWM频率$f_{PWM}$=更新频率$f_u$
- PWM全周期$T_{full}$=更新周期$T_u$
- PWM有效周期$T_{valid}$=到达PWM阈值的时间=$\frac{CCR}{ARR+1}\times T_u$
- PWM占空比$Duty=T_{valid}/T_{full}=\frac{CCR}{ARR+1}$
- PWM分辨率$Reso=K*CCR$
  - 若占空比不变，则ARR越大，CCR就越大，分辨率就越大，所以Reso与CCR成正比
  - 分辨率越大越好，说明全周期大，PWM变化范围大，可调频率就细腻



# RTC实时时钟

- STM32后备区域（包含RTC与VBAT与TAMPER）

  ![image-20260107153604877](C:\Users\FINALx\AppData\Roaming\Typora\typora-user-images\image-20260107153604877.png)

- RTC时钟源与RTC输出

  - RTC时钟源见低速RCC树的RTC
  - RTC输出见RCC输出的MCO
  - VBAT可以支持LSE的晶振运行

- 跨时钟域问题

  - RTC挂载在APB1下，即读写RTC使用PCLK1
  - 但RTC也挂载在RCC低速时钟树上，即RTC内部使用RTCCLK
  - 所以总线读写RTC的时钟和RTC内部时钟不一致，产生跨时钟域问题
  - 由于PCLK1比RTCCLK快，所以读写RTC之前必须等待RTC内部不BUSY时，再进行操作