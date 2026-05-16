# RTOS基础

## 基础

- FreeRTOS使用systick作为调度时钟，所以不要设置systick的重装值，阻塞delay需要用TIM来计时

- 变量/函数/宏名的特点

  - 前缀+变量名：p表示指针，x表示BaseType_t，pr表示static

  - 前缀+文件+函数名：p表示指针，x表示BaseType_t，pr表示static

  - 文件+宏名




## 内存管理

- 不使用系统堆的malloc/free的原因：线程不安全、堆资源紧缺、内存碎片化等

- 所以使用ucHeap堆的pvPortMalloc/pvPortFree：是全局静态数组，即存在于bss段

- ucHeap文件特点

  <img src="C:\Users\FINALx\AppData\Roaming\Typora\typora-user-images\image-20260115115009563.png" alt="image-20260115115009563" style="zoom: 67%;" />

- ==ucHeap4相关函数==
  - pvPortMalloc、pvPortFree：分配/回收堆
  - xPortGetFreeHeapSize：获取当前的堆空闲大小
  - xPortGetMinimumEverFreeHeapSize：获取运行过程中，堆空闲容量达到过的最小值
    - 可以用于推测分配给堆的大小是否合理
  - vApplicaionMallocFailedHook：分配堆失败时执行的hook函数
    - 需要将configUSE_MALLOC_FAILED_HOOK宏置1
- FreeRTOS中，常常将ucHeap堆看成一个==RTOS模拟内存==，然后在这个模拟内存上进行任务的堆和栈的分配
  - 系统内存=系统堆+系统栈+bss+data+其他区域
  - bss=ucHeap模拟内存+其他区域
  - ucHeap模拟内存=任务堆+任务栈



## 任务管理

- 任务**约等于**线程：共享任务堆、私有任务栈，cpu调度的最小单位
- 任务定义：任务用函数实现（多对1），不能返回
- 任务句柄=线程控制块TCB
- 注意任务优先级和中断优先级的定义

#### 任务创建/删除

- 创建使用xTaskCreate(任务函数, 任务名, 栈深度, 任务函数的传参, 优先级, TCBHandler)：在**ucHeap**中分配任务私有栈
  - TCBHandler：表示任务状态，若为NULL，表示任务不存在；一般先判断TCBHandler==NULL再创建任务。
- 创建使用xTaskCreateStatic()：在**用户提供的buffer**中分配任务私有栈
- 删除使用vTaskDelete(TCBHandler)
  - 删除其他任务时，先判断TCBHandler!=NULL，再删除，最后置TCBHandler为NULL
  - 删除自己时任务名填NULL

#### 任务阻塞/挂起/恢复

- 阻塞（睡眠）：vTaskDelay()/vTaskDelayUntil()，进入阻塞态
  - 资源获取后，自动切换到就绪态
  - vTaskDelayUntil()可以结合xTaskGetTickCount()使用
  - **区别忙等：mdelay**
- 任务挂起与恢复：vTaskSuspend()，进入挂起态
  - 资源获取后，执行vTaskResume()/xTaskResumeFromISR()才会回到就绪态
  - 在中断中恢复任务必须使用xTaskResumeFromISR()
- 调度器挂起与恢复：vTaskSuspendAll()，调度器挂起
  - 调度器挂起后，==不会进行线程切换==，而不是将所有线程挂起
  - 必须在本线程中执行xTaskResumeAll()来恢复



#### 任务状态转换

<img src="https://rtos.100ask.net/assets/image5-44e16105.png" alt="img" style="zoom: 80%;" />

- 注意：当block任务被suspend+resume之后，会将延时计数器清零，然后立即进入ready，而不是回到block
- 与状态相关的函数
  - 启动/暂停调度：vTaskStartScheduler()/vTaskEndScheduler()
  - 获取/设置任务优先级：uxTaskPriorityGet()/vTaskPrioritySet()
  - 获取tick数：xTaskGetTickCount()、vTaskGetIdleRunTimeCounter()
  - 获取任务详细信息：
    - pcTaskGetName()
    - eTaskGetState()
    - vTaskGetInfo()
    - uxTaskGetStackHighWaterMark()
    - xTaskGetCurrentTaskHandle()、xTaskGetHandle()
  - 获取所有任务的详细信息：
    - uxTaskGetSystemState()
    - vTaskList()
    - vTaskGetRunTimeStats()
