# 企业级OTA

## OTA流程

- APP下载阶段
  - 下载固件：==使用一个线程+MQTT+断点续传==将加密固件分段下载到RAM，并发送到外挂flash的**A区域**上存储
  - 校验与写flag：==使用CRC==校验外挂flash上的固件，校验成功，则将需要更新的flag写到eeprom上
- BL更新阶段
  - 读flag：检查eeprom的flag值，为“需要更新”才更新，否则直接跳转到app
  - 解密并转移固件：==使用AES解密==将A区域固件分段拿到RAM中解密，然后发送到外挂flash的**B区域**上存储
  - 备份APP：将APP复制到A区域，作为备份区域
  - 更新固件：将B区域的固件，复制到APP
- 二次校验-复位-跳转阶段
  - 校验栈顶地址、复位向量：略
  - 关闭全局中断：\_\_disable_irq()
  - 关闭所有中断：NVIC->ICER和NVIC->ICPR全部置0xFFFFFFFF
  - deinit时钟、所有外设
  - 设置msp与特权级：__set_CONTROL(0)
  - 设置SCB->VTOR
  - 跳转到复位向量