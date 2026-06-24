# EEGNet for CHB-MIT Seizure Detection

## Overview

This project implements an EEG-based seizure detection pipeline using **EEGNet** and the **CHB-MIT Scalp EEG Database**.

The objective is to classify EEG windows into:

- **0 → Non-Seizure**
- **1 → Seizure**

The pipeline includes:

- EEG preprocessing
- Channel standardization
- Window extraction
- Subject-wise train/validation/test splitting
- Class imbalance handling
- EEGNet training
- Clinical threshold evaluation

---

## Dataset

**CHB-MIT Scalp EEG Database (PhysioNet)**

- 24 pediatric subjects
- Scalp EEG recordings
- EDF format
- Seizure and non-seizure annotations

Dataset Version:

```text
CHB-MIT Scalp EEG Database 1.0.0
```

---

## EEG Channels

18-channel bipolar montage:

```text
FP1-F7
F7-T7
T7-P7
P7-O1

FP1-F3
F3-C3
C3-P3
P3-O1

FP2-F4
F4-C4
C4-P4
P4-O2

FP2-F8
F8-T8
T8-P8
P8-O2

FZ-CZ
CZ-PZ
```

---

## Preprocessing Pipeline

### Signal Processing

```text
High-pass Filter : 1 Hz
Notch Filter     : 50 Hz
Sampling Rate    : 256 Hz
```

### Windowing

```text
Window Length : 4 seconds
Window Size   : 1024 samples
Stride        : 4 seconds
Overlap       : None
```

---

## Subject Split

### Seizure Subjects

```text
01
02
03
04
05
06
```

### Normal Subjects

```text
07
08
09
10
```

### Training Set

```text
01
02
03
04
07
08
```

### Validation Set

```text
05
09
```

### Test Set

```text
06
10
```

Patient-wise separation is maintained to prevent data leakage.

---

## Model Architecture

### EEGNet

```text
Input EEG
    ↓
Temporal Convolution
    ↓
Depthwise Convolution
    ↓
Separable Convolution
    ↓
Pooling
    ↓
Classification Layer
    ↓
Seizure / Non-Seizure
```

Configuration:

```python
EEGNet(
    n_chans=18,
    n_outputs=2,
    n_times=1024,
    final_conv_length="auto",
    pool_mode="mean"
)
```

Trainable Parameters:

```text
2,418
```

---

## Class Imbalance Handling

The seizure detection problem is highly imbalanced.

Techniques used:

### Focal Loss

```text
Gamma = 2.0
```

### Weighted Random Sampling

Oversamples seizure windows during training to improve minority-class learning.

---

## Training Configuration

```text
Optimizer      : AdamW
Learning Rate  : 5e-4
Weight Decay   : 1e-4

Batch Size     : 64
Epochs         : 30
Patience       : 7

Gradient Clip  : 1.0
```

### Learning Rate Scheduler

```text
ReduceLROnPlateau
```

### Early Stopping

Validation-loss-based checkpointing and early stopping are used to reduce overfitting.

---

## Evaluation Metrics

The following metrics are reported:

```text
Recall (Sensitivity)
Specificity
Precision
F1 Score
G-Mean
ROC-AUC
```

Threshold Sweep:

```text
0.10
0.15
0.20
0.30
0.40
0.50
```

Best threshold is selected using:

```text
Maximum G-Mean
```

---

## Example Results

```text
ROC-AUC : 0.5512
Best Threshold : 0.20

True Positives  (TP) : 9
False Positives (FP) : 3522
True Negatives  (TN) : 1865
False Negatives (FN) : 4
```

These results were obtained from a rapid-prototype subset of CHB-MIT and are intended for pipeline validation rather than final clinical reporting.

---

## Project Structure

```text
eegnet_rapid_prototype.py

├── Step 1  Configuration & Reproducibility
├── Step 2  Focal Loss
├── Step 3  Dataset Loading
├── Step 4  Signal Preprocessing
├── Step 5  Label Extraction
├── Step 6  DataLoader Creation
├── Step 7  EEGNet Model
├── Step 8  Training
├── Step 9  Evaluation
└── Step 10 Main Entry Point
```

---

## Installation

```bash
pip install braindecode
pip install mne
pip install torch torchvision torchaudio
pip install scikit-learn
pip install numpy
```

Or:

```bash
pip install braindecode mne torch torchvision torchaudio scikit-learn numpy
```

---

## Future Work

The same preprocessing and evaluation pipeline will be used to compare:

- EEGNet
- EEGFormer
- EEG-BENDR
- BiOT
- DeepConvNet

This ensures fair comparison between architectures using identical preprocessing, windowing, and subject-wise splits.

---

## Author

**Mithun Martin**

B.Tech Computer Science (AI)  
SRM Institute of Science and Technology

Research Area:
EEG-Based Epileptic Seizure Detection using Deep Learning
