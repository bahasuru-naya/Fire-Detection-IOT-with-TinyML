# 🔥 Fire-Detection-IOT-with-TinyML — Dataset & ML Model

> A custom dataset and lightweight neural network for real-time fire detection, optimized for edge deployment on NodeMCU ESP32.

[![Accuracy](https://img.shields.io/badge/Accuracy-96.49%25-brightgreen)](#results)
[![TFLite Accuracy](https://img.shields.io/badge/TFLite%20Accuracy-96.08%25-green)](#tflite-deployment)
[![Model Size](https://img.shields.io/badge/Model%20Size-0.03%20MB-blue)](#model-architecture)
[![Dataset](https://img.shields.io/badge/Dataset-6000%20records-lightgrey)](#dataset)

---

## 📋 Table of Contents

- [Dataset](#dataset)
- [Data Preprocessing](#data-preprocessing)
- [Model Architecture](#model-architecture)
- [Training Strategy](#training-strategy)
- [Results](#results)
- [TFLite Deployment](#tflite-deployment)
- [Kaggle Resources](#kaggle-resources)


---

## Dataset

A custom dataset was collected using an ESP32-based sensor setup in outdoor environments, capturing both fire and non-fire conditions under varying temperature, humidity, and gas concentration levels.

| Property | Value |
|----------|-------|
| Total records | 6,000 |
| Input features | 6 |
| Target classes | 2 (FIRE / SAFE) |
| Collection method | ESP32 → Firebase cloud storage |
| Labeling | Manual switch (binary label) |

**Sensors used for data collection:**

| Sensor | Parameters Captured |
|--------|---------------------|
| ENS160 + AHT21 | eCO2, TVOC, temperature, humidity |
| BMP180 | Atmospheric pressure |
| MQ8 | Hydrogen gas concentration |
| MQ3 | Ethanol/alcohol vapor |

**Input features:**

- `eCO2` — equivalent CO2 concentration
- `humidity` — relative humidity (%)
- `pressure` — atmospheric pressure
- `raw_ethanol` — ethanol vapor concentration
- `raw_h2` — hydrogen gas concentration
- `temperature` — ambient temperature (°C)

---

## Data Preprocessing

- **Train/test split:** 80% training, 20% testing (fixed random state for reproducibility)
- **Normalization:** `StandardScaler` applied to all features
- **Class imbalance:** Handled using inverse-frequency class weighting to prevent bias toward the majority class

---

## Model Architecture

A compact sequential neural network was designed with embedded deployment constraints in mind:

```
Input Layer       →  6 features
Hidden Layer      →  8 neurons, ReLU activation, L2 regularization
BatchNorm Layer   →  Stabilizes learning dynamics
Dropout Layer     →  Reduces overfitting
Output Layer      →  1 neuron, Sigmoid activation (binary classification)
```

| Parameter | Value |
|-----------|-------|
| Loss function | Binary cross-entropy |
| Optimizer | Adam |
| Final model size | 0.03 MB |

---

## Training Strategy

| Parameter | Value |
|-----------|-------|
| Max epochs | 100 |
| Batch size | 32 |
| Validation split | 20% |
| Early stopping | Enabled — monitors `val_loss`, restores best weights |
| Learning rate reduction | Halved when `val_loss` plateaus |
| Class weighting | Dynamic inverse-frequency weighting |

---

## Results

### Accuracy Comparison (vs. 11 ML algorithms)

| Model | Accuracy | Log Loss | Size (MB) |
|-------|----------|----------|-----------|
| CatBoost | 99.51% | 0.0295 | 1.04 |
| LightGBM | ~99% | ~0.03 | moderate |
| Random Forest | ~99% | competitive | 1.08 |
| XGBoost | ~99% | ~0.03 | 0.14 |
| Decision Tree | 99.10% | moderate | compact |
| **Custom NN (ours)** | **96.49%** | **0.1131** | **0.03** |
| MLP | < 96.49% | elevated | ~0.03 |
| KNN | moderate | elevated | — |
| Logistic Regression | moderate | — | minimal |
| SVC | lowest | — | — |
| AdaBoost | — | highest | compact |
| Gradient Boosting | — | — | moderate |

### Why the custom model was selected

Although ensemble methods such as CatBoost (99.51%) and Random Forest (~99%) achieved higher accuracy, they were not viable for deployment:

- **CatBoost** requires 1.04 MB and **Random Forest** requires 1.08 MB — both exceed ESP32 memory limits
- The custom model at **0.03 MB** represents a **97% size reduction** vs. CatBoost
- **96.49% accuracy** satisfies the practical threshold of >95% for fire safety applications
- **Log loss of 0.1131** supports reliable confidence-based threshold decisions, outperforming KNN, MLP, and AdaBoost

---

## TFLite Deployment

The trained model was converted to **TensorFlow Lite** format for on-device inference:

| Metric | Value |
|--------|-------|
| TFLite accuracy | 96.08% |
| Accuracy drop post-conversion | ~0.41% |
| Inference target | NodeMCU ESP32 |
| Internet dependency | None (fully on-device) |

Conversion confirmed that quantization did not cause significant performance degradation.

---

## Kaggle Resources

| Resource | Link |
|----------|------|
| 📊 Dataset | [View on Kaggle]([https://www.kaggle.com](https://www.kaggle.com/datasets/bahasurunayanakantha/manual-fire-dataset)) |
| 🧠 Model Training Notebook | [View on Kaggle]([https://www.kaggle.com](https://www.kaggle.com/code/bahasurunayanakantha/fire-detection-iot-with-tinyml)) |

---



---

> **Index Terms:** Fire detection · IoT sensors · Machine learning · Neural network · TensorFlow Lite · Edge computing
