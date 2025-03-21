# 说明

基于lvgl和freertos的智能手表项目

芯片：STM32F411RET6

库：使用hal库

工具：cubemx、keil、sourceinsight





# 使用cubemx创建工程

**设置时钟树:** 

![start_one](.\images\config\RCC.png)

- 使用的STM32F411RET6芯片有两个外部晶振，HSE为25MHz，LSE为32.768KHz

-------

**设置DMA，串口接收发：**

![start_two_USART1](.\images\config\USART1.png)

![USART2](.\images\config\USART2.png)

- 配置usart1引脚和启动中断

![USART3](.\images\config\USART3.png)

- 设置USART的DMA通道，配置为DMA2的流5和流7。每个流都连接到专用的硬件DMA请求，并支持每个流上的软件触发。配置由软件完成，源和目标之间的传输大小是独立的。方向分别设置为外设到内存（RX）、内存到外设（TX）。

-----

**配置SPI：**

![SPI1](.\images\config\SPI1.png)

- 设置为仅主机发送模式，主要用于将内容打印到LCD屏幕上，因此也只需要设置MOSI引脚就好了。至于波特率，后面设置时钟后会变为50.0 M Bit/s

![SPI2](.\images\config\SPI2.png)

- 设置为DMA模式，这样刷屏的时候不需要经过CPU，在于多任务系统下效率应该会更高，因为CPU也可以去干其它事情。

----

**RTC：**

![RTC1](.\images\config\RTC1.png)

![RTC3](.\images\config\RTC3.png)

- 设置实时时钟，具体都是什么意思看附图就好了

![RTC2](.\images\config\RTC2.png)

- 使能中断

----

**时钟树配置：**

![Clock_config](.\images\config\Clock_config.png)

- 配置完后发现，SPI的波特率变为50就没问题
- ![Clock_config_1](.\images\config\Clock_config_1.png)

-----

**设置优先级NVIC：**

![NIVC](.\images\config\NIVC.png)

----

**启用FreeRTOS：**

![FreeRTOS](.\images\config\FreeRTOS.png)

----

**other：**

![SYS](.\images\config\SYS.png)

----

![over1](.\images\config\over1.png)

- 使用MDK-ARM



# 导入lvgl库

直接把库复制到middleware下就行了，这里把lvgl放在了根目录下，



# keil工程

![keil1](.\images\keil1.png)

![keil2](.\images\keil2.png)

- 设置复位和启动

![directory](.\images\directory.png)

- lvgl的库放在middlewares中，然后创建BSP文件夹用于存放底层驱动，SYSTEM是延迟文件夹，Task用于存放实现多任务的代码（主体）

接下来就将这些文件和keil工程关联起来就好了
![keil3](.\images\keil4.png)

对应的头文件关联设置：

![keil3](.\images\keil3.png)

创建的组GUI、GUI_Port、GUI_App、GUI_Font_img、Task：

- GUI：存放lvgl的一些函数文件
- GUI_Port：使用官方提供的模板改动而来，这里只有两个文件，编译后是会报错的，因为对应的驱动还没有完成，主要是关于使用lvgl时在对应模块上要用到的初始化函数，比如LCD显示屏的显示初始化、CST816触摸屏的初始化
  - ![lvgl1](.\images\lvgl1.png)
  - ![lvgl2](.\images\lvgl2.png)
  - ![lvgl3](.\images\lvgl3.png)
- GUI_App：后面进行界面开发的时候文件存放地
- GUI_Font_img：字体文件
- Task：freertos实现多任务的文件存放地

如果配置成功，编译结果应该如下：

![oen](D:\develop\Demo\watch_lvgl and freertos\images\oen.png)

