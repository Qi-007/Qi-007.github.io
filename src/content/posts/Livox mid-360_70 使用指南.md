---
title: ​🐱​ Livox mid-360/70 使用指南
published: 2026-01-02
description: "手把手教你在 Ubuntu 22.04 下把 Livox Mid-360 / Mid-70 接好、装好、跑起来Ꮚ･ꈊ･Ꮚ"
image: "https://aircheetah.dpdns.org/PicGo/lidar.png"
tags: ["lidar"]
category: Guides
draft: false
lang: 'zh-CN'
---
可以去Livox官网底下找激光雷达的用户手册来看看 👉 [Livox 官网](https://www.livoxtech.com/cn)

操作环境：

    1、Linux版本：Ubuntu 22.04
    
    2、激光雷达：mid-360、mid-70

## Mid-360 使用指南

### 1️⃣硬件连接

- 将mid360的一分三航空线的网线插口插入机载电脑的网口中，然后给mid360上电，注意Mid-360的工作电压为 9~27 V，推荐使用 12 V电压。

    ps: 队内的TB47S、48S电池输出电压为24v，需要一个出口为XT30的分电板和一块24v转12v的降压板

    *~~有自己找东西的功夫不如找个电控哥，专事找专人。~~*  
    <img src="https://aircheetah.dpdns.org/PicGo/radar-1.jpg" alt="radar-1" style="zoom:15%;" />

- 上电后修改网络设置

修改有线配置为手动，设置IP地址为**192.168.1.50**，子网掩码**255.255.255.0**，网关不用动默认为192.168.1.1

<img src="https://aircheetah.dpdns.org/PicGo/radar-7.png" alt="radar-7" style="zoom:50%;" />

<img src="https://aircheetah.dpdns.org/PicGo/radar-6.png" alt="radar-6" style="zoom:50%;" />

### 2️⃣安装 [Livox-SDK2](https://github.com/Livox-SDK/Livox-SDK2)

```shell
git clone https://github.com/Livox-SDK/Livox-SDK2.git
cd ./Livox-SDK2/
mkdir build
cd build
cmake .. && make -j
sudo make install
```

### 3️⃣安装 [livox_ros_driver2](https://github.com/Livox-SDK/livox_ros_driver2)

clone到 **[work_space]/src/** 路径下

```shell
cd src
git clone https://github.com/Livox-SDK/livox_ros_driver2.git
cd ./livox_ros_driver2/
source /opt/ros/melodic/setup.sh
./build.sh humble
```

这时候，由于运行了build.sh 脚本，会编译[work_space]整个目录下的功能包
等待编译完成即可

*注意：此时不能在[work_space]目录下直接用colcon build来编译*

### 4️⃣修改mid360的配置文件

进入[work_space]/src/livox_ros_driver2/config/**MID360_config.json**修改mid360的IP地址

- 将host_net_info中的ip全部修改为刚刚设置的主机ip **192.168.1.50**

- 将lidar_configs中的ip修改为：198.168.1.1xx，其中xx为雷达二维码SN码后两位

  > 就比如下边这个雷达的SN码后两位是32，就要将lidar_configs中的ip修改为198.168.1.132

  > SN码除了在雷达的包装盒上，雷达本身也贴着带有SN码的标签，不过我手里这个雷达的标签已经战损了，所以说再看见标签磨损的情况记得再找个标签贴上，不然别人就得一顿好找
  >
  > <img src="https://aircheetah.dpdns.org/PicGo/radar-12.jpg" alt="radar-12" style="zoom:15%;" />

<img src="https://aircheetah.dpdns.org/PicGo/radar-10.jpg" alt="radar-10" style="zoom: 10%;" />

<img src="https://aircheetah.dpdns.org/PicGo/radar-11.jpg" alt="radar-11" style="zoom:10%;" />

![radar-9](https://aircheetah.dpdns.org/PicGo/radar-9.png)

### 4️⃣启动👊🏻

[work_space]/src/livox_ros_driver2/launch_ROS2中有两个mid360会用到的启动文件：

![radar-13](https://aircheetah.dpdns.org/PicGo/radar-13.png)

- **rviz_MID360_launch.py**在rviz中显示激光雷达电云图像:

```shell
ros2 launch livox_ros_driver2 rviz_MID360_launch.py
```

![radar-8](https://aircheetah.dpdns.org/PicGo/radar-8.png)

- msg_MID360_launch.py 只发布话题
<img src="https://aircheetah.dpdns.org/PicGo/radar-14.png" alt="radar-14" style="zoom: 50%;" />

## Mid-70 使用指南

Mid-70和360的使用方式差不多，主要说一下各个方面二者之间的差别

### 1️⃣硬件连接

a. 连接 Livox Mid-70 与航插电源网口线，然后将航插电源网口线上的激光探测测距仪连接头插入电源转接插座 2.0 的激光探测测距仪连接头接口

b. 使用以太网线（自备），将电源转接插座 2.0 连接至个人电脑

c. 通过电源转接插座 2.0 的电源接口连接外部电源，电源转接插座的工作电压为9~30V，直接使用队内电池供24V电源即可

d. 网络设置同mid-360

<img src="https://aircheetah.dpdns.org/PicGo/radar-2.png" alt="radar-2" style="zoom: 50%;" />

### 2️⃣安装 [Livox-SDK](https://github.com/Livox-SDK/Livox-SDK)

```plain
git clone https://github.com/Livox-SDK/Livox-SDK.git
cd Livox-SDK
cd build && cmake ..
make
sudo make install
```

### 3️⃣安装[livox_ros2_driver](https://github.com/Livox-SDK/livox_ros2_driver)

注意是**livox_ros2_driver**而不是livox_ros_driver2，Livox 官方把 Tele-15 / Mid-70 / Avia / Horizon 归在 Livox SDK（SDK1） 这条线，而 SDK2 主要面向 HAP / Mid-360。

clone到 **[work_space]/src/** 路径下

```plain
cd src
git clone https://github.com/Livox-SDK/livox_ros_driver2.git
cd ..
colcon build --symlink-install
```

**注意：**

1. 若需要构建览沃 ROS2 驱动程序，务必将 "/usr/local/lib/" 目录下的 "livox_sdk_static.a" 文件删除或改名，再执行如上构建步骤。览沃 ROS2 驱动程序自身将编译 [Livox-SDK](https://github.com/Livox-SDK/Livox-SDK) 为动态库。

2. 如果有使用conda管理环境的话最好先退出，因为ROS2 Humble 的构建过程本来应该用系统/ROS 自带的 Python 环境（里面有各种所需要的依赖），但被环境变量影响后，改成用 conda 的 python 去跑，而你新建conda的python环境时一般不会安装ros的依赖

   *实在想用就编译一下看报错，缺哪个就pip install装哪个，不过不推荐（*

   <img src="https://aircheetah.dpdns.org/PicGo/radar-7.jpg" alt="radar-7" style="zoom:10%;" />

```plain
conda deactivate
# 如果有多层conda的话，要重复执行直到前缀不是 (base)
```

### 4️⃣启动👊🏻

[work_space]/src/livox_ros2_driver/livox_ros2_driver/launch中有三个mid70会用到的启动文件：

![radar-3](https://aircheetah.dpdns.org/PicGo/radar-3.png)

- **livox_lidar_rviz_launch.py** 可以在rviz中显示激光雷达电云图像

![radar-4](https://aircheetah.dpdns.org/PicGo/radar-4.png)

- **livox_lidar_msg_launch.py**和**livox_lidar_launch.py**的区别只有一处 **xfer_format**

![radar-5](https://aircheetah.dpdns.org/PicGo/radar-5.png)

livox_lidar_launch.py

👉 发布 标准 sensor_msgs/PointCloud2

livox_lidar_msg_launch.py  
👉 发布 Livox 自定义消息（Raw / Custom format）

驱动内部采集是一样的，只是最后一步“打包成什么 ROS 消息”不同。

这就看情况选择了，像fast livo中使用的就是livox的自定义点云数据格式，而并非常规的pointcloud2格式，就要用livox_lidar_msg_launch.py

### The END

mid360和mid70都有各自的上位机软件，Livox Viewer 2 和  Livox Viewer

> 这是mid-360的上位机软件Livox Viewer 2呈现的效果，也比较简单，感兴趣可以自己捣鼓一下
>
> <img src="https://aircheetah.dpdns.org/PicGo/radar-16.jpg" alt="radar-16" style="zoom:10%;" />

![radar-15](https://aircheetah.dpdns.org/PicGo/radar-15.png)

仙人指路😶‍🌫️👇

[mid-360相关资料下载](https://www.livoxtech.com/cn/mid-360/downloads)

[mid-70相关资料下载](https://www.livoxtech.com/cn/mid-70/downloads)

到这里，**Mid-360 和 Mid-70 在 Ubuntu 22.04 + ROS2 环境下的基本使用流程就算完整跑通了**：
 从上电、网络配置、SDK/驱动选择，到启动、话题发布和 RViz 显示，基本不会再被“卡在第一步”。

其实只记住几件事就够了：

- **Mid-360 👉 SDK2 + livox_ros_driver2**
- **Mid-70 👉 SDK1 + livox_ros2_driver（注意名字）**
- IP 一定要对，SN 后两位别抄错
- ROS2 编译时尽量别掺和 conda

Livox 的官方文档整体偏“工程说明书”风格，第一次看多少有点劝退 🤯，但**只要把「设备 – SDK – 驱动」这三件事对应清楚，其实并不复杂**。真遇到问题，多半也就卡在网络、库冲突或者启动文件这几个地方。

等你在 RViz 里看到第一帧点云的时候，前面折腾的那些网络、编译、依赖问题，基本都会一笔勾销 ✨
 **能看到点云，后面的事就都好说了**。后续如果要接 **FAST-LIO / FAST-LIVO / 自己的点云算法**，这篇指南已经算是一个可靠的起点了。

如果你在折腾过程中踩了新的坑，或者有更优雅的配置方式，**欢迎补充 / 交流 / 吐槽**。
 毕竟 ——

> *调通雷达的那一刻，世界都是有点云的。* ☁️
