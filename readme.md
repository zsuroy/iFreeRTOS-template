# 基于 STM32CubeIDE 的 FreeRTOS 手动移植模版

> 网上看了一下，发现用CubeIDE的比较少，几乎没有找到用这个IDE集成开发FreeRTOS的，于是我便踩坑尝试了一下。


## 开发环境

+ FreeRTOS: V9.0
+ IDE: STM32CUBEIDE
+ MCU: STM32F103C8T6
+ 工程模版: STM32CubeMX 生成 HAL 库裸机工程

## 功能说明

两个Task任务，实现点亮频闪LED灯～


## 移植说明

> 已经移植上机测试过了，直接编译运行即可。  
> 本文仅记录一下关键步骤，详细移植可以看  
> [使用STM32CubeIDE 进行 FreeRTOS 手动移植F103系列最小系统](https://suroy.cn/embeded/use-stmcubeide-for-freertos-manual-migration-of-f103-series-minimum-system.html)

1. 将FreeRTOS官方源码模版其目录下的 `FreeRTOSConfig.h` 宏定义头文件复制到我们项目中 `FreeRTOS/include/` 文件夹下

2. 修改宏定义

```c

// 以下是变更原配置参数，否则时钟拉起不了
#define configUSE_PREEMPTION		1
#define configUSE_IDLE_HOOK			0
#define configUSE_TICK_HOOK			0 //Suroy: 启用报错，于是关了 22.2.8
#define configCPU_CLOCK_HZ			( ( unsigned long ) 72000000 )	
#define configTICK_RATE_HZ			( ( TickType_t ) 1000 )
```

3. 新增宏定义

```c
/* Suroy: fix some error */
#define INCLUDE_xTaskGetCurrentTaskHandle 1
#define xPortPendSVHandler PendSV_Handler
#define xPortSysTickHandler SysTick_Handler
#define vPortSVCHandler SVC_Handler
```

4. 处理重复定义

注释掉 `stm32f1xx_it.c` 文件中的 `SysTick_Handler` 函数，与FreeRTOS宏定义产生冲突
