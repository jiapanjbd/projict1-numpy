# 项目一 · 图像编码器前向计算复现（OpenCLIP ViT-B-32）

本提交只完成了随附项目中的 `image_encoder.py`（TODO-01 ~ TODO-06），用 NumPy
复现图像编码器的前向计算。其余框架代码、权重、数值辅助函数与校验脚本均为课程
提供，未作改动。

## 运行方式

把本 `image_encoder.py` 覆盖到随附的 `project1-numpy-student/` 根目录即可。该目录
需保留课程提供的 `helpers.py`、`runtime.py`、`config.json`、`weights/`、`assets/`
等文件；按提交要求，大权重文件与参考数据不随本提交重复打包。

依赖：Python 3.9+、NumPy、Pillow、imageio；无需 GPU、PyTorch、OpenCLIP 或网络。

```bash
python -m pip install -r requirements.txt
python check.py --stage all                    # 8 个阶段数值校验
python run.py --output output_tokens.npz       # 生成 10 张图的输出向量
python run.py --images a.jpg b.png --output my_tokens.npz   # 自定义图片
```

`run.py` 保存与输入同序的图片 ID 和输出向量，输出为 float32 `[B,512]`，每行单位长度。

## 结果

在 Windows 11 / Python 3.13.14 / NumPy 2.5.5 下，`check.py --stage all` 全部 PASS：

| 阶段 | shape | 最大绝对误差 | 平均绝对误差 | 结果 |
| --- | --- | --- | --- | --- |
| preprocess | (10,3,224,224) | 0.000e+00 | 0.000e+00 | PASS |
| patches | (2,49,3072) | 0.000e+00 | 0.000e+00 | PASS |
| tokens | (2,50,768) | 4.387e-05 | 1.335e-06 | PASS |
| block0 | (2,50,768) | 7.147e-06 | 3.348e-07 | PASS |
| last_tokens | (2,50,768) | 4.196e-05 | 2.633e-06 | PASS |
| output | (10,512) | 5.662e-07 | 8.688e-08 | PASS |
| batch | (3,512) | 4.061e-07 | 7.848e-08 | PASS |

输出向量容差 atol=2e-5、rtol=2e-4，内部张量 atol=2e-4、rtol=2e-4，全部满足。

## 文件清单

- `image_encoder.py`：完成的编码器（唯一被修改的文件）
- `main.tex`：实验报告源码，XeLaTeX 编译
- `check_all_output.txt`：`check.py --stage all` 的实测输出，供核对
