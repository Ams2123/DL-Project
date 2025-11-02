# DL-Project

| # | File                       | Name       | Type                 | Input             | Output          | Layers      | Status       |
| - | -------------------------- | ---------- | -------------------- | ----------------- | --------------- | ----------- | ------------ |
| 1 | BasicCNN.py                | CNN        | Conv Neural Net      | Image (64×64×3)   | 1 value (angle) | 2 Conv + FC | ✅ACTIVE      |
| 2 | PoseNet.py                 | NN         | Fully Connected      | Image (64×64×3)   | 1 value (angle) | 4 FC layers | ⚠️ Reference |
| 3 | fnet_basic_architecture.py | FNet       | Fully Connected      | Laser (20 values) | 3 actions       | 2 FC layers | ⚠️ Reference |
| 4 | fnet_with_ssd.py           | FNet + SSD | Hybrid (FC + SSD300) | Laser + Image     | 3 actions       | FNet + SSD  | ⚠️ Reference |
| 5 | cheatCNN.py                | Rule-Based | Image Processing     | Image (64×64×3)   | 3 actions       | No learning | ⚠️ Baseline  |

## 1️⃣ BasicCNN 
File: BasicCNN.py

Architecture:

```
Input: 64×64×3 (RGB image)
  ↓
Conv2d(in=3, out=16, kernel=5×5) + ReLU
  ↓
MaxPool2d(2×2) → 32×32×16
  ↓
Conv2d(in=16, out=32, kernel=5×5) + ReLU
  ↓
MaxPool2d(2×2) → 16×16×32
  ↓
Flatten → 8,192 features
  ↓
Linear(8192, 1) → Steering Angle
```
### Specs:

Input: Camera image (64×64 RGB)

Output: 1 steering angle (-180° to +180°)

Parameters: ~8,193

Use Case: Image-based steering prediction

Status: ✅ Training & Active

## 2️⃣ PoseNet (Fully Connected)
File: PoseNet.py

Architecture:

```
Input: 64×64×3 (RGB image)
  ↓
Flatten → 12,288 features
  ↓
Linear(12288, 12288) + BatchNorm + Sigmoid
  ↓
Linear(12288, 12288) + BatchNorm + Sigmoid
  ↓
Linear(12288, 1) + Tanh → Output
```
### Specs:

Input: Flattened image

Output: 1 value (bounded by Tanh)

Parameters: ~150M+ (very large!)

Layers: 4 fully connected

Activation: Sigmoid + Tanh

Use Case: Pose estimation or steering

Status: ⚠️ Reference/Alternative

## 3️⃣ FNet - Basic Architecture
File: fnet_basic_architecture.py

Architecture:

```
Input: Laser readings (20 sensors)
  ↓
Linear(20, 64) + ReLU
  ↓
Linear(64, 3) → Actions
```
### Specs:

Input: 20 laser distance values

Output: 3 actions [Turn Left, Forward, Turn Right]

Parameters: ~1,300

Layers: 2 fully connected

Use Case: Laser-based control

Status: ⚠️ Reference (not trained)

## 4️⃣ FNet + SSD300
File: fnet_with_ssd.py

Architecture:

```
Input: Laser readings + Image
  ↓
SSD300 (Pre-trained) → Object Detection
         ↓
    Bounding boxes + Labels
  ↓
FNet (Laser readings)
  ↓
Linear(20, 64) + ReLU → Linear(64, 3)
  ↓
Combined output: Actions
```
### Specs:

Input: Laser readings + Camera image

SSD Model: Pre-trained SSD300 for object detection

FNet: 2 FC layers (20→64→3)

Output: 3 actions

Use Case: Multi-modal learning (laser + vision)

Status: ⚠️ Reference (hybrid approach)

Note: Requires torchvision

## 5️⃣ CheatCNN (Rule-Based)
File: cheatCNN.py

Architecture:

```
Input: 64×64×3 (RGB image)
  ↓
Red-Blue channel difference
  ↓
Find goal position in image
  ↓
Simple heuristics:
  - If goal left → Turn left
  - If goal right → Turn right
  - If goal center → Move forward
  ↓
Output: [W, A, D] controls
```
### Specs:

Input: RGB image

Output: 3 binary controls [Forward, Left, Right]

Layers: None (no neural network!)

Method: Rule-based image processing

Use Case: Baseline comparison

Status: ⚠️ No learning (baseline)

## Comparison Table
| Feature      | BasicCNN | PoseNet     | FNet      | FNet+SSD    | CheatCNN  |
| ------------ | -------- | ----------- | --------- | ----------- | --------- |
| Input Type   | Image    | Image       | Laser     | Laser+Image | Image     |
| Network Type | CNN      | FC          | FC        | Hybrid      | Heuristic |
| Parameters   | 8K       | 150M+       | 1.3K      | 150M+       | 0         |
| Trainable    | ✅ Yes    | ✅ Yes       | ✅ Yes     | ✅ Yes       | ❌ No      |
| Speed        | Fast     | Slow        | Very Fast | Slow        | Instant   |
| Accuracy     | Medium   | High (size) | Low       | High        | Baseline  |
| Current Use  | ✅ Active | ⚠️ Ref      | ⚠️ Ref    | ⚠️ Ref      | ⚠️ Ref    |
