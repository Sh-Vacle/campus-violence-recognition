<p align="center"><img src="assets/logo.svg" width="184" alt="紫青视频取景框Logo"></p>

<h1 align="center">🛡️ 校园暴力视频识别</h1>
<p align="center">Campus Violence Recognition · MobileNetV2 帧分类与视频概率聚合</p>

<p align="center"><img src="assets/badges/tech-0.svg" alt="Python 3.9"> <img src="assets/badges/tech-1.svg" alt="TensorFlow 2.10.1"> <img src="assets/badges/tech-2.svg" alt="MobileNetV2迁移学习"> <img src="assets/badges/tech-3.svg" alt="FFmpeg视频处理"></p>

使用 MobileNetV2 对 Violence / NonViolence 图像帧做二分类，再将单视频的帧概率取均值生成预测。工程覆盖 FFmpeg 预处理、Keras 训练/评估、视频预测和 FP16 TFLite 导出。

## 🧭 导航

[🎞️ 视频与模型流程](#️-视频与模型流程) · [📊 帧级评估](#-历史帧级验证与边界) · [⚙️ 运行](#️-使用与项目结构) · [📦 模型与来源](#-模型来源与验证状态)

[完整运行说明](docs/RUNNING.md) · [评估口径](docs/EVALUATION.md) · [共同贡献](docs/SOURCES_AND_PERMISSIONS.md) · [实际验证](VERIFICATION.md)

## 🎞️ 视频与模型流程

<p align="center"><img src="assets/pipeline.svg" width="420" alt="campus-violence-recognition业务流程图"></p>

模型分类头：`MobileNetV2 → GlobalAveragePooling2D → Dense(512, ReLU) → Dropout(0.5) → BatchNormalization → Dense(1, Sigmoid)`。先冻结骨干训练，再可选解冻最后20层微调，BatchNorm保持冻结。实现入口：[训练](src/04_train_model.py)、[视频推理](src/05_predict_video.py)、[FFmpeg封装](src/ffmpeg_utils.py)。

TFLite 是导出格式，不等于已完成开发板部署。尚无四路1080P、知识蒸馏、SQLite告警、72小时连续运行的对应实现或验收证据，不纳入成果。

## 📊 历史帧级验证与边界

以下是保存记录中的**本地帧级验证**，本轮未重跑，不能作为视频级测试或竞赛成绩。训练8418帧、验证2103帧；代码在帧目录上做20%验证切分，未实现按视频来源隔离，可能包含同源视频帧。

| Accuracy | Precision | Recall | F1 | AUC |
|---:|---:|---:|---:|---:|
| 0.9163 | 0.9356 | 0.8994 | 0.9171 | 0.9653 |

历史混淆矩阵 `[[953, 67], [109, 974]]`，类别顺序为 NonViolence、Violence。Accuracy/Precision/Recall/F1 已做算术核验；缺原始逐帧概率，AUC 未独立复算。历史输出见 [训练记录](results/train_result.txt)、[预测示例](results/predict_examples.txt)。个别示例预测不能证明视频测试集性能。

## ⚙️ 使用与项目结构

环境由 [environment_tf210_py39.yml](environment_tf210_py39.yml)定义：Python3.9、TensorFlow2.10.1、Keras2.10.0及FFmpeg。完整数据布局、训练参数、模型位置与推理命令见[运行说明](docs/RUNNING.md)。例：

```powershell
python src/05_predict_video.py --video "path/to/authorized-example.mp4" --model "models/best_model_finetuned.h5" --fps 1
```

`src/` 中编号01–05的脚本对应重封装、抽帧、检查、训练、预测；`scripts/` 为 Windows 示例调用。`dataset/`、`frames/`、`fixed_videos/`、`logs/`、`models/` 只保留说明，数据和权重不进入当前 Git 文件树。

## 📦 模型、来源与验证状态

[既有 Releases](https://github.com/Sh-Vacle/campus-violence-recognition/releases)包含历史模型资产；本轮只核对元数据，未下载、测试或重新上传模型，包内容与分发权仍需项目拥有者确认。数据历史来源记为 Kaggle Real Life Violence Situations Dataset；请自行确认合法使用范围。

现有代码 [MIT LICENSE](LICENSE) 保留，但不据此重新授权数据、模型、厂商材料或全部团队方案。历史方案涉及多位共同贡献者，见[来源说明](docs/SOURCES_AND_PERMISSIONS.md)，不描述为个人独立完成整个团队成果。开发与工程整理使用过 AI 辅助。

当前分支已停止跟踪 OrangePi 厂商手册，改用[厂商支持页](http://www.orangepi.cn/html/hardWare/computerAndMicrocontrollers/service-and-support/Orange-Pi-kunpeng.html)；旧 Git 历史仍保留该文件。当前 Logo 与本地徽章是自行编写的 SVG。

静态源码、链接和历史指标核对通过；本轮没有训练、TensorFlow模型加载、视频实测、TFLite执行或设备部署。项目用于学习与实验，预测需要人工复核，不能据此自动处罚或判断身份。[公开文件清单](PUBLIC_FILES_MANIFEST.md) · [排除清单](EXCLUDED_FILES.md)
