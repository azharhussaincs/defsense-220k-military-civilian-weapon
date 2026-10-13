# FullData: Unified 5-Class Military, Civilian & Weapon Object Detection Dataset

[![Format: YOLO](https://img.shields.io/badge/Format-YOLO%20Normalized-blue.svg)](https://docs.ultralytics.com/datasets/detect/)
[![Classes: 5](https://img.shields.io/badge/Classes-5-brightgreen.svg)](#2-class-taxonomy--schema)
[![Images: 220,214](https://img.shields.io/badge/Images-220%2C214-orange.svg)](#1-dataset-overview--scope)
[![Annotations: 434,329](https://img.shields.io/badge/Annotations-434%2C329-red.svg)](#1-dataset-overview--scope)
[![Split: 80/10/10](https://img.shields.io/badge/Split-80%2F10%2F10%20Stratified-purple.svg)](#3-directory-structure--annotation-format)

Welcome to **FullData**, an enterprise-grade, curated computer vision dataset engineered for **Intelligence, Surveillance, and Reconnaissance (ISR)** and **Urban Battlefield Situational Awareness**. This dataset resolves the challenge of cross-domain discrimination: separating military assets from civilian traffic, identifying military troops versus non-combatant civilians, and detecting armed threats via standalone or wielded firearms.

---

## 1. Dataset Overview & Scope

### Project Mission
Automated target recognition (ATR) and perimeter defense systems require simultaneous high-recall vehicle classification, person-type discrimination, and ultra-fine weapon localization. **FullData** harmonizes 14 heterogeneous source datasets (over 249,000 raw files) into a clean, unified, zero-leakage corpus.

### High-Level Statistics
* **Total Verified Images**: **220,214** (JPEG / PNG / BMP, fully verified headers)
* **Total Bounding Boxes**: **434,329** (strictly bounded $[0.0, 1.0]$, 0 NaNs, 0 coordinate errors)
* **Image-to-Label Parity**: **100.0% Exact 1:1 Match** (0 orphan images, 0 orphan labels)
* **Partition Strategy**: Deterministic **80% Train / 10% Val / 10% Test** combinatorial stratification
* **Storage Footprint**: **~25.2 GB** on disk

---

## 2. Class Taxonomy & Schema

The dataset operates on a standardized, non-overlapping 5-class schema defined in [`data.yaml`](file:///home/albaloshi/Desktop/DataSet/FullData/data.yaml):

| Class ID | Class Name | Population Count | Proportion | Included Entities & Tactical Scope |
| :---: | :--- | :---: | :---: | :--- |
| `0` | **`military_vehicle`** | **203,551** | 46.87% | Main Battle Tanks (MBTs), Armoured Personnel Carriers (APCs), Infantry Fighting Vehicles (IFVs), Multiple-Launch Rocket Systems (MLRS), Military Logistics Trucks, Self-Propelled Artillery, Anti-Aircraft Systems, Naval Warships, and Combat Aircraft/Helicopters. |
| `1` | **`civilian_vehicle`** | **53,080** | 12.22% | Passenger Cars, Sedans, SUVs, Minivans, Commercial Transit Buses, Cargo Trucks, Delivery Vans, Motorbikes, Scooters, Bicycles, Auto-Rickshaws, Three-Wheelers, Pickups, and Civilian Aircraft. |
| `2` | **`soldier`** | **80,125** | 18.45% | Uniformed Military Personnel, Camouflage Combatants, Tactical Operators, Armed Irregular Fighters, and Troops manning combat vehicles or weapon systems. |
| `3` | **`civilian`** | **84,257** | 19.40% | Non-combatant Citizens, Pedestrians, Bystanders, Men, Women, and Children in non-military clothing without tactical gear. |
| `4` | **`weapon`** | **13,316** | 3.07% | Small Arms, Assault Rifles (AK, M4, etc.), Submachine Guns (SMGs), Handguns/Pistols, Sniper Rifles, Shotguns, RPGs/Bazookas, and Grenade Launchers (both in-hand and standalone). |

### Stratified Split Distribution
Every class maintains an exact $\sim 80/10/10$ representation across splits, eliminating evaluation bias:

| Class Name | ID | Total Boxes | Train (80%) | Val (10%) | Test (10%) |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **`military_vehicle`** | `0` | 203,551 | 162,675 (79.92%) | 20,552 (10.10%) | 20,324 (9.98%) |
| **`civilian_vehicle`** | `1` | 53,080 | 42,400 (79.88%) | 5,317 (10.02%) | 5,363 (10.10%) |
| **`soldier`** | `2` | 80,125 | 64,576 (80.59%) | 7,794 (9.73%) | 7,755 (9.68%) |
| **`civilian`** | `3` | 84,257 | 67,526 (80.14%) | 8,477 (10.06%) | 8,254 (9.80%) |
| **`weapon`** | `4` | 13,316 | 10,713 (80.45%) | 1,348 (10.12%) | 1,255 (9.42%) |
| **Total Boxes** | — | **434,329** | **347,890** (80.10%) | **43,488** (10.01%) | **42,951** (9.89%) |
| **Total Images** | — | **220,214** | **176,196** (80.01%) | **22,017** (10.00%) | **22,001** (9.99%) |

---

## 3. Directory Structure & Annotation Format

### File System Layout
```
FullData/
├── data.yaml                    # Master Ultralytics YOLO dataset configuration
├── README.md                    # Production onboarding and operations manual
├── dataset_summary.md           # Granular audit and provenance documentation
├── qc_heuristics_check.jpg      # 100-sample visual QC verification montage
├── images/
│   ├── train/                   # 176,196 training images
│   ├── val/                     #  22,017 validation images
│   └── test/                    #  22,001 test images
└── labels/
    ├── train/                   # 176,196 training YOLO label files (.txt)
    ├── val/                     #  22,017 validation YOLO label files (.txt)
    └── test/                    #  22,001 test YOLO label files (.txt)
```

### YOLO Normalized Annotation Standard
Every `.txt` file corresponds to the identical base filename of its image file (e.g., `images/train/sample_001.jpg` $\leftrightarrow$ `labels/train/sample_001.txt`). Each object bounding box occupies one line:

$$\langle\text{class\_id}\rangle \quad \langle x_{\text{center}}\rangle \quad \langle y_{\text{center}}\rangle \quad \langle\text{width}\rangle \quad \langle\text{height}\rangle$$

```
0 0.512450 0.489120 0.642100 0.384200
2 0.315000 0.521000 0.082000 0.320000
4 0.334000 0.548000 0.031000 0.125000
```

> [!NOTE]
> * All coordinates are normalized floats relative to image dimensions ($[0.0, 1.0]$).
> * $x_{\text{center}} = \frac{x_{\min} + x_{\max}}{2 \cdot W}$, $y_{\text{center}} = \frac{y_{\min} + y_{\max}}{2 \cdot H}$, $\text{width} = \frac{x_{\max} - x_{\min}}{W}$, $\text{height} = \frac{y_{\max} - y_{\min}}{H}$.
> * Negative background images contain an empty `0-byte` label file, providing necessary non-target false-positive suppression ($\sim 8.7\%$ background samples).

---

## 4. Critical Engineering Considerations

### A. The Small-Object Receptive Field Challenge (Weapons)
> [!WARNING]
> Standard object detectors trained at default `imgsz=640` downsample input features by a stride of $32\times$ at the lowest feature map (P5), reducing an $800\times 800$ image feature cell to $25\times 25$ pixels. Handguns and rifles often span only $10\text{--}25$ pixels ($<1.5\%$ of frame area), causing feature maps to lose signal entirely.

* **Mandatory Resolution**: You **MUST** train with `imgsz=1024` or `imgsz=1280`.
* **P2 Detection Layer**: For architectures supporting P2 feature heads (stride 4), ensure tiny anchor feature pyramids are active.

### B. Class Imbalance Mitigation Strategy
The dataset exhibits a natural operational imbalance: `military_vehicle` (203k instances) vs. `weapon` (13.3k instances) and `civilian_vehicle` (53k instances). To prevent gradient dominance by the majority class:

1. **Focal Loss ($\gamma=1.5$)**: Down-weights well-classified majority vehicle samples and concentrates gradient updates on challenging, rare minority samples (`fl_gamma=1.5`).
2. **Classification Loss Gain**: Elevate classification loss weight relative to box regression (`cls=1.5`, `box=7.5`, `dfl=1.5`).
3. **Data Augmentation**:
   * Enable Copy-Paste augmentation (`copy_paste=0.30`) to paste segmented soldiers and weapons onto diverse backgrounds.
   * Mosaic augmentation (`mosaic=1.0`) combined with MixUp (`mixup=0.15`).
   * Disable mosaic for the final 10 epochs (`close_mosaic=10`) to stabilize boundary localization.

---

## 5. Training Launch Recipes

### Environment Setup
```bash
# Recommended Python: 3.10 or 3.11
pip install torch torchvision --index-url https://download.pytorch.org/whl/cu121
pip install ultralytics opencv-python-headless pyyaml
```

### Recipe 1: Distributed Multi-GPU YOLOv11x (Recommended Baseline)
```bash
yolo detect train \
  data=/home/albaloshi/Desktop/DataSet/FullData/data.yaml \
  model=yolo11x.pt \
  epochs=100 \
  patience=15 \
  batch=32 \
  imgsz=1024 \
  device=0,1,2,3 \
  workers=16 \
  optimizer=AdamW \
  lr0=0.001 \
  lrf=0.01 \
  weight_decay=0.0005 \
  fl_gamma=1.5 \
  box=7.5 \
  cls=1.5 \
  dfl=1.5 \
  mosaic=1.0 \
  mixup=0.15 \
  copy_paste=0.30 \
  close_mosaic=10 \
  amp=True \
  project=/home/albaloshi/Desktop/DataSet/runs \
  name=fulldata_yolo11x_1024
```

### Recipe 2: RT-DETR-x (Transformer-based SOTA for Small Targets)
```bash
yolo detect train \
  data=/home/albaloshi/Desktop/DataSet/FullData/data.yaml \
  model=rtdetr-x.pt \
  epochs=72 \
  patience=12 \
  batch=16 \
  imgsz=1024 \
  device=0,1,2,3 \
  workers=16 \
  optimizer=AdamW \
  lr0=0.0001 \
  weight_decay=0.0001 \
  amp=True \
  project=/home/albaloshi/Desktop/DataSet/runs \
  name=fulldata_rtdetr_1024
```

### Recipe 3: Python API Launch Script
```python
#!/usr/bin/env python3
from ultralytics import YOLO

# Initialize model with pre-trained weights
model = YOLO('yolo11x.pt')

# Train on FullData with production hyperparameters
results = model.train(
    data='/home/albaloshi/Desktop/DataSet/FullData/data.yaml',
    epochs=100,
    patience=15,
    batch=32,
    imgsz=1024,
    device=[0, 1, 2, 3],
    workers=16,
    optimizer='AdamW',
    lr0=1e-3,
    lrf=1e-2,
    fl_gamma=1.5,
    box=7.5,
    cls=1.5,
    dfl=1.5,
    mosaic=1.0,
    copy_paste=0.30,
    close_mosaic=10,
    amp=True,
    project='/home/albaloshi/Desktop/DataSet/runs',
    name='fulldata_yolo11x_production'
)
```

---

## 6. Validation, Testing & Inference

### A. Formal Model Evaluation on Test Split
Once training completes, evaluate your top checkpoint on the unseen test partition:

```bash
# Evaluate on test split (22,001 unseen images)
yolo detect val \
  data=/home/albaloshi/Desktop/DataSet/FullData/data.yaml \
  model=/home/albaloshi/Desktop/DataSet/runs/fulldata_yolo11x_1024/weights/best.pt \
  split=test \
  imgsz=1024 \
  batch=32 \
  device=0 \
  save_json=True
```

### B. Production Inference & Visualization
Run predictions on unannotated ISR drone video feeds or multi-spectral sensor feeds:

```bash
# Inference on directory or video stream
yolo detect predict \
  model=/home/albaloshi/Desktop/DataSet/runs/fulldata_yolo11x_1024/weights/best.pt \
  source=/path/to/surveillance_video.mp4 \
  imgsz=1024 \
  conf=0.25 \
  iou=0.45 \
  device=0 \
  save=True \
  save_txt=True \
  line_width=2
```

```python
# Real-time streaming Python inference snippet
from ultralytics import YOLO
import cv2

model = YOLO('/home/albaloshi/Desktop/DataSet/runs/fulldata_yolo11x_1024/weights/best.pt')

# Run batch prediction
results = model.predict(source='custom_feed.jpg', conf=0.25, imgsz=1024)

for r in results:
    boxes = r.boxes
    for box in boxes:
        cls_id = int(box.cls[0])
        conf = float(box.conf[0])
        xyxy = box.xyxy[0].tolist()
        print(f"Detected {model.names[cls_id]} ({conf:.2f}) at {xyxy}")
```

---

## 7. Quality Assurance & Provenance Records

* **Provenance & Lineage Audit**: [`dataset_summary.md`](file:///home/albaloshi/Desktop/DataSet/FullData/dataset_summary.md)
* **Visual Bounding Box Montage**: [`qc_heuristics_check.jpg`](file:///home/albaloshi/Desktop/DataSet/FullData/qc_heuristics_check.jpg)
* **Master Configuration**: [`data.yaml`](file:///home/albaloshi/Desktop/DataSet/FullData/data.yaml)

*Maintained by the Computer Vision & MLOps Engineering Team.*
# defsense-220k-military-civilian-weapon
