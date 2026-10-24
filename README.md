**芯片缺陷检测数据集 芯片分割数据集**
1337张图片，使用labelme标注
内含coco格式标签（.json）文件、yolo格式标签（.txt）文件、mask格式文件（.png）
四种缺陷类别

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/bfa3538c74c0493fae8185ed3e062b88.png)
1
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/f718d2836dec426887490014a835130d.png)


### 🧩 芯片缺陷检测分割数据集（自制）  
**总数量：1,337 张图像**  
**标注工具：LabelMe**  
**支持格式：COCO JSON、YOLO TXT、Mask PNG**

| **项目**                | **内容**                                                                 |
|------------------------|--------------------------------------------------------------------------|
| **数据集名称**          | 芯片缺陷检测分割数据集（自制）                                           |
| **总图像数量**          | 1,337 张                                                                 |
| **图像分辨率**          | 多样化（常见为 1024×1024 或 2048×2048，高分辨率工业相机拍摄）             |
| **文件格式**            | JPG / PNG（原始图像）                                                    |
| **标签格式**            | ✅ COCO JSON（`instances.json`）<br>✅ YOLO TXT（`.txt` 文件，含边界框与掩码）<br>✅ Mask PNG（每张图对应一个 `.png` 掩码图） |
| **标注工具**            | LabelMe（支持多边形/矩形标注，生成精确掩码）                             |
| **缺陷类别数量**        | 4 类                                                                     |
| **缺陷类别及中文说明**  |                                                                          |
| &nbsp;&nbsp;– ZF-scratch | 字符污痕（如“JHGR”字符区域的污染或模糊）                                 |
| &nbsp;&nbsp;– scratch    | 划损（表面划痕，通常细长且连续）                                         |
| &nbsp;&nbsp;– pinbreak   | 引脚损坏（引脚断裂、缺失或变形）                                         |
| &nbsp;&nbsp;– broken     | 基材破损（芯片主体结构裂纹、崩角、碎裂等）                               |
| **数据划分**            | 未明确给出，建议按 7:2:1 分为训练集、验证集、测试集                         |
| **应用场景**            | 半导体制造质检、AI 自动光学检测（AOI）、智能制造、SMT 工艺监控               |
| **特点**                | - 高精度像素级标注<br>- 包含复杂缺陷形态（如细小划痕、局部破损）<br>- 支持实例分割任务（mask） |

---

### 🔍 图像示例说明（根据图片）

- **左图**：显示“JHGR”字符区域存在 **ZF-scratch**（字符污痕），标注为多边形包围该区域。
- **右图**：芯片边缘有 **broken**（基材破损），标注为不规则多边形覆盖破损区域。

> 💡 所有缺陷均以 **像素级 mask** 标注，适合用于：
> - 实例分割（Instance Segmentation）
> - 目标检测 + 掩码预测
> - 缺陷修复生成模型（如 GAN、扩散模型）

---

### 📁 数据目录结构示例-参考

```
chip_defect_dataset/
├── images/
│   ├── train/
│   ├── val/
│   └── test/
├── labels/
│   ├── coco/
│   │   └── instances_train.json
│   ├── yolo/
│   │   ├── train/
│   │   └── val/
│   └── masks/
│       ├── train/
│       └── val/
└── README.md
```

---

### ✅ 可支持的模型任务

| 任务类型           | 支持情况 | 说明 |
|--------------------|----------|------|
| 目标检测（Detection） | ✅ | 使用 YOLOv5/v8 或 Faster R-CNN |
| 实例分割（Segmentation） | ✅ | 使用 Mask R-CNN、YOLACT、YOLOv8-seg |
| 语义分割（Semantic Segmentation） | ✅ | 使用 U-Net、DeepLabV3+ |
| 缺陷分类（Classification） | ✅ | 提取 ROI 后分类 |
| 缺陷修复（Inpainting） | ✅ | 使用 GAN、Diffusion Model 生成补丁 |

---


> 🚀 该数据集非常适合用于构建 **工业 AI 视觉质检系统**，尤其适用于半导体、PCB、电子元器件等领域的自动化缺陷识别。

# 🧪 芯片缺陷检测分割系统（YOLOv8-seg 实例分割）  
**数据集说明**：1337 张高精度芯片图像，4 类缺陷，含 **COCO JSON / YOLO TXT / Mask PNG** 三种标注格式  

✅ 提供完整 **YOLOv8-seg 训练 + 推理代码**  
✅ 支持 COCO 或 YOLO 格式直接训练  
✅ 适用于工业 AOI（自动光学检测）场景  

---

## 📁 一、项目结构

```
chip_defect_seg/
├── images/                 # 原始图像（.jpg）
├── labels/
│   ├── yolo/               # YOLO格式标签（.txt）
│   └── coco/
│       └── instances.json  # COCO格式（可选）
├── masks/                  # 可视化掩码（.png，用于验证）
├── data.yaml               # YOLO格式配置
├── train_yolo.py           # 使用YOLO标签训练
├── train_coco.py           # 使用COCO标签训练（推荐）
├── detect.py               # 推理脚本
└── split_dataset.py        # 自动划分 train/val/test
```

> 💡 **建议**：优先使用 **COCO 格式训练**（YOLOv8 对 COCO 支持更完整，尤其对 mask）

---

## 🔧 二、环境安装

```bash
conda create -n chip_seg python=3.9 -y
conda activate chip_seg
pip install ultralytics opencv-python pycocotools matplotlib tqdm numpy pandas
```

> ✅ `pycocotools` 用于加载 COCO 标注。

---

## 📂 三、数据集划分（若未划分）

### 脚本：`split_dataset.py`

