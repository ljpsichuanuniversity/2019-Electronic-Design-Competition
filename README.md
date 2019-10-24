# 纸张计数显示装置 :smile:

<p align="center">

[电赛论文📚](/docs/论文/纸张计数显示器论文.md) |
[软件说明💻](/software/README.md) |
[机械说明🎮](/mechanical structure/README.md)
</p>

---
<p align="center">
![电赛LOGO](/media/6ac5361fa9b6c8eeee5dd25e7f2a6793.jpg "电赛LOGO")
</p>

<p align="center">
  <a href="https://zh.wikipedia.org/wiki/C%E8%AF%AD%E8%A8%80"><img src="https://img.shields.io/badge/language-C-brigreen.svg?style=flat-square" alt="C"></a>
  <a href="https://img.shields.io"><img src="https://img.shields.io/github/repo-size/zengwangfa/2019-Electronic-Design-Competition?style=flat-square" alt="Size"></a>
</p>

## 1、简介
>纸张计数显示装置基于**RT-Thread**实时操作系统，硬件平台采用STM32F407单片机为主控制器，以具有抗电磁干扰（**EMI**）架构的FDC2214模块作为电容采集传感器，通过**屏蔽双绞线**连接至两铜极板，读取采集的数据并进行相应判断，应用触摸屏和语音模块进行状态显示与播报。FDC2214模块将采集到的数据通过IIC协议传输给主控制器，主控制器对原始数据进行**卡尔曼滤波**，抑制噪声对数据采集的影响。校准模式下，本系统利用最大隶属度法，确定电容模拟值及纸张数的论域，定义模糊子集和隶属函数，建立模糊规则控制表，求得模糊控制查询表。其根据采集到的两极板的数据，做出短路判断并读取多组实时数据，将其与模拟区间进行归类，选取**最大可能性区间**作为期望值，减小最终判断的差错率。


|  | 主控制器(STM32) |
| :--: | :--: | :--: |
|芯片型号| STM32F407ZGT6 | 
|CPU| 32位 Cortex-M4(ARMv7) | 
|主频| 168MHz | 
|RAM| 192KB | 
|外存| 1MB(FLASH) | 
|单元| FPU、DSP |

---
|  | 主传感器(STM32) |
| :--: | :--: | :--: |
|芯片型号| FCD2214 | 
|分辨率| 28位 | 
|通道数| 4 | 
|接口| I2C | 
|最高输出速率| 4.08ksps |  

- 系统结构简图：
![系统结构简图](/media/9f95c192bfd92310ecad47e2f9367f9c.jpg "系统结构简图")


## 2、目录说明
| 目录名 | 描述  |
| :---: | :---: |
| 2019电赛题目 | 2019年电赛A-H题题目及器件清单 |
| docs | 数据手册、设计图片、参考文献及设计文档 |
| hardware | FDC2214、核心板原理图及PCB |
| hmi | 串口屏幕设计UI |
| matlab | matlab函数拟合及Kalman  |
| mechanical structure | 机械结构设计 |
| media | 设计图片 |
| openMV | openMV设计程序 |
| software | 纸张计数显示装置STM32程序 |
| software for development board | 纸张计数显示装置精简版 |
| software for rail gun | 电磁炮测试程序 |
| 综合测评 | 综合测评题目及Multisim电路仿真 | 

## 3、机械结构
- 3维模型
![3D](/media/e90a8f38fdda2e8b7624d84bee78ab5a.jpg "3D")


## 4、软件结构
![Program Flow](/media/9f95c192bfd92310ecad47e2f9367f9c.jpg "Program Flow")



## 5、硬件结构
![Controller PCB 3D](/docs/pictures/Controller_3D.jpg "Controller 3D")

- 核心板(Core Controller)拥有外设：

| 外设名称 | RGB LED | Key | Buzzer | Dial Switch | MS5837 | OLED | W25Q128 | Voltage Detection | Current Detection | Zigbee |  JY901 | USR-C216 | CP2102 | OV2640 Camera | Servo Motor | Searchlights | Perpellers |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 控制(通讯)方式 | GPIO | GPIO | GPIO | GPIO | I2C | SPI | SPI | ADC | ADC | USART | USART | USART  | USART | DCMI-DMA | PWM | PWM | PWM |
| 功能描述 | 指示灯 | 按键 | 蜂鸣器 | 拨码开关 | 深度传感器 | 显示屏 | 闪存芯片 | 电压检测 | 电流检测 | 2.4G无线通信 | 九轴 | WiFi模块 | 串口转USB | 摄像头 | 舵机  | 探照灯 | ESC |

