# Machine Learning Intrusion Detection System

## Project Overview

This project develops and evaluates a machine learning-based intrusion detection system using the UNSW-NB15 cybersecurity dataset.

The project compares XGBoost and PyTorch neural network models for classifying network traffic as either NORMAL or ATTACK.

The final system uses an XGBoost classifier with a complete preprocessing and prediction pipeline that can process raw network traffic records and produce an attack probability and security classification.

## Project Goals

- Explore and analyze the UNSW-NB15 network traffic dataset.
- Prepare categorical and numerical network features for machine learning.
- Train an XGBoost intrusion detection model.
- Train a PyTorch neural network for comparison.
- Compare the performance of the machine learning models.
- Analyze detection performance across different attack categories.
- Evaluate false positives and false negatives.
- Analyze how the decision threshold affects security performance.
- Build a reusable intrusion detection prediction pipeline.
- Validate the saved model using raw network traffic records.

## Dataset

This project uses the UNSW-NB15 network intrusion detection dataset.

The dataset contains both normal network traffic and malicious network traffic. Each network record contains features describing characteristics such as protocol, service, connection state, packet counts, byte counts, and time-to-live (TTL) values.

For this project, the primary classification target is:

- `0` = NORMAL traffic
- `1` = ATTACK traffic

The dataset contains multiple attack categories, including:

- Fuzzers
- Analysis
- Backdoor
- DoS
- Exploits
- Generic
- Reconnaissance
- Shellcode
- Worms

The test dataset used for final model evaluation contains 82,332 network records:

- 37,000 normal records
- 45,332 attack records

## Project Structure

The project is organized into separate directories for data, trained models, notebooks, results, and source code.

