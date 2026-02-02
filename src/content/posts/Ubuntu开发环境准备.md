---
title: 🧑‍💻​ Ubuntu开发环境准备
published: 2026-02-02
description: "从制作系统盘开始的安装Windows与Ubuntu双系统新手指引"
image: "https://aircheetah.dpdns.org/PicGo/Ubuntu.jpg"
tags: ["Ubuntu"]
category: Guides
draft: false
lang: 'zh-CN'
---

# 零. 前期环境准备

## 制作系统盘

硬件需要一个U盘和一台电脑，软件需要 **Ventoy**（制作启动U盘的开源工具）和所要制作的Ubuntu版本镜像。

### 镜像下载

统一下载 Ubuntu 22.04.5 LTS 版本：

[Ubuntu 22.04.5 LTS (Jammy Jellyfish)](https://releases.ubuntu.com/jammy/)

### Ventoy 工具

Ventoy 是一个制作启动U盘的开源工具，相比于软碟通来说使用 Ventoy 你的U盘不再局限于某个PE系统，你只需要把 ISO/IMG/EFI 等类型的文件拷贝到U盘里面就可以启动了，无需其他操作。你可以一次性拷贝很多个不同类型的镜像文件，Ventoy 会在启动时显示一个菜单来供你进行选择。

- **下载地址**：[Ventoy download](https://www.ventoy.net/cn/)
- **GitHub 仓库**：[A new bootable USB solution](https://github.com/ventoy/Ventoy)

### 制作步骤

1. **制作 Ventory 启动盘**

   双击运行 Ventoy2Disk，开始制作U盘（注：当你制作成功后，你的U盘名称会变成 Ventoy）

   ![ventoy1](https://aircheetah.dpdns.org/PicGo/ventoy1.png)
   ![ventoy2](https://aircheetah.dpdns.org/PicGo/ventoy2.png)
   ![ventoy3](https://aircheetah.dpdns.org/PicGo/ventoy3.png)

2. **拷贝系统镜像**

   将系统镜像拷贝到U盘中（注：**ISO 文件最好保存在U盘根目录下**）

   ![copy-iso](https://aircheetah.dpdns.org/PicGo/copy-iso.png)

3. **使用效果**

   将U盘插入服务器将启动项改成U盘启动，你就会发现出现如下界面，此时我们就可以正常安装系统（注：你也可以在U盘里面同时放多个操作系统）

   ![ventoy-menu1](https://aircheetah.dpdns.org/PicGo/ventoy-menu1.png)
   ![ventoy-menu2](https://aircheetah.dpdns.org/PicGo/ventoy-menu2.png)

### 注意事项

- 如果发现自己制作的启动盘，在BIOS界面不显示，可能是自己U盘的问题，如果要购买U盘，提前问好商家是否可以制作启动盘
- 制作系统盘时会将U盘格式化，所以要提前将U盘中的文件备份或者干脆使用一个新U盘
- U盘复写结束之前不能将U盘拔出，否则可能会造成U盘内的数据损坏
- 注意镜像文件的大小不要超过U盘的内存空间，最好预留出足够的空间以供日后使用，建议16G或32G

## 安装双系统

> 建议结合该教学视频观看：[如何安装和卸载Windows和Ubuntu双系统](https://b23.tv/aGB9J9I)

### 安装前准备

1. **关闭加密**

   关闭自己电脑上的设备加密和 BitLocker 加密，这个过程可能会花点时间，两分钟到两三个小时不等，请耐心等待

   ![bitlocker](https://aircheetah.dpdns.org/PicGo/bitlocker.jpg)

2. **预留磁盘空间**

   预留磁盘空间给 Ubuntu，第一次可以装150G试一下，后期重装自己看情况给（1GB = 1024MB，可以简化为1000来计算）

3. **压缩卷**

   点击磁盘管理，选择一个内存富裕的分区，右击 -> "压缩卷"，要注意内存在磁盘上需要是连续的，即最好只从一个盘腾地方，压缩完成之后，在你选择压缩的分区上的图形化显示会出现灰色条，即证明成功

   ![disk1](https://aircheetah.dpdns.org/PicGo/disk1.png)
   ![disk2](https://aircheetah.dpdns.org/PicGo/disk2.png)

4. **修改 BIOS 设置**

   每个品牌的电脑 BIOS 界面有所不同，进入 BIOS 的方式也不同，需要自行搜索。进入 BIOS 界面后：

   a. 禁用电脑的安全引导项：依次选择 Security -> security boot -> disable
   b. 开启U盘启动，保存设置并重启
   c. 插上做好的系统盘后重启，再次进入 BIOS 界面
   d. 在启动项中将U盘启动调整至第一位，保存设置并重启
   e. 如果上述步骤操作成功则会进入 Ventoy 的选择界面

### 安装 Ubuntu

1. 语言选择 English，后点击 Install Ubuntu

   ![step1](https://aircheetah.dpdns.org/PicGo/step1.jpg)

2. 键盘布局选择 Chinese 不变

   ![step2](https://aircheetah.dpdns.org/PicGo/step2.jpg)

3. **连接 wifi（十分必要）**

4. 选择最小安装、安装时不更新、安装无线模块及第三方库

   ![step4](https://aircheetah.dpdns.org/PicGo/step4.jpg)

5. 选择 Something else 自己分区，之后继续点 Continue

   ![step5](https://aircheetah.dpdns.org/PicGo/step5.jpg)

6. **磁盘分区**（选中提前空出来的 free space）

   - **/boot**：2G

     ![boot](https://aircheetah.dpdns.org/PicGo/boot.jpg)

   - **/swap**：2G（图中为16G，根据自己的情况，比如内存16G那么swap设置8G或16G；若你为32G，那么swap设置为2G）

     ![swap](https://aircheetah.dpdns.org/PicGo/swap.jpg)

   - **/home**：≥100G（图中为200G，根据自己情况，/home目录和/根目录中给/home多分一些，这就好比Windows中你的D盘和C盘）

     ![home](https://aircheetah.dpdns.org/PicGo/home.jpg)

   - **/**：≥50G（图中为100G，根据自己情况）

     ![root](https://aircheetah.dpdns.org/PicGo/root.jpg)

7. 分区完成，进行后续操作

   ![step7-1](https://aircheetah.dpdns.org/PicGo/step7-1.jpg)
   ![step7-2](https://aircheetah.dpdns.org/PicGo/step7-2.jpg)

8. 设置名称密码（名称要简短，可以使用自己姓名首字母小写）

   ![step8](https://aircheetah.dpdns.org/PicGo/step8.jpg)

9. 安装完成后等待重启，耐心等待提示（翻译：拔掉U盘后按 Enter 键）

### 安装后配置

1. **开机进入系统**

   开机后进入选择界面，选择第一个进入 Ubuntu

2. **连接 Wifi**

   ![wifi](https://aircheetah.dpdns.org/PicGo/wifi.png)

3. **系统换源**

   点击软件与更新，"下载自："，选择其他，选择阿里源，关闭后会出现重新载入，重新载入即可

   ![source1](https://aircheetah.dpdns.org/PicGo/source1.png)
   ![source2](https://aircheetah.dpdns.org/PicGo/source2.png)
   ![source3](https://aircheetah.dpdns.org/PicGo/source3.png)

   > **小鱼 ROS 一键换源（推荐）**
   >
   > 按下 `Ctrl+Alt+T` 打开终端，输入以下命令：
   >
   > ```bash
   > wget http://fishros.com/install -O fishros && . fishros
   > ```
   >
   > 输入密码赋予权限，选择"一键配置:系统源"，接下来按照脚本指示操作
   >
   > 相关教程：[小鱼一键安装系列](https://fishros.org.cn/forum/topic/20/%E5%B0%8F%E9%B1%BC%E7%9A%84%E4%B8%80%E9%94%AE%E5%AE%89%E8%A3%85%E7%B3%BB%E5%88%97)

4. **安装语言支持**

   进入语言支持安装完整的语言支持

   ![lang1](https://aircheetah.dpdns.org/PicGo/lang1.png)
   ![lang2](https://aircheetah.dpdns.org/PicGo/lang2.png)

5. **切换系统语言**

   再次进入后，打开设置切换语言为中文，换完会提示你登出，登出就行

   ![lang3](https://aircheetah.dpdns.org/PicGo/lang3.png)

6. **保留英文目录名**

   重新登入后选择 **保留旧名称**，并点击不要再次询问我

   ![english-name](https://aircheetah.dpdns.org/PicGo/english-name.png)

---

# 一. 主要流程概述

→ 搜狗拼音 → 双系统时间同步 → Typora → 星火应用商店（非必要，但推荐）

---

# 二. 搜狗拼音安装

- **官网**：[https://pinyin.sogou.com/linux](https://pinyin.sogou.com/linux)
- **官方教程**：[https://shurufa.sogou.com/linux/guide](https://shurufa.sogou.com/linux/guide)

下载 x86_64 版本（Ubuntu 22.04 系统也适用）

```bash
# 更新源
sudo apt update

# 安装 fcitx
sudo apt install fcitx
# 语言支持 -> 选择 fcitx

# 开机自启动
sudo cp /usr/share/applications/fcitx.desktop /etc/xdg/autostart/

# 卸载 ibus 框架
sudo apt purge ibus

# 安装搜狗拼音
sudo dpkg -i 安装包名

# 安装依赖
sudo apt install libqt5qml5 libqt5quick5 libqt5quickwidgets5 qml-module-qtquick2
sudo apt install libgsettings-qt1

# 重启电脑
reboot
```

---

# 三. 双系统时间同步

```bash
sudo apt install ntpdate
sudo ntpdate time.windows.com
sudo hwclock --localtime --systohc
```

---

# 四. Typora 安装

```bash
# add Typora's key
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://typoraio.cn/linux/typora.gpg | sudo tee /etc/apt/keyrings/typora.gpg > /dev/null

# add Typora's repository securely
echo "deb [signed-by=/etc/apt/keyrings/typora.gpg] https://typoraio.cn/linux ./" | sudo tee /etc/apt/sources.list.d/typora.list

# install typora
sudo apt update
sudo apt install typora
```

---

# 五. 星火应用商店安装

- **官网**：[星火应用商店](https://www.spark-app.store/download)

选择对应的计算机架构，一般都是 Intel/AMD 架构也就是 amd64，少部分人可能是 ARM。

在星火安装 QQ 和微信以及其他软件时可以和 Windows 平台一样便捷，主要用来安装 QQ 和微信等（方便文件传输），但不要依赖，学会命令行操作是基本技能。

```bash
cd ~/Downloads
sudo apt install ./spark-store-*.deb
```

---

# 六. VSCode 安装及环境配置

## VSCode 安装

1. 下载 .deb 后缀的软件包

   **官网**：[https://code.visualstudio.com/Download](https://code.visualstudio.com/Download)

   ![vscode-download](https://aircheetah.dpdns.org/PicGo/vscode.png)

2. 安装软件包

   打开下载所在文件夹，鼠标右键打开终端，执行以下命令：

   ```bash
   sudo apt install ./软件包名.deb
   ```

## 环境配置

与过渡阶段的 WSL + VSCode 的配置大体相同，可参考往期视频：

> **视频提示**：视频前半段为 Windows 环境下安装 WSL 与 VSCode，直接跳到 **7m10s** VSCode 配置部分

[WSL+VSCode 教程视频](https://aircheetah.dpdns.org/PicGo/WSL+VSCode.mp4)
