<p align="center">
  <img src="assets/logo.svg" width="130" alt="校园暴力识别 Logo">
</p>

<h1 align="center">🛡️ 校园暴力识别系统</h1>
<h3 align="center">Campus Violence Recognition</h3>

<p align="center">
  <strong>基于 MobileNetV2 的校园暴力 / 非暴力视频二分类项目</strong>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.9-3776AB?logo=python&logoColor=white" alt="Python 3.9">
  <img src="https://img.shields.io/badge/TensorFlow-2.10-FF6F00?logo=tensorflow&logoColor=white" alt="TensorFlow 2.10">
  <img src="https://img.shields.io/badge/Keras-2.10-D00000?logo=keras&logoColor=white" alt="Keras 2.10">
  <img src="https://img.shields.io/badge/MobileNetV2-Transfer%20Learning-2E8B57" alt="MobileNetV2">
  <img src="https://img.shields.io/badge/FFmpeg-Video%20Preprocessing-007808?logo=ffmpeg&logoColor=white" alt="FFmpeg">
  <img src="https://img.shields.io/badge/TFLite-FP16%20Export-6A5ACD" alt="TFLite FP16">
  <img src="https://img.shields.io/badge/License-MIT-181717" alt="MIT License">
</p>

<p align="center">
  <a href="#-项目简介">项目简介</a> ·
  <a href="#-快速开始">快速开始</a> ·
  <a href="#-实验结果">实验结果</a> ·
  <a href="#-预训练模型">预训练模型</a> ·
  <a href="#-license">License</a>
</p>

---

## ✨ 项目亮点

| 模块 | 说明 |
|---|---|
| 🎞️ 视频预处理 | 使用 FFmpeg 对原始视频进行抽帧与重新封装 |
| 🧠 深度学习模型 | 使用 MobileNetV2 迁移学习完成 Violence / NonViolence 二分类 |
| 📊 实验评估 | 输出 Accuracy、AUC、Precision、Recall、F1-score 和 Confusion Matrix |
| 🚀 推理部署 | 支持单视频预测，并导出 FP16 TFLite 模型 |
| 📦 模型分发 | 大模型文件通过 GitHub Releases 作为 Release Assets 发布 |

---

## 🧭 目录

