---
title: ♿ ​深度学习初识：Ultralytics YOLO
published: 2026-01-02
description: "以 Ultralytics YOLO 为例，带你快速跑通一次从数据到训练、再到部署的完整深度学习入门流程 😊"
image: "https://aircheetah.dpdns.org/PicGo/YOLO.png"
tags: ["YOLO", "DeepLearning"]
category: Guides
draft: false
lang: 'zh-CN'
---

多说无益，先直接上手用 Ultralytics YOLO（YOLO11 / YOLOv8）训练自己的模型，对深度学习的整体工程流程有一定概念后再去看教学视频和书籍会更好，这样在书中令人头昏的专业术语会变成让你真正摸不着头脑的实际问题，比如：

❓ 为什么我 mAP 上不去？

❓ 是数据问题还是模型容量问题？

❓ 输入 640 和 1280 差多少？

❓ batch 开多了为什么显存炸？

❓ 同一个模型为啥部署后精度掉？

也能让你明白为什么**训练 ≠ 推理**，**模型效果 ≠ 系统效果**，**帧率 / 延迟 / 精度三者的权衡**那么难找

<img src="https://aircheetah.dpdns.org/PicGo/tired.jpg" alt="tired" style="zoom:10%;" />

## Step 1：明确任务与指标

在动手前一定要想清楚：

**你需要回答的问题**

- 是 目标检测 / 分割 / 姿态 / 分类？

- 实时性要求？