```python
# split_dataset.py
import os
import shutil
from pathlib import Path
import random

def split_dataset(img_dir, label_dir, output_dir, ratios=(0.7, 0.2, 0.1)):
    img_paths = list(Path(img_dir).glob("*.jpg"))
    random.seed(42)
    random.shuffle(img_paths)
    
    n = len(img_paths)
    n_train = int(n * ratios[0])
    n_val = int(n * (ratios[0] + ratios[1]))
    
    splits = {
        "train": img_paths[:n_train],
        "val": img_paths[n_train:n_val],
        "test": img_paths[n_val:]
    }
    
    for split, paths in splits.items():
        (Path(output_dir) / "images" / split).mkdir(parents=True, exist_ok=True)
        (Path(output_dir) / "labels" / split).mkdir(parents=True, exist_ok=True)
        
        for img_path in paths:
            # 复制图像
            shutil.copy(img_path, Path(output_dir) / "images" / split / img_path.name)
            # 复制对应标签（YOLO .txt）
            lbl_path = Path(label_dir) / (img_path.stem + ".txt")
            if lbl_path.exists():
                shutil.copy(lbl_path, Path(output_dir) / "labels" / split / (img_path.stem + ".txt"))

if __name__ == "__main__":
    split_dataset(
        img_dir="images",
        label_dir="labels/yolo",
        output_dir="datasets/chip_defect"
    )
    print("✅ 数据集已划分为 train/val/test")
```

---

## 📄 四、YOLO 格式配置：`data.yaml`

```yaml
# data.yaml
path: ./datasets/chip_defect
train: images/train
val: images/val
test: images/test

nc: 4
names: ['ZF-scratch', 'scratch', 'pinbreak', 'broken']
```

---

## 🚀 五、训练代码（两种方式）

### 方式 1️⃣：使用 **YOLO 格式标签**（简单，但 mask 精度略低）

```python
# train_yolo.py
from ultralytics import YOLO

model = YOLO('yolov8n-seg.pt')  # 或 yolov8s-seg.pt

results = model.train(
    data='data.yaml',
    epochs=150,
    imgsz=640,
    batch=8,          # 小 batch（因图像分辨率高）
    name='chip_defect_yolo',
    device=0,
    patience=25,
    hsv_h=0.01,
    hsv_s=0.5,
    hsv_v=0.3,
    degrees=5.0,
    mosaic=0.5        # 降低 mosaic（避免小缺陷被裁剪）
)

print("✅ YOLO格式训练完成！")
```

---

### 方式 2️⃣（推荐）：使用 **COCO 格式标签**（更高 mask 精度）

> ⚠️ 需将 COCO JSON 放入标准 COCO 目录结构

#### COCO 目录结构：
```
coco_chip/
├── images/
│   ├── train2017/
│   └── val2017/
└── annotations/
    ├── instances_train2017.json
    └── instances_val2017.json
```

#### 脚本：`train_coco.py`

```python
# train_coco.py
from ultralytics import YOLO

# YOLOv8 可直接加载 COCO 格式
model = YOLO('yolov8n-seg.pt')

results = model.train(
    data='coco_chip/annotations/instances_train2017.json',  # 直接传 JSON 路径
    epochs=150,
    imgsz=640,
    batch=8,
    name='chip_defect_coco',
    device=0,
    patience=25,
    project='runs/segment'
)

print("✅ COCO格式训练完成！")
```

> ✅ **优势**：COCO 格式保留完整多边形点，mask 更精确，适合细小划痕（scratch）检测。

---

## 🔍 六、推理与可视化（`detect.py`）

```python
# detect.py
from ultralytics import YOLO
import cv2

model = YOLO('runs/segment/chip_defect_coco/weights/best.pt')

def detect_image(img_path, output_path="result.jpg"):
    results = model(img_path, imgsz=1024)  # 高分辨率推理
    annotated = results[0].plot()
    cv2.imwrite(output_path, annotated)
    print(f"✅ 结果保存至 {output_path}")

# 显示 mask 掩码（可选）
def save_masks(img_path, output_dir="masks_output"):
    import numpy as np
    results = model(img_path)
    masks = results[0].masks.data.cpu().numpy()  # (N, H, W)
    for i, mask in enumerate(masks):
        mask_uint8 = (mask * 255).astype(np.uint8)
        cv2.imwrite(f"{output_dir}/mask_{i}.png", mask_uint8)

if __name__ == "__main__":
    detect_image("test_chip.jpg")
    save_masks("test_chip.jpg")
```

---

## 📊 七、预期性能（1337 张数据）

| 指标 | 预期值 | 说明 |
|------|--------|------|
| **Seg mAP@0.5** | ≥ 0.85 | 对“scratch”等细小缺陷敏感 |
| **Recall** | ≥ 0.82 | 低漏检率 |
| **训练时间** | ~3 小时（RTX 3060, yolov8n-seg） | |
| **最佳模型** | yolov8s-seg | 平衡精度与速度 |

---

## 🛠️ 八、工业部署建议

1. **输入尺寸**：推理时用 `imgsz=1024` 保留细节；
2. **后处理**：对“scratch”类启用 **细粒度 NMS**（IoU 阈值设为 0.3）；
3. **量化**：导出 TensorRT 加速：
   ```python
   model.export(format='engine', half=True)  # FP16 TensorRT
   ```
4. **报警逻辑**：任一缺陷面积 > 阈值 → 触发 NG 报警。

---



---

> 🏭 **总结**：本系统专为 **半导体芯片 AOI 质检** 设计，利用 YOLOv8-seg 实现像素级缺陷分割，可精准识别 **污痕、划伤、引脚损坏、基材破损** 四类典型缺陷，助力智能制造与零缺陷生产。
