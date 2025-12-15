# ECE-GY-6143-Machine-Learning-Project

A machine learning project that detects **network intrusions** by classifying network traffic flows into **Benign** (normal) or one of **8 attack types** using classic ML models and gradient boosting.

## Group Members

Yunfei Quan，New York University - Tandon School of Engineering

Peifeng Yao，New York University - Tandon School of Engineering

Yulong Wang，New York University - Tandon School of Engineering

Ze Xu，New York University - Tandon School of Engineering

Shubo Wang，New York University - Tandon School of Engineering


## Repository Structure
```
Machine Learning Project/
├── README.md
├── ML Project/
   └── ids_analysis_no_svm.ipynb
   └── Project_report.pdf
   └── Project_presentaion.pptx
   └── dataset.csv.zip
   └── ids_analysis_no_svm.pdf
├──presentation.mp4
```
## Description

Network attacks are often disguised as normal traffic. Traditional signature-based IDS systems struggle with:
- **Zero-day attacks**
- New variants that don’t match known patterns

This project treats intrusion detection as a **supervised multi-class classification** problem:

- **Input:** a network *flow* represented by dozens of numeric features (timing, packet stats, protocol flags, etc.)
- **Output:** one of 9 labels (Benign + 8 attacks)

## Dataset

This project is based on **CSE-CIC-IDS2018**, a widely used intrusion detection benchmark dataset created by CIC (Canadian Institute for Cybersecurity) and CSE (Communications Security Establishment).

### What we actually used in this repo
To make model comparison fair and training faster, we used a **cleaned and balanced subset**:

- **360,000 samples total**
- **9 classes**
- **40,000 samples per class**
- Features were extracted with **CICFlowMeter-V3** (flow-based features)

## Attack Labels (9 classes)

- Benign
- Bot
- DDoS-HOIC
- DoS-GoldenEye
- DoS-Hulk
- DoS-SlowHTTPTest
- FTP-BruteForce
- Infiltration
- SSH-BruteForce

## Method Overview

### 1) Data checks
- No missing values
- No infinite values

### 2) Train/Test split
- **70% training** (252,000 samples)
- **30% testing** (108,000 samples)
- **Stratified sampling** to preserve class proportions

### 3) Feature scaling
We standardize each feature to:
- mean = 0
- std = 1

### 4) Dimensionality analysis (PCA)
PCA helps estimate how many dimensions really matter:
- ~**23** principal components capture **95%** variance
- ~**19** components capture **90%** variance
- First 2 components explain ~**34.09%** variance

### 5) Model training & evaluation
We trained and evaluated multiple model families:
- Logistic Regression (L1 / L2)
- SVM (RBF) *(optional; may be excluded in the “no_svm” notebook)*
- Random Forest
- Neural Network (MLP)
- XGBoost
- LightGBM

Metrics reported:
- Accuracy
- Precision
- Recall
- F1-score (weighted for multi-class)

## Results

### Overall performance
Gradient boosting methods perform best, with **LightGBM** ranking #1.

| Model | Train Acc | Test Acc | Precision | Recall | F1-score |
|------|----------:|---------:|----------:|-------:|--------:|
| Logistic Regression (L2) | 0.8515 | 0.8500 | 0.8562 | 0.8500 | 0.8456 |
| Logistic Regression (L1) | 0.8423 | 0.8410 | 0.8439 | 0.8410 | 0.8365 |
| SVM (RBF) | 0.8613 | 0.8599 | 0.8884 | 0.8599 | 0.8501 |
| Random Forest | 0.9064 | 0.8852 | 0.9039 | 0.8852 | 0.8797 |
| Neural Network (MLP) | 0.8741 | 0.8726 | 0.8844 | 0.8726 | 0.8689 |
| XGBoost | 0.9015 | 0.8886 | 0.8957 | 0.8886 | 0.8865 |
| **LightGBM** | **0.9175** | **0.9127** | **0.9243** | **0.9127** | **0.9102** |

**Key takeaway:** LightGBM reached **91.27%** test accuracy and **0.9102** F1-score.

## Per-Attack-Type Detection

| Class | Attack Type | Recall | Notes |
|------:|------------|------:|------|
| 0 | Benign | 0.81 | ~19% benign traffic incorrectly flagged as attacks |
| 1 | Bot | 1.00 | very distinctive patterns |
| 2 | DDoS-HOIC | 1.00 | high-volume attacks are easy to detect |
| 3 | DoS-GoldenEye | 1.00 | strong signature in flow statistics |
| 4 | DoS-Hulk | 1.00 | strong signature in flow statistics |
| 5 | DoS-SlowHTTPTest | **0.54** | hardest: “low-and-slow” attacks resemble legitimate long connections |
| 6 | FTP-BruteForce | 0.95 | strong but not perfect |
| 7 | Infiltration | 0.92 | stealthy behavior can mimic normal traffic |
| 8 | SSH-BruteForce | 1.00 | clear authentication patterns |

## Reference
### Dataset:
CSE-CIC-IDS2018: https://www.unb.ca/cic/datasets/ids-2018.html

CSE-CIC-IDS2018_Cleaned_40k-samples-each: https://www.kaggle.com/datasets/hunglevinh/cse-cic-ids2018-cleaned-40k-samples-each/data

