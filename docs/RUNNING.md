# 环境、数据与运行

以下命令由使用者在项目根目录运行。本轮展示整理未执行训练、下载模型或设备推理。

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