```text
cyber_ml_project/
│
├── data/
│   └── processed/
│
├── models/
│
├── notebooks/
│   ├── 01_environment_test.ipynb
│   ├── 02_data_exploration.ipynb
│   ├── 03_data_preprocessing.ipynb
│   ├── 04_xgboost_baseline.ipynb
│   ├── 05_pytorch_neural_network.ipynb
│   ├── 06_model_comparison_and_tuning.ipynb
│   ├── 07_intrusion_detection_pipeline.ipynb
│   ├── 08_model_visualization_and_report.ipynb
│   └── 09_final_project_validation.ipynb
│
├── results/
├── src/
└── README.md

## Machine Learning Workflow

The project was developed through a series of Jupyter notebooks, with each notebook representing a stage of the machine learning workflow.

### 1. Environment Setup
Verified the Python environment and required machine learning libraries.

### 2. Data Exploration
Explored the UNSW-NB15 dataset, network features, class labels, and attack categories.

### 3. Data Preprocessing
Prepared the network traffic data for machine learning by processing categorical and numerical features and creating the training and testing datasets.

### 4. XGBoost Baseline Model
Trained and evaluated an XGBoost classifier to establish baseline intrusion detection performance.

### 5. PyTorch Neural Network
Built and evaluated a neural network using PyTorch as an alternative intrusion detection model.

### 6. Model Comparison and Tuning
Compared the XGBoost and PyTorch models and selected XGBoost for the final intrusion detection system.

### 7. Intrusion Detection Pipeline
Built a reusable prediction pipeline that converts raw network records into model-ready features and produces an attack probability and final NORMAL or ATTACK classification.

### 8. Model Visualization and Analysis
Analyzed model performance using confusion matrices, ROC and precision-recall curves, feature importance, attack-category detection rates, prediction probabilities, and decision-threshold analysis.

### 9. Final Project Validation
Reloaded the saved model and preprocessing components and verified that the complete pipeline could reproduce the saved results and classify raw network traffic records.

## Final Model Performance

The final XGBoost intrusion detection model was evaluated on 82,332 test network records using a decision threshold of 0.50.

| Metric | Result |
|---|---:|
| Accuracy | 87.30% |
| Precision | 82.18% |
| Recall | 98.23% |
| F1 Score | 89.49% |
| ROC-AUC | 98.31% |

### Confusion Matrix Results

| Result | Records |
|---|---:|
| True Negatives | 27,343 |
| False Positives | 9,657 |
| False Negatives | 803 |
| True Positives | 44,529 |

The model correctly detected 44,529 of the 45,332 attack records in the test dataset.

The high recall of 98.23% indicates that the model successfully identified most malicious network traffic. However, the model also generated 9,657 false positives, demonstrating the tradeoff between detecting as many attacks as possible and incorrectly flagging normal traffic.

## Security Threshold Analysis

The model's decision threshold determines the attack probability required for network traffic to be classified as an ATTACK.

Two thresholds were examined in detail:

| Metric | Threshold 0.50 | Threshold 0.90 |
|---|---:|---:|
| Precision | 82.18% | 96.67% |
| Recall | 98.23% | 90.29% |
| F1 Score | 89.49% | 93.37% |
| False Positives | 9,657 | 1,409 |
| False Negatives | 803 | 4,402 |

Increasing the threshold from 0.50 to 0.90 reduced false positives by 8,248, but resulted in 3,599 additional attacks being missed.

Although the 0.90 threshold produced a higher F1 score, the final intrusion detection pipeline retains the 0.50 threshold. For this project, detecting malicious traffic and minimizing missed attacks were prioritized over reducing false-positive alerts.

This demonstrates an important security tradeoff: increasing the threshold can reduce unnecessary alerts, but it can also allow more attacks to go undetected.

## Attack Category Detection

The model's ability to detect attacks was also evaluated across the different UNSW-NB15 attack categories.

| Attack Category | Total | Detected | Missed | Detection Rate |
|---|---:|---:|---:|---:|
| Backdoor | 583 | 583 | 0 | 100.00% |
| Worms | 44 | 44 | 0 | 100.00% |
| Generic | 18,871 | 18,870 | 1 | 99.99% |
| DoS | 4,089 | 4,087 | 2 | 99.95% |
| Reconnaissance | 3,496 | 3,493 | 3 | 99.91% |
| Exploits | 11,132 | 11,069 | 63 | 99.43% |
| Shellcode | 378 | 374 | 4 | 98.94% |
| Analysis | 677 | 632 | 45 | 93.35% |
| Fuzzers | 6,062 | 5,377 | 685 | 88.70% |

The model achieved very high detection rates for most attack categories. Backdoor and Worm attacks were detected at 100%, while Generic, DoS, Reconnaissance, Exploits, and Shellcode attacks were detected at rates above 98%.

Fuzzers were the most difficult attack category for the model to identify. Of 6,062 Fuzzer attacks, 5,377 were detected and 685 were incorrectly classified as normal traffic.

This analysis shows that overall recall alone does not describe the complete performance of an intrusion detection system. Examining individual attack categories can reveal specific types of malicious traffic that are more difficult for the model to detect.

## Feature Importance Analysis

Feature importance was analyzed to understand which network traffic characteristics had the greatest influence on the XGBoost model's predictions.

The three most important features were:

1. `is_sm_ips_ports` - 53.49%
2. `sttl` - 20.37%
3. `ct_state_ttl` - 13.26%

Together, these three features accounted for most of the model's feature importance.

The `sttl` feature represents the source time-to-live (TTL) value of a network connection. Analysis of the test data showed that STTL values differed substantially between normal and attack traffic. An STTL value of 254 appeared frequently in attack traffic and was also present in many false-positive predictions.

This indicates that the model relies heavily on TTL and related connection characteristics when distinguishing normal traffic from malicious traffic. Feature importance therefore helps explain why certain network records may be classified as attacks even when they are actually normal.

## Model Validation

The final saved intrusion detection system was tested to verify that the complete machine learning pipeline could be loaded and used on raw network traffic records.

The validation process confirmed that:

- All required project files were available.
- The saved XGBoost model loaded successfully.
- The saved preprocessing pipeline loaded successfully.
- The saved scaler loaded successfully.
- The deployment configuration loaded successfully.
- The model reproduced the previously saved evaluation metrics.
- Raw network traffic records could be transformed into the required 194 model input features.

Two raw records were then tested through the complete prediction pipeline.

### Attack Test

A raw Backdoor attack record was processed by the saved pipeline.

- Actual classification: ATTACK
- Predicted classification: ATTACK
- Attack probability: 99.59%

### Normal Traffic Test

A raw normal network record was also processed.

- Actual classification: NORMAL
- Predicted classification: NORMAL
- Attack probability: 40.04%

These tests confirmed that the saved model can accept raw network traffic data, apply the required preprocessing and scaling steps, and produce a final intrusion detection prediction.

## Key Findings

The project produced several important findings:

- XGBoost achieved 87.30% accuracy and 98.23% recall on the test dataset.
- The model correctly detected 44,529 of 45,332 attack records.
- Only 803 attacks were missed at the selected 0.50 decision threshold.
- Fuzzers were the most difficult attack category, with a detection rate of 88.70%.
- Backdoor and Worms attacks achieved 100% detection in the test dataset.
- The model generated 9,657 false-positive alerts, showing the tradeoff between high attack detection and alert volume.
- Increasing the decision threshold to 0.90 greatly reduced false positives but also increased missed attacks.
- TTL and connection-related features were among the strongest influences on model predictions.
- The complete saved pipeline successfully processed raw network traffic and produced intrusion detection predictions.

## Conclusion

This project demonstrated the development of an end-to-end machine learning intrusion detection system using the UNSW-NB15 cybersecurity dataset.

The project covered data exploration, preprocessing, XGBoost and PyTorch model development, model comparison, intrusion detection analysis, threshold evaluation, model persistence, and end-to-end validation.

XGBoost was selected for the final system and achieved a recall of 98.23% and ROC-AUC of 98.31%. The final decision threshold of 0.50 was retained because the primary security objective was to detect as many attacks as possible while minimizing false negatives.

The final validation demonstrated that the trained model and preprocessing components could be saved, reloaded, and used to classify raw network traffic as NORMAL or ATTACK.

Overall, the project demonstrates how machine learning can support network intrusion detection while also showing the importance of evaluating false positives, false negatives, attack-specific performance, feature importance, and decision thresholds when applying machine learning to cybersecurity.

