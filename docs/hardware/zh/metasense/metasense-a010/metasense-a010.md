# MetaSense-A010

|     时间      | 负责人 |   更新内容   |
| :-----------: | :----: | :----------: |
| 2022年8月12日 | yuexin | 初次编写文档 |


## 产品概述

![a010-11](asstes/tof/../../assets/a010-11.jpg)
MetaSense-A010 是由 Sipeed 所推出的一款由 BL702 + 炬佑 100x100 TOF 所组成的极致性价比 3D 传感器模组，最大支持 100x100 的分辨率和 8 位精度，并且自带 240×135 像素的 LCD 显示屏来实时预览 color map 后的深度图 。

**资料汇总**

硬件规格书：
硬件接线图：

## 产品开箱指南

### 准备工作

这款设备采用串口协议对外提供接口和传输数据，物理接口对外提供了 type-c（虚拟串口）及 1.0mm 的 4pin 母座（UART）都能获取深度图数据用于集成。

**产品接线说明**
通过 type-c 接口连接电脑后，可以识别到 /dev/ttyUSBx（Linux）或COMx（Windows）。
![a010-12](asstes/../assets/a010.12.jpg)

还可通过 usb2ttl 模组按照硬件引脚图连接 4pin 母座。
硬件引脚图：屏幕正面朝下
<html>
  <img src="./asstes/../assets/a010-12.jpg" width=48%>
  <img src="./asstes/../assets/a010-5.jpg" width=48%>
</html>

<table>
    <tr>
        <td>MS-A010</td>
        <td>TX</td>
        <td>RX</td>
        <td>GND</td>
        <td>5V</td>
    </tr>
    <tr>
        <td>usb2ttl 模块</td>
        <td>RX</td>
        <td>TX</td>
        <td>GND</td>
        <td>5V</td>
    </tr>
</table>


**COMTOOL 软件包**
Windows 系统连接：下载压缩包后解压安装即可。
Linux 系统：不提供软件压缩包，需用户自行编译。
注意：Win 7 及以下系统需装驱动，可自行前往 FTDI 官网下载。

### 上电互动预览
将设备通电后，可在设备上自带 LCD 屏实时预览 color map 后的深度伪彩图
![010-3](assets/a010-3.jpg)

### PC 预览和微调
1. 打开 COMTOOL 软件后，如果软件上方无 Graph 的话，可在右上角 + 号处添加 Graph 。
2. 左侧选择 Port 和对应波特率（忽略 USB 串口，可任意选择高波特率），选择 MaixSenseLite 后点击 open 后再勾选下方 USB 即可接收大量数据。

![010-1](assets/a010-1.jpg)
3. 设定 Header 为 \x00\xFF 即可正确解析图像数据并观察到深度图，可以直观感受二维平面图像上的深度。
![010-2](assets/a010-2.jpg)


### 互动配置说明

COMTOOL 上位机的配置控件说明
- Header 设置识别包头
- RawCMD 用户可以手动发送指令（USB 和 UART 串口行为相同）
- ISP 启动停止
- LCD 显示自带屏的开启关闭
- USB 串口传输深度图开启关闭
- UART 串口传输深度图开启关闭
- ANTIMMI 自动抗多机干扰开启关闭（易受干扰，关闭的效果都要更好些）
- Binn 可下拉设置 BINNING，Baud 设置 UART 波特率
- X，Y 设置坐标，D左侧多选框开启关闭显示 ( x，y ) 处据该摄像头的距离，位于 D 右侧
- Unit 设置量化单位（16位量化到8位，比例缩小，设置太小会只能看清很近处的影像）
- FPS 设置出图帧率（不宜过高，根据对接设备的性能合理设置即可，减小帧率可以减少传输数据量）
- Ev 曝光间隙控制（最左代表 AE，其他是固定曝光时间）



## 案例：检测人流

高精度，大分辨率的实时监测人流走动的情况统计。

![a010-14](assets/a010-14.jpg)



## 案例：键盘灯跟随

实现超酷炫的键盘灯跟随，实时跟踪手部的位置，再根据手部的位置映射键盘灯。

