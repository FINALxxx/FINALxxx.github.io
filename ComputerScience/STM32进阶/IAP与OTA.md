# 固件更新

## 基础

- 固件更新的三种方式
  - ICP：烧录更新主Flash
  - ISP：串口更新主Flash
  - IAP：串口**在线更新**主Flash
  - OTA：远程**在线更新**主Flash
- IAP和OTA需要将用户Flash分割为Bootloader区和用户程序区





## IAP理论

- 程序开头为中断向量表，中断向量表格式为：栈顶地址、复位向量地址、其他中断向量地址

<img src="C:\Users\FINALx\AppData\Roaming\Typora\typora-user-images\image-20260118165542182.png" alt="image-20260118165542182" style="zoom:67%;" />



#### 地址分割

- 主Flash分割为IAP程序、用户程序
- SRAM分割为IAP程序使用的SRAM、用户程序使用的SRAM

#### BL实现

- 用USART将用户程序输入到SRAM中（USART->数组buf）
  - ==优化==：使用DMA中断、双缓冲区、数据校验
  - 使用\_\_attribute\_\_ ((at(0X20001000)))设定一个固定SRAM地址的数组（因为SRAM前面部分是留给IAP用的）

- 将SRAM的用户程序输入到主Flash中（数组buf->主Flash）
  - 按半字写入，需要处理字节序
- 跳转到主Flash的用户程序
  - ==校验==
    - 校验栈顶地址
    - 校验复位向量地址
    - 其他校验步骤

  - ==关闭中断==（为了形成临界区）
    - 在NVIC中关闭中断
    - \_\_disable_irq()

  - ==设置MSP==（原因见注意事项）
  - ==跳转==
    - 找到用户程序main()地址appAddr，若设对应函数指针为void (\*appFunc)(void)，则有appFunc = (appFunc)\*(uint32_t\*)appAddr
    - 使用appFunc()跳转到用户程序




#### APP实现

- ==关闭中断==（防止直接执行APP程序时，直接开中断）

- ==设置中断向量表的偏移==
  - 使用SCB->VTOR，CPU会自动找到偏移后的中断向量表

- ==内核初始化==
  - 先对时钟和外设Deinit、再Init（原因见注意事项）

- ==开中断==
- 其余和正常main一致



## 存在RTOS的IAP注意事项

[Bootloader核心原理与简单实现：从零写一个bootloader_bootloader中 防止逆向工程-CSDN博客](https://blog.csdn.net/leilei050213/article/details/152273193)

- systick定时器和关中断问题

  - 则跳转App之前需要关闭systick定时器和关中断。

  - 在App中需要先对外设和时钟DeInit，然后再对外设和时钟进行Init，最后再开启中断。
    - 因为有些设备（如PLL）必须在DeInit重新激励才能初始化
- MSP和PSP切换问题
  - BL是RTOS的用户程序，而用户态会使用PSP（防止污染内核态的MSP）
  - 但APP程序进入时是内核态，必须用MSP
  - 如果直接跳转而不设置MSP，则APP进入时还是用的PSP，导致出现MemFault



## IAP实践

- 使用UARTEx_ReceiveToIdle_IT()或UARTEx_ReceiveToIdle_DMA()
- 如果每次传输256B时丢数据，可以考虑：增加单次传输之间的延时、降低波特率
- 使用指针访问外设/flash时必须要加volatile
- ==程序文件端序、UART端序和flash端序==
  - hex/bin文件为Byte大端序，Bit大端序
  - UART不考虑端序（与发送文件一致），但内部发送时的Bit端序实际上是小端序
  - flash为字节小端序，Bit大端序
  - 所以写入flash时必须调整Byte端序
- 可以活用状态机



# 企业级OTA

- 有三个分区：app/download/factory

## 四个基本模块

#### 1. bootloader总体状态

- 更新流程控制flow_flags
- 固件更新状态update_flags
- 传输状态transfer_flags

#### 2. 固件管理模块

- 包含函数：读写存储设备、检测固件是否可用（检查数据包是否存在、校验数据包）、解密数据包

#### 3. 数据传输

- 包含函数：数据收发

#### 4. 协议解析

- 包含函数：解析数据包

## 上电时

- 读取flash/eeprom中的更新方式标志位
  - 若需要复位，则直接进入APP分区
  - 若需要下载后从DOWNLOAD区更新，则设置flow_flags.method=UPDATE_FROM_DOWNLOAD
  - 若需要直接从FACTORY区更新，则设置flow_flags.method=UPDATE_FROM_FACTORY
  - 清除更新方式标志位



## 初始化时

- (内核时钟初始化之后)
- 存放固件的设备初始化
  - 初始化flash、eeprom等
  - 初始化对应设备的驱动（如fal和SFUD）
  - 初始化crc32
  - 初始化update_flags
- 传输初始化
  - 启动超时定时器，若超时未重启，会设置transfer_flags.status为传输超时
  - 初始化传输接口（如UART）
  - 初始化协议解析器（如ymodem）



## 进入BootloaderLoop

- 重启定时器

- bootloader状态机

  - flow_flags.method==UPDATE_FROM_DOWNLOAD
    - 存放的固件不可用，则设置flow_flags.method=UPDATE_INIT
    - 存放的固件可用，则直接进入APP分区

  

  

  - flow_flags.method==UPDATE_FROM_FACTORY
    - 存放的固件不可用，则设置flow_flags.method=UPDATE_INIT

  

  

  - flow_flags.method==UPDATE_INIT
    - 重置协议解析器
  - flow_flags.method==UPDATE_WAIT
    - 等待