- 部署平台是 PC GPU / Jetson / CPU？
  - x86和arm架构在部署推理上有所区别，这与他们各自的指令集和硬件加速生态不同有关
  - 不知道x86和arm架构间的区别? 🧐👉 [CPU是咋工作的？详解x86 vs ARM](https://b23.tv/J27nFCW)

- 精度要求？
  - 看你侧重的是**有没有检测出来**还是**不仅要检测到，还要框得很准**

## Step 2：数据采集

**原则**

- 数据分布 > 数据量

- 一定要覆盖：

  - 不同光照

  - 不同距离

  - 不同角度

  - 模糊 / 遮挡 / 噪声

**建议数量（检测）**

- 最低可用：300～500 张

- 稍微靠谱：1000～3000 张

- 工程可用：5000+

📌 数据比模型重要 10 倍

## Step 3：数据标注

常用工具

- [MakeSence](https://makesense.bimant.com/)  (我比较喜欢这个)

- LabelImg（最常见）

- CVAT（多人协作）

- Roboflow（在线）

怎么标注，以MakeSence为例 👉 [makesence标注车辆的讲解视频](https://b23.tv/Y3hs5XD)

YOLO 检测标注格式

```
class_id x_center y_center width height
```

全部是**归一化**到 [0,1]

📌 注意：

- 类别 ID 从 0 开始

- 坐标是 中心点，不是左上角

 数据集有不同格式，例如VOC、COCO，拿到不同格式的数据集也不要慌，可以使用python脚本将数据集转换成所需要的格式（大部分）

## Step 4：数据集组织（YOLO 标准）

数据集里分为训练集、验证集、测试集，三者各自干什么<img src="https://aircheetah.dpdns.org/PicGo/think.jpg" alt="think" style="zoom:10%;" />

先用一个不恰当的比喻来说明3种数据集之间的关系：

- 训练集相当于上课学知识
- 验证集相当于课后的的练习题，用来纠正和强化学到的知识
- 测试集相当于期末考试，用来最终评估学习效果

1️⃣ 训练集（Train）

- 用来 **更新模型参数**

- 反向传播、梯度下降

- 数据增强主要用在这里

📌 占比最大

2️⃣ 验证集（Val）

- 不参与训练

- 用来：
  - 评估模型效果，为了调整超参数而服务
  - 调整超参数，使得模型在验证集上的效果最好
  - 验证集不像训练集和测试集，它是非必需的。如果不需要调整超参数，就可以不使用验证集，直接用测试集来评估效果。
  - 验证集评估出来的效果并非模型的最终效果，主要是用来调整超参数的，模型最终效果以测试集的评估结果为准。

📌 非必须

3️⃣ 测试集（Test）

- 完全隔离

- 通过测试集的评估，我们会得到一些最终的评估指标，例如：准确率、精确率、召回率、F1等。

📌 不准参与任何决策

**推荐目录结构**

```shell
dataset/
├── images/
│   ├── train/
│   ├── val/
│   └── test/
├── labels/
│   ├── train/
│   ├── val/
│   └── test/
└── data.yaml // 它是“数据集说明书”，告诉训练程序：数据在哪、有什么类别、一共多少类。
```

**data.yaml 示例：**

```yaml
path: dataset
train: images/train
val: images/val
test: images/test

nc: 3
names: ['red', 'blue', 'green']
```

### ✅ 划分推荐

| 数据量         | Train | Val | Test |
| -------------- | ----- | --- | ---- |
| 很小（<1k）    | 70%   | 20% | 10%  |
| 中等（1k–10k） | 80%   | 10% | 10%  |
| 大（>10k）     | 90%   | 5%  | 5%   |

📌 **数据越少，验证集比例越高**

划分集合的注意事项：

1. 测试集永远最后用
2. 调参只看验证集
3. 同源数据不跨集合
4. 数据越少，越要谨慎切
5. 超参数越少，或者超参数很容易调整，那么可以减少验证集的比例，更多的分配给训练集。

## Step 5：环境配置

### 1️⃣ 首先安装miniconda管理python环境

指路🧑‍🦽‍➡️👉 [MiniConda安装教程](https://blog.csdn.net/weixin_43651674/article/details/134880766)

*miniconda是一个 “轻量版的 Conda 环境管理器 ”，用来隔离 Python 版本和依赖，避免环境冲突。*

*如果你在 Ubuntu / 深度学习 / ROS / 多项目并行 这些场景下工作（你基本都在这些环境里），Miniconda 几乎是必备工具。*

创建并激活py310环境

```shell
conda create -n yolo python=3.10 -y
conda activate yolo
```

### 2️⃣ 安装 Ultralytics

```shell
pip install ultralytics
```

### 3️⃣ 验证

```shell
yolo version
```

## Step 6：选择模型

**模型大小选择建议**

| 模型      | 使用场景                    |
| --------- | --------------------------- |
| yolo11n   | 极限实时 / 嵌入式           |
| yolo11s   | 实时 + 稍高精度（推荐起步） |
| yolo11m   | 精度优先                    |
| yolo11l/x | 服务器                      |

📌 第一次训练：直接用 yolo11s.pt

## Step 7：开始训练（核心）

**命令行方式**

```
yolo train \
  model=yolo11s.pt \
  data=dataset/data.yaml \
  epochs=100 \
  imgsz=640 \
  batch=16 \
  device=0
```

注意路径问题

<img src="https://aircheetah.dpdns.org/PicGo/yolo-2.png" alt="yolo-2" style="zoom:50%;" />

**关键参数解释**

- `epochs`：不够就欠拟合，过多可能过拟合
- `imgsz`：部署尺寸一致！
- `batch`：越大越稳定（显存限制）
- `device=0`：GPU (提前安装好显卡驱动)

**炼丹中**...   <img src="https://aircheetah.dpdns.org/PicGo/yolo-7.jpg" alt="yolo-6" style="zoom:10%;" />

<img src="https://aircheetah.dpdns.org/PicGo/yolo-1.png" alt="yolo-1" style="zoom:50%;" />

**训练完成🎉**

<img src="https://aircheetah.dpdns.org/PicGo/yolo-3.png" alt="yolo-3" style="zoom:50%;" />

📌 输出在：

```
runs/detect/train/
```

其中包含：

```
runs/detect/train/
├── weights/    ← 模型权重（最重要）
    ├── best.pt   ← 真正拿去部署的模型🌟
    └── last.pt   ← 最后一个 epoch 的模型            
├── args.yaml               ← 本次训练的全部参数记录
├── results.png / csv       ← 训练过程指标
├── *curve.png              ← P/R/F1 曲线
├── confusion_matrix*.png   ← 混淆矩阵
├── train_batch*.jpg        ← 训练时可视化
├── val_batch*_labels.jpg   ← 验证集 GT
├── val_batch*_pred.jpg     ← 验证集预测结果
└── labels.jpg              ← 类别统计
```

<img src="https://aircheetah.dpdns.org/PicGo/yolo-4.png" alt="yolo-4" style="zoom: 50%;" />

## Step 8：看训练结果

你应该关注：

- `loss`（损失函数） 是否下降
- train / val 是否分叉（过拟合）
- PR 曲线
- 混淆矩阵

别只看 mAP，因为也会出现mAP 高但实际效果差，这样的话有可能数据有问题

## Step 9：推理测试（真实感知）

**图片**

```shell
yolo predict model=runs/detect/train/weights/best.pt source=test.jpg
```

效果展示：

<img src="https://aircheetah.dpdns.org/PicGo/yolo-5.png" alt="yolo-5" style="zoom:50%;" />

/runs/detect/predict是 YOLO 推理的输出目录，里边有检测结果的图片 / 视频

<img src="https://aircheetah.dpdns.org/PicGo/yolo-6.png" alt="yolo-6" style="zoom:50%;" />

**视频 / 摄像头**

```shell
yolo predict model=best.pt source=0
```

**Python**

```python
from ultralytics import YOLO

model = YOLO("best.pt")
results = model("test.jpg", conf=0.25)
```

## Step 10：模型导出

​ 因为.pt 是 PyTorch 的“私有格式”，其中包含了PyTorch 计算图、Python 代码依赖、GPU / CUDA 绑定，导致它只能在Python、PyTorch的环境中运行，但我们的部署环境显然不止这么简单，这时候就需要一个并不关心各种依赖而是只关心“这个网络在做什么”的中转格式，ONNX自然就被引出了

​ 详细的可以看这篇文章 👉 [ONNX 模型格式分析与使用](https://blog.csdn.net/yitiaoxiaolu/article/details/136355292)

**导出** ONNX

```shell
yolo export model=best.pt format=onnx imgsz=640
```

除了ONNX也有其他的可选格式，可以去了解这里不细说了就：

- engine（TensorRT）
- openvino
- ncnn

📌 **导出尺寸必须和部署一致**

**先验证 ONNX 是否“正确”**

```
yolo predict model=best.onnx source=test.jpg
```

👉 结果应当和 .pt 几乎一致

📌 如果这里就不一致：

- 导出有问题

- opset 不对

- 动态 shape 出错

## Step 11：部署

此时你需要：

- 自己写预处理
- 自己写后处理（NMS / decode）
- 控制输入尺寸、batch

## The END

第一次用 Ultralytics YOLO 跑通完整流程时，很多人都会有一种感觉：

> “原来深度学习不是玄学，是工程。” <img src="https://aircheetah.dpdns.org/PicGo/DL.jpg" alt="DL" style="zoom:10%;" />

从数据采集和标注开始，到一行命令启动训练；
从 **.pt** 的模型效果，到 ONNX、TensorRT 的部署落地；
你会慢慢意识到——
**模型只是中间的一环，而不是终点。**

YOLO 帮你把门槛降得足够低 🧱
但真正的提升，来自你一次次踩坑、复盘和优化的过程。

你会发现：

- 精度不行，往往不是网络不行，而是数据不行 📦

- 推理慢，通常不是模型太大，而是部署方式不对 ⚙️

- 能训练出来，和能稳定跑在设备上，是两回事 💡

当你开始关心：

- 输入尺寸该不该改？

- batch 能不能再大一点？

- ONNX 和 .pt 为什么对不齐？

恭喜你 🎉
你已经不再是“跑模型的人”，而是在**做深度学习工程**了。

Ultralytics YOLO 不是终点，而是一个**非常好的起点** 🌱
从这里出发，你可以继续拆模型、改结构、做加速、搞部署——
一步步把“深度学习”变成**可控、可复现、可落地**的工程能力。

最后一句送给刚入门的你：

> 别怕模型看不懂，
> 真正的理解，永远来自自己跑通的那一遍。 😎🔥

<img src="https://aircheetah.dpdns.org/PicGo/yolo-8.jpg" alt="yolo-8" style="zoom:15%;" />