![tof-1.9](./../assets/tof-1.9.jpg)


## 案例：接入 MCU 

MS-A010 拥有强大的兼容性，可基于串口协议外接 K210 bit 这样的单片机开发板或树莓派之类的 linux 开发板来进行二次开发。

![a010-13](asstes/../assets/a010-13.jpg)
获取链接：


## 二次开发：串口协议

可参考上方的案例：**K210 bit 外接 MCU**

| AT                             |                                                                                                                                                                                                                |
| ------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| +ISP<br>Image Signal Processor | :0: turn ISP off<br>=1: turn ISP on                                                                                                                                                                            |
| +BINN<br>full binning          | =1: output 100x100 pixel frame<br>=2: output 50x50 pixel frame<br>=4: output 25x25 pixel frame<br>                                                                                                             |
| +DISP<br>display mux           | =0: all off<br>=1: lcd display on<br>=2: usb display on<br>=3: lcd and usb display on<br>=4: uart display on<br>=5: lcd and uart display on<br>=6: usb and uart display on<br>=7: lcd, usb and uart display on |
| +BAUD<br>uart baudrate         | =0: 9600<br>=1: 57600<br>=2: 115200<br>=3: 230400<br>=4: 460800<br>=5: 921600<br>=6: 1000000<br>=7: 2000000<br>=8: 3000000                                                                                     |
| +UNIT<br>quantization unit     | =0: auto<br>=1-10: quantizated by unit(mm)                                                                                                                                                                     |
| +FPS<br>frame per second       | =1-19: set frame per second                                                                                                                                                                                    |
| +Save<br>save config           | : save current configuration                                 |




## 二次开发：接入 ROS
### 接入 ROS1 

**1. 准备工作** 
首先，准备适用的环境：Linux 系统
可使用虚拟机 `virtual box` 或者 `vmware` 也可安装双系统，安装方法请自行查询。

**2. 安装运行**
由于我们提供的是 ROS2 的接入功能包，运行 ROS1 的话只需切换分支即可。

```bash

#解压缩sipeed_tof_ms_a010.zip，并进入目录
git switch ros1 #切换到ros1分支
source /opt/ros/*/setup.sh
catkin_make
source devel/setup.sh
rosrun sipeed_tof_ms_a010 a010_publisher _device:="/dev/ttyUSB0"
#之后终端会持续刷新显示[sipeed_tof]: Publishing，即正常工作

```
**3. 可自行在 RQT 查看帧率**

**4. RVIZ2 预览**
打开 `rviz2` 后，在界面左下角的 `Add`->`By topic`->`PointCloud2或/depth` ->`Image 添加` ->`Display/Global Options/Fixed Frame` 需要修改成 `tof`，才能正常显示点云，根据添加的内容，左侧会显示 `Image` 而中间则显示点云。
![a010-8](asstes/../assets/a010-8.jpg)


### 接入 ROS2 

**1. 准备工作**
首先，准备适用的环境：Linux 系统
可使用虚拟机 `virtual box` 或者 `vmware` 也可安装双系统，安装方法请自行查询。

**2. 安装运行**
我们提供了 ROS2 的接入功能包，用户需要在运行 ROS2 的系统上编译安装。
接入包下载连接：

```bash
#解压缩 sipeed_tof_ms_a010.zip，并进入目录
source /opt/ros/*/setup.sh
colcon build #如提示缺少colcon时需要sudo apt install python3-colcon-ros
ros2 run sipeed_tof_ms_a010 publisher --ros-args -p device:="/dev/ttyUSB0"
source install/setup.sh
#之后终端会持续刷新显示[sipeed_tof]: Publishing，即正常工作。
```

**3. RQT 查看帧率**

![a010-9](asstes/../assets/a010-9.jpg)

**4. RVIZ2 预览**
打开 `rviz2` 后，在界面左下角的 `Add`->`By topic`->`PointCloud2或/depth` ->`Image 添加` ->`Display/Global Options/Fixed Frame` 需要修改成 `tof`，才能正常显示点云，根据添加的内容，左侧会显示 `Image` 而中间则显示点云。
![a010-10](assets/a010-10.jpg)

