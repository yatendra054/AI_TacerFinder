# Scanner Device Identification from Scanned Documents

## Project Overview
This project aims to identify the **scanner brand/model** used to digitize documents by analyzing **scanner-specific noise patterns, textures, and artifacts** present in scanned images.

Unlike traditional document analysis, this work focuses on **hardware-level signatures** rather than document content (text, layout, etc.), making it useful for **digital forensics and document verification**.

---

## Dataset Description

### Scanned Document Dataset
- **Total Images:** 600+ (extended to ~1000 during experiments)
- **Resolution:** 300 DPI  
- **Image Size:** ~2480 × 3508 pixels (A4 at 300 DPI)  
- **Format:** `.tif` (TIFF – lossless)
- **Color Modes:** RGB & Grayscale  
- **Bit Depth:** 8-bit per channel  

### Scanner Brands
- Canon  
- Epson  
- HP  

### Directory Structure
dataset/
│
├── Canon/
│ └── s2_25.tif
├── Epson/
│ └── s8_47.tif
└── Hp/
└── s11_3.tif


### Label Mapping Example

| Image Path           | Label  |
|----------------------|--------|
| `./Canon/s2_25.tif`  | canon  |
| `./Epson/s8_47.tif`  | epson  |
| `./Hp/s11_3.tif`     | hp     |

---

Each image was analyzed to ensure dataset consistency:

- ✔ Resolution verification (~2480 × 3508)
- ✔ DPI validation (300 DPI)
- ✔ Format confirmation (TIFF)
- ✔ Color channels (RGB / Grayscale)
- ✔ Bit depth (8-bit)

---


### Steps

#### 1. Image Resizing
- Resized to **224 × 224**
- Ensures compatibility with deep learning models

#### 2. Grayscale Conversion
- Converted from **RGB → Grayscale**
- Reduces complexity (3 channels → 1)

#### 3. Final Format
- Converted to NumPy arrays  
- Final shape: (224, 224, 1)


### Benefits
- Faster training  
- Lower memory usage  
- Focus on **texture & noise patterns**

---

### Objective
Extract **scanner-specific features** independent of document content.

### Features Used

#### 1. Local Binary Patterns (LBP)
- Parameters: **P = 8, R = 1**
- Extracts **micro-texture patterns**
- Outputs normalized histogram

#### 2. Noise Pattern Analysis
- Applied smoothing to suppress content
- Extracted **sensor noise residuals**

#### 3. Frequency Domain Features (FFT)
- Identifies **periodic scanner artifacts**
- Captures frequency-based noise signatures

---


### Models Tested
- Logistic Regression  
- Support Vector Machine (SVM)  
- Random Forest  

### Best Model
- **SVM (RBF Kernel)**

### Performance
- **Accuracy: ~91–92%**

### Why SVM?
- Handles **non-linear boundaries**
- Works well with **LBP features**
- Robust to noise
- Strong generalization on medium datasets

---


### CNN (From Scratch)

#### Setup
- Custom CNN architecture
- Grayscale input
- Basic augmentation

#### Results
- Training Accuracy: ~55%  
- Validation Accuracy: ~40%  
- Test Accuracy: ~40%  

#### Issue
- **Underfitting**
- Insufficient data for deep learning

---

### Transfer Learning (MobileNetV2)

#### ⚙️ Configuration
- Pre-trained on ImageNet
- Input: 224 × 224 × 3
- Fine-tuned upper layers

####  Augmentation
- Small rotations (±2–3°)
- Brightness variation

#### Results
- Training Accuracy: ~82%  
- Validation Accuracy: ~84%  
- Test Accuracy: ~86%  

#### Conclusion
- Transfer learning significantly improved performance
- Learned meaningful scanner-specific patterns

---


### Metrics Used
- Accuracy  
- F1 Score  
- Confusion Matrix  

---

### Grad-CAM Explainability

#### Purpose
Understand **what the model is focusing on**

#### Observations
- Focus on:
  - Background regions  
  - Paper texture  
  - Noise artifacts  
- Minimal focus on text  

#### Insight
Model successfully learned **scanner-specific features**, not document content.

---

## Streamlit Web Interface

### Overview
A simple UI built with **Streamlit** allows users to upload scanned images and get predictions instantly.

---

### Workflow
1. Upload image (`.jpg`, `.png`, `.tif`)
2. Apply **same preprocessing pipeline**
3. Extract **LBP features**
4. Predict using **SVM model**
5. Display result

---

### Key Design Decision
To ensure accuracy:
- Reused the **exact preprocessing function**
- Avoided reimplementation inconsistencies

---

### TIFF Handling
- Supports high-resolution images
- Handles:
  - DPI differences  
  - Bit depth variations  
- Ensures correct visualization without affecting prediction

---

### Output
- Uploaded image preview  
- Predicted scanner brand (Canon / Epson / HP)

---

## Final Results Summary

| Approach                     | Accuracy |
|----------------------------|----------|
| CNN (from scratch)         | ~40%     |
| MobileNetV2 (Transfer)     | ~86%+    |
| LBP + SVM (Classical ML)   | ~92%+    |

---

##  Key Takeaways

- Hand-crafted features outperform deep learning on **small datasets**
- Scanner identification relies on:
  - Texture  
  - Noise  
  - Frequency artifacts  
- Transfer learning is effective but still slightly behind classical ML here
- Explainability confirms **correct learning behavior**

---

## Future Work

- Increase dataset size (multi-device, multi-resolution)
- Explore:
  - CNN + handcrafted feature fusion
  - Vision Transformers (ViT)
- Fine-grained classification (model-level, not just brand)
- Real-time forensic applications

---

## Tech Stack

- Python  
- OpenCV  
- NumPy  
- Scikit-learn  
- TensorFlow / Keras  
- Streamlit  

---

## Conclusion

This project demonstrates that **scanner devices leave unique digital fingerprints** in scanned documents. By leveraging **texture, noise, and frequency-based features**, it is possible to accurately identify the source scanner—making this approach highly valuable in **digital forensics and document authentication**.