- 除了图中的函数，还可以直接修改就绪/阻塞/挂起队列来更改线程状态

#### 估算任务T的栈深度

- 计算任务中最深的函数调用有K层，每次函数调用中需要保存CalleeSavedReg（8*32b=32B）
- 任务切换时需要保存任务上下文，即保存全部的寄存器（16*32b=64B）
- 则栈深度 = K*调用时保存的寄存器（32B） + 局部变量 + 上下文切换时保存的寄存器（64B）



#### 任务私有存储TLS

- 每个任务（线程）共用进程的资源，但是也有自己的私有存储，叫TLS
- 使用TLS：configNUM_THREAD_LOCAL_STORAGE_POINTERS宏、vTaskSetThreadLocalStoragePointer()



#### RTOS调度算法

- 时间片轮转调度+优先级调度（可配置）
  - 不同优先级之间，使用优先级调度==优先级越大越优先==
  - 同优先级之间，使用时间片轮转调度
- 公平：假设现在有T1-T3在ready态（假设T2是ready队首，下一个是T3），如果T2时间片内被切换为高优先级T4，则返回时不会继续执行T2，而是为了公平，执行T3
- 任务函数不能返回：当线程创建时，os会在线程私有栈中保存LR的值（指向prvTaskExitError函数）。所以任务函数一旦返回，就会拿出LR的值并跳转到prvTaskExitError中。
  - 所以线程函数正常不应该返回，必须运行一个死循环，只有删除线程后才可以返回。
  - prvTaskExitError中包含一个死循环，并且关闭了所有中断，因此线程返回时一定会在这里卡死。
  - 因此线程函数的最后一句，可以写vTaskDelete(NULL)，防止线程异常时系统死机。
- 优先级反转
  - 优先级50准备访问共享资源A，但资源A正在被优先级40访问，所以50需要等待40释放资源A。
  - 但此时优先级46需要执行其他任务，其高于优先级40，因此中断了优先级40的释放过程。
  - 此时：==中级正在运行，低级被中级阻塞（无法释放锁），高级正在等待低级释放锁。==
- 优先级反转的解决方法
  1. 优先级继承：占用资源的低优先级进程，和**正在申请**该资源的进程的最高优先级相同。
  2. 优先级天花板：占用资源进程的优先级，和**所有可以申请**该资源的进程的最高优先级相同。



#### 空闲任务（IDLE）

- 在调度器启动时被创建，当没有任务执行时立即执行IDLE（及时性）
- 第一步是==清理自杀的任务==（如果是A杀B，那么由A来清理B）
  - 如果多个任务反复创建-自杀，且部分任务使用mdelay（不释放cpu）
  - 这会导致IDLE没有机会抢占cpu来清理任务，导致内存堆积，进而造成内存不足
  - ==注意==：其他函数尽量使用vTaskDelay，给IDLE提供使用cpu的机会。
- 第二步是==执行空闲Hook==，作用是：计算cpu占用率、实现rtos低功耗等
  - 使用configUSE_IDLE_HOOK宏、vApplicationIdleHook()来实现空闲Hook
  - ==注意==：空闲Hook不能进入阻塞/挂起态、长时间执行。



## 任务函数的基本格式

```c
#include STM相关
#include RTOS相关
#include C库相关

void myTask(void* args){
    //task inner init
    while(1){
        // task details
        vTaskDelay(100); // 防止循环占用cpu
    }
    xTaskDelete(NULL); // 防止不正常退出循环时跳转到ERROR函数
}
```





## 中断管理

- STM32的中断机制（硬件排队器）：抢占优先级、次优先级==优先级越小越优先==
  - 配置NVIC，\_\_disable_irq()/_\_enable_irq()
- FreeRTOS的中断机制（软件屏蔽）：**BASEPRI寄存器的高四位**来屏蔽
  - vPortRaiseBASEPRI()/vPortSetBASEPRI()
  - portENABLE_INTERUPTS()/portDISABLE_INTERUPTS()
  - taskENTER_CRITICAL()/taskEXIT_CRITICAL()/taskENTER_CRITICAL_FROM_ISR()/taskEXIT_CRITICAL_FROM_ISR()
    - 只能对屏蔽范围内的中断构成临界区
    - 通过一个全局变量来记录临界区进入加1/退出减1的计数，只有为0才能再次进入