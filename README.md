# DengFOC 极简FOC库 -- 开源


# 课程教案 和 库文档,请访问：dengfoc.com

## 1 概述

DengFOC 库是灯哥开源的，基于 Arduino 和 ESP32 硬件开发的开源 FOC 库。

![mainTitle](pic/mainTitle.png)

本FOC库参考了 SimpleFOC 的部分设计，但更秉承极简主义和实用主意，拥有比SimpleFOC更简易但更丰富的二次开发和应用特性：

1. **资源占用低**：内存占用比SImpleFOC少2/3
2. **开放度更广**：库把基本的FOC算法接口（如电角度、Iaplha 、IBeta）等以一种及其简单的方式向用户开放，使得用户可以直接接触到 FOC 算法实现的全过程，易于学习和在此基础上更进一步的开发 FOC 算法。
3. **外接能力强**：支持与OpenMV、树莓派、Python等多种硬件方案和软件语言直接的对接和互相调用，可以以比SimpleFOC更快的方式完成无刷电机应用的开发。
4. **即插即用，无需校准**：先进的参数自识别功能可以使得用户无需配置任何参数，直接插入电机和编码器即可跑FOC
5. **无线控制支持**：高速UDP，ESPNow 通讯，无需信号线即可控制电机
6. **脚本支持**：库内建强大的脚本语言 Lua，可以在不编译的情况下快速建立FOC应用
7. **强大的工具链支持**：支持与 Matlab 、Simulink 、ROS 等系统直接通讯，秒速建立机器人应用
8. **高精度减速器应用支持**：支持双编码器减速机应用

## 2 重要链接

本 FOC 库与 DengFOC 硬件联合组成一整套完整可用的 FOC 电机驱动方案，资料链接：

1 [灯哥开源 淘宝店--一站配齐DengFOC板](https://shop564514875.taobao.com/) 您的支持就是我们持续做开源内容和课程的动力，项目收益将用于后续开发DengFOC和做课程~

2 [DengFOC硬件 Github](https://github.com/ToanTech/Deng-s-foc-controller)

3 [DengFOC官网](http://dengfoc.com/#/) 包含课程文字版讲义，DengFOC使用文档，库使用方法等。

## 2 社区

本FOC板子社区唯三Q群，欢迎加入：**开源FOC无刷驱动交流群 灯哥开源 群号 778255240（1群） 735755513（2群）471832283（3群）**

任何使用问题和 DIY 问题 都会在这里做直接的讨论解答

## 3 怎样写 DengFOC 代码

灯哥在设计DengFOC库的时候，秉承极简和低内存占用的设计思路。因此，所有的功能块都是走**即用即引入，不用不引入**的原则，以此获得极低的资源占用率。

目前库已经有的模块有：

1. DengFOC 主库 -- 包含DengFOC基础运行函数
2. AS5600 编码器库  -- 传感器库
3. InlineCurrent 库 -- INA240 电流传感器库

目前已实现的功能：

1. 双路开环速度
2. 双路AS5600传感器读取
3. 双路在线电流检测
4. 双路力位闭环（基于电压Uq）
5. 双路力位闭环（基于电流Iq）

下面用力+位置闭环代码（力位闭环，力作为内环，位置作为外环），来示范 DengFOC 库的代码编写，更多有关于库函数的说明，请查看[DengFOC官网](dengfoc.com) 。

### 3.1 双电机力位闭环（基于Voltage）

```c++
#include "DengFOC.h"        //库导入

int Sensor_DIR=-1;          //传感器方向设置
int Motor_PP=7;             //电机极对数

void setup() {
  Serial.begin(115200);
  DFOC_Vbus(12.6);         //设定驱动器供电电压为12.6V
  DFOC_alignSensor(Motor_PP,Sensor_DIR);     //校准传感器
}

void loop() 
{
  //输出角度值
  //Serial.print("当前角度：");
  //Serial.println(DFOC_M0_Angle());
  //输出角度值
  float Kp=0.133;    //设置力位闭环Kp值
  float Sensor_Angle=DFOC_M0_Angle();
  setTorque(Kp*(serial_motor_target()-Sensor_DIR*Sensor_Angle)*180/PI,_electricalAngle());   //位置闭环
  serialReceiveUserCommand();
}

```

## 4 免费手把手教写FOC算法原理课

为了方便大家更进一步的理解FOC的算法和原理，我做了手把手教些FOC算法原理课，通过这些原理课你能够快速的从原理角度理解FOC知识并尝试写出自己的FOC基本功能库，视频链接：

1 [【手把手教写FOC算法】1_起源，无刷电机概念与控制原理](https://www.bilibili.com/video/BV1dy4y1X7yx)

2 [【手把手教写FOC算法】2_克拉克变换，建立简化电机数学模型](https://www.bilibili.com/video/BV1x84y1V76u/)

3 [【手把手教写FOC算法】3_等幅值变换与克拉克逆变换](https://www.bilibili.com/video/BV13s4y1Z7Tg/)

4 [【手把手教写FOC算法】4_帕克变换](https://www.bilibili.com/video/BV1t24y1u7do/)

5a [【手把手教写FOC算法】5a_撰写开环速度代码的前置知识](https://www.bilibili.com/video/BV1Pc411s7mP/)

5b [【手把手教写FOC算法】5b_开环速度代码编程+硬件调试教学](https://www.bilibili.com/video/BV16X4y167XZ/)

6a [【手把手教写FOC算法】6a_撰写闭环位置代码的前置知识](https://www.bilibili.com/video/BV1Rm4y1871K/)

6b [【手把手教写FOC算法】6b_闭环位置代码编程+硬件调试教学](https://www.bilibili.com/video/BV1yh4y1J7Xx/)