![Hardware PCB 3D](/docs/pictures/hardware.jpg "Hardware PCB 3D")

---

## 6、测试数据分析

(1)
保持测试环境不变，采集50个样本，通过MATLAB拟合出纸张页数与传感器原始数据的曲线关系，如图4-2所示。

![](media/341a3f3eb5fd3f1a7a8d4b5c0d028951.png)

**图4-2 纸张页数与模拟量的拟合函数关系图**

(2) 根据(1)中采集的50个样本，确定电容模拟值及纸张数的论域，划分模糊子集，如表4-1
所示。

**表4-1页数与模拟量区间对应表**

| 页数/张 | 对应模拟量区间    | 页数/张 | 对应模拟量区间  |
|---------|-------------------|---------|-----------------|
| 1       | [1680.00,1345.34] | 6       | [682.48,633.84] |
| 2       | [1345.34,1013.26] | 7       | [633.84,598.03] |
| 3       | [1013.26,845.12]  | 8       | [598.03,569.46] |
| 4       | [845.12,748.79]   | 9       | [569.46,546.28] |
| 5       | [748.79,682.48]   | 10      | [546.28,529.16] |

(3)
根据（2）中划分的模糊子集，保持测试环境不变，固定测量35张纸张，采集数据如表4-2所示，采集到的原始数据90%落入35张页数所对应的区间[401.35,399.50]，如图4-3所示。

![](media/c45d8d729b636aa9712018bf969c4e80.png)

**表4-2 纸张数为35的实时数据**

| 测试组别 | 原始数据 | 测试组别 | 原始数据 |
|----------|----------|----------|----------|
| 1        | 400.896  | 6        | 400.824  |
| 2        | 401.049  | 7        | 400.591  |
| 3        | 400.157  | 8        | 401.111  |
| 4        | 399.912  | 9        | 400.443  |
| 5        | 401.125  | 10       | 398.529  |

>   **图4-3 纸张数为35的分布区间图**

(4)
根据（2）中划分的模糊子集，保持测试环境不变，固定测量46张纸张，采集数据如表4-3所示，模拟值大部分落入46张页数所对应的区间[383.6,382.7]，如图4-4所示。

![](media/7b244171ee345f202c05662f140550c5.png)

**表4-3纸张数为46的实时数据**

| 测试组别 | 原始数据 | 测试组别 | 原始数据 |
|----------|----------|----------|----------|
| 1        | 383.1243 | 6        | 385.6523 |
| 2        | 383.7841 | 7        | 382.9651 |
| 3        | 384.1236 | 8        | 382.2015 |
| 4        | 384.3164 | 9        | 383.7621 |
| 5        | 383.9984 | 10       | 384.1343 |

**图4-4 纸张数为46的分布区间图**

### 4.3.2测试结果

**表4-4 工作模式下纸张测试结果表**

| 范围   | 测试次数 | 正确次数 | 正确率 |
|--------|----------|----------|--------|
| 1\~10  | 25       | 25       | 100%   |
| 11\~20 | 25       | 25       | 100%   |
| 20\~30 | 25       | 25       | 100%   |
| 30\~40 | 25       | 25       | 100%   |
| 40\~50 | 25       | 25       | 100%   |
| 50\~60 | 25       | 23       | 92%    |
| 60\~70 | 25       | 20       | 80%    |
| 70\~80 | 25       | 16       | 64%    |

## 7、分工
- [X] 电路设计
    - [X] 硬件搭建 by [@Ian](https://github.com/zengwangfa)	

	
- [X] 控制程序
    - [X] 整体框架 by [@Ian](https://github.com/zengwangfa)
	- [X] 核心分割算法 by [@Okurarisona](https://github.com/Okurarisona)

- [X] 机械结构
	- [X] 结构方案设计 by [@JackyLin](https://github.com/JackyLin1205) 
	- [X] 固定铰链式抗干扰结构 by [@JackyLin](https://github.com/JackyLin1205) 

- [X] 论文
	- [X] 论文 by [@Hyf338](https://github.com/Hyf338)




#### 整理
- [点击联系我](Mailto:zengwangfa@outlook.com)