- [✨ 项目亮点](#-项目亮点)
- [📌 项目简介](#-项目简介)
- [📁 仓库结构](#-仓库结构)
- [⚙️ 环境配置](#️-环境配置)
- [📦 数据集目录](#-数据集目录)
- [🎞️ 视频抽帧](#️-视频抽帧)
- [🔎 数据集检查](#-数据集检查)
- [🧠 模型训练](#-模型训练)
- [🚀 预训练模型](#-预训练模型)
- [🎬 单视频预测](#-单视频预测)
- [📊 实验结果](#-实验结果)
- [⚠️ 模型限制与使用声明](#️-模型限制与使用声明)
- [🗂️ Git 跟踪规则](#️-git-跟踪规则)
- [📄 License](#-license)

---

## 📌 项目简介

本项目面向校园场景下的视频暴力行为识别任务，使用 **MobileNetV2** 作为特征提取骨干网络，并在其后接入全局平均池化、全连接层、Dropout、BatchNorm 与 Sigmoid 输出层，实现 **Violence / NonViolence** 二分类。

整体流程：

```text
原始视频 → FFmpeg 抽帧 → 图像二分类训练 → 单视频推理 → TFLite 模型导出
```

核心功能：

- FFmpeg 自动查找与跨平台调用；
- 原始视频按指定 FPS 抽取图片帧；
- 基于 Keras `flow_from_directory` 的图像数据读取；
- MobileNetV2 迁移学习训练与部分层微调；
- 输出 Accuracy、AUC、Precision、Recall、F1-score 与 Confusion Matrix；
- 导出 `.h5` Keras 模型与 FP16 `.tflite` 部署模型；
- 支持单个视频的 Violence / NonViolence 推理。

---

## 📁 仓库结构

```text
campus-violence-recognition/
├─ assets/
│  └─ logo.svg
├─ src/
│  ├─ ffmpeg_utils.py
│  ├─ 01_fix_videos.py
│  ├─ 02_extract_frames.py
│  ├─ 03_check_dataset.py
│  ├─ 04_train_model.py
│  └─ 05_predict_video.py
├─ dataset/
├─ frames/
├─ fixed_videos/
├─ models/
├─ logs/
├─ results/
├─ scripts/
├─ docs/
├─ environment_tf210_py39.yml
├─ requirements_tf210.txt
├─ PROJECT_FILES_MANIFEST.txt
├─ README.md
├─ LICENSE
└─ .gitignore
```

---

## ⚙️ 环境配置

参考环境：

```text
Windows 10 / 11
Python 3.9
TensorFlow 2.10.x
Keras 2.10.x
FFmpeg
CUDA-capable GPU，可选
```

使用 Conda 创建环境：

```powershell
conda env create -f environment_tf210_py39.yml
conda activate tf210_py39
```

已有 Conda 环境的依赖更新命令：

```powershell
conda activate tf210_py39
conda install -c conda-forge ffmpeg scipy=1.9.3 -y
```

TensorFlow / GPU 检查命令：

```powershell
python -c "import tensorflow as tf; print(tf.__version__); print(tf.config.list_physical_devices('GPU'))"
```

FFmpeg 检查命令：

```powershell
ffmpeg -version
```

---

## 📦 数据集目录

原始视频数据集不进入 Git 跟踪，原因是文件体积较大且存在数据集再分发限制。目录结构如下：

```text
dataset/
├─ NonViolence/
│  ├─ NV_1.mp4
│  ├─ NV_2.mp4
│  └─ ...
└─ Violence/
   ├─ V_1.mp4
   ├─ V_2.mp4
   └─ ...
```

默认类别映射：

```text
{'NonViolence': 0, 'Violence': 1}
```

数据集来源：Kaggle Real Life Violence Situations Dataset。

---

## 🎞️ 视频抽帧

标准抽帧命令：

```powershell
python src/02_extract_frames.py --fps 1
```

覆盖已有抽帧结果：

```powershell
python src/02_extract_frames.py --fps 1 --overwrite
```

默认输出目录：

```text
frames/NonViolence
frames/Violence
```

视频重新封装后抽帧：

```powershell
python src/01_fix_videos.py --overwrite
python src/02_extract_frames.py --input fixed_videos --output frames --fps 1 --overwrite
```

---

## 🔎 数据集检查

```powershell
python src/03_check_dataset.py
```

输出示例：

```text
Found 8418 images belonging to 2 classes.
Found 2103 images belonging to 2 classes.
训练集图片数：8418
验证集图片数：2103
类别映射：{'NonViolence': 0, 'Violence': 1}
```

---

## 🧠 模型训练

默认训练命令：

```powershell
python src/04_train_model.py
```

低显存模式：

```powershell
python src/04_train_model.py --batch-train 1 --batch-val 4
```

仅训练冻结骨干网络阶段：

```powershell
python src/04_train_model.py --no-finetune
```

默认输出文件：

```text
models/best_model_frozen.h5
models/best_model_finetuned.h5
models/best_model_fp16.tflite
```

主要推理模型：

```text
models/best_model_finetuned.h5
```

---

## 🚀 预训练模型

现有远程模型资产通过 [GitHub Releases](https://github.com/Sh-Vacle/campus-violence-recognition/releases) 列出。当前有 v1.0.1 和 v1.0.0；两个资产包均为 42,301,599 字节。本次只核对公开元数据，没有下载或验证包内容，模型/训练数据的再分发权限仍待核对。

Release 信息：

```text
Current listed tag: v1.0.1
Asset: campus_violence_models_v1.0.1.zip
Older tag: v1.0.0
Asset: campus_violence_models_v1.0.zip
```

Release Asset 内容：

```text
models/
├─ best_model_finetuned.h5
├─ best_model_fp16.tflite
└─ best_model_frozen.h5
```

模型包解压到项目根目录后，`models/` 目录即可用于单视频推理。

---

## 🎬 单视频预测

暴力视频示例：

```powershell
python src/05_predict_video.py --video ".\dataset\Violence\V_100.mp4" --model ".\models\best_model_finetuned.h5" --fps 1
```

非暴力视频示例：

```powershell
python src/05_predict_video.py --video ".\dataset\NonViolence\NV_100.mp4" --model ".\models\best_model_finetuned.h5" --fps 1
```

输出示例：

```text
========== 预测结果 ==========
抽帧数量：5
平均暴力概率：0.8291
最高暴力概率：0.9180
最低暴力概率：0.7448
预测类别：Violence
```

---

## 📊 历史帧级验证结果

验证集规模：

```text
Train images: 8418
Validation images: 2103
```

历史本地帧级验证指标（本次整理未重跑）：

```text
Accuracy: 0.9163
AUC: 0.9653
Precision: 0.9356
Recall: 0.8994
F1-score: 0.9171
```

混淆矩阵：

```text
[[953, 67],
 [109, 974]]
```

预测示例：

```text
V_100.mp4     avg_prob=0.8291    prediction=Violence
V_1000.mp4    avg_prob=0.8426    prediction=Violence
V_101.mp4     avg_prob=0.9385    prediction=Violence
NV_100.mp4    avg_prob=0.0940    prediction=NonViolence
NV_1000.mp4   avg_prob=0.0694    prediction=NonViolence
```

---

## 评估单位与已实现范围

8,418/2,103是图像帧数量，不是视频数量。`src/04_train_model.py`直接对抽帧目录使用ImageDataGenerator的validation_split=0.2，未见按视频ID/拍摄场景分组的独立测试集；同一视频相关帧可能跨划分。上述结果不能当成独立视频测试、真实校园场景泛化率或平台正式评测。

视频预测是帧概率平均，不是时序动作网络。代码实现了FFmpeg预处理、帧分类、视频概率聚合及FP16 TFLite导出；没有验证OrangePi板端推理性能，也没有找到PPT中的四路1080P、知识蒸馏、SQLite事件库、三级预警或72小时连续运行的对应实现/验收。方案素材不进入完成成果清单。

更多证据边界见 [评估说明](docs/EVALUATION.md)，第三方资料与共同贡献见 [来源说明](docs/SOURCES_AND_PERMISSIONS.md)。

## ⚠️ 模型限制与使用声明

本项目基于视频抽帧后的图像帧进行 Violence / NonViolence 二分类识别，视频级预测结果由帧级概率聚合得到。模型性能可能受到画面清晰度、摄像角度、动作发生时间、遮挡、场景差异和数据分布差异等因素影响。

本项目仅用于学习、实验和安全场景研究，不应用于未经人工复核的自动化处罚、身份判断或其他高风险决策。

---

## 🗂️ Git 跟踪规则

`.gitignore` 保留代码、说明文档、结果记录和目录占位文件，同时排除数据集、抽帧结果、训练日志与模型大文件。

```gitignore
dataset/*
frames/*
fixed_videos/*
logs/*
models/*
```

`dataset/`、`frames/`、`fixed_videos/`、`models/` 和 `logs/` 在当前文件树中通过 README 保留结构；本次核查未见 `.gitkeep` 文件。

---

## 📄 License

保留远程原有 [MIT LICENSE](LICENSE)，本次没有改变许可证或据此重新授权任何第三方内容。团队贡献、参考代码及模型/数据权利仍需项目拥有者核对。

数据集和预训练模型文件不包含在 Git 仓库中，相关使用需遵守其各自来源和发布平台的许可要求。
