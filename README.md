# Modernbert-IDS-OOD-RAG
## OOD-RAG: Open-World Intrusion Detection System

![Python](https://img.shields.io/badge/Python-3.9+-blue.svg)
![PyTorch](https://img.shields.io/badge/PyTorch-2.0+-red.svg)
![License](https://img.shields.io/badge/License-MIT-green.svg)
[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](#)

---

## Overview

**OOD-RAG (Out-of-Distribution Retrieval-Augmented Generation)** is a novel framework for **open-world intrusion detection** that combines:

- ModernBERT as a semantic embedding engine  
- Prototype-based classification for known attack detection  
- FAISS-powered retrieval for local neighborhood analysis  
- Uncertainty-aware decision fusion for robust OOD detection  

Unlike traditional IDS systems that assume all attack types are known during training, **OOD-RAG detects previously unseen (zero-day) attacks with 100% accuracy** while maintaining reasonable performance on known classes.

---

## Key Results

| Model                     | Known Attack Accuracy | Unknown Detection | Overall Accuracy | F1 Macro |
|--------------------------|---------------------|------------------|------------------|----------|
| Random Forest            | 81.5%               | 0.0%             | 81.5%            | 0.855    |
| Logistic Regression      | 80.1%               | 0.0%             | 80.1%            | 0.838    |
| SVM                      | 79.9%               | 0.0%             | 79.9%            | 0.832    |
| **OOD-RAG (Ours)**       | 61.6%               | **100.0%**       | 73.5%            | 0.702    |
| OOD-RAG (Base)           | 53.3%               | 0.0%             | 36.8%            | 0.441    |

---

## Performance Visualizations

### Known Attack Confusion Matrix
![Confusion Matrix]([https://confusion_matrix_known%2520-%2520copia.png](https://github.com/ccaug/Modernbert-IDS-OOD-RAG/blob/main/confusion_matrix_known.png)

Confusion matrix for known attack classification using the finetuned ModernBERT model.

---

### Prototype Similarity Distribution
![Similarity Distribution]([https://distributionscore.png](https://github.com/ccaug/Modernbert-IDS-OOD-RAG/blob/main/distributionscore.png)

Clear separation between:
- Known samples: μ = 0.957, σ = 0.019  
- Unknown samples: μ = 0.667, σ = 0.013  

Threshold: **τ = 0.75**

---

### F1 Score Comparison
![F1 Score]([https://f1score.png](https://github.com/ccaug/Modernbert-IDS-OOD-RAG/blob/main/f1score.png)

Comparison across models for known accuracy and unknown detection.

---

## Architecture
                                   OOD-RAG Framework
                                   ═══════════════

    ┌─────────────┐
    │  Input Log  │
    └──────┬──────┘
           │
           ▼
    ┌─────────────┐
    │ ModernBERT  │
    └──────┬──────┘
           │
           ▼
    ┌─────────────┐
    │  Embedding  │
    │   768-dim   │
    └──────┬──────┘
           │
     ┌─────┴─────┐
     │           │
     ▼           ▼
┌─────────┐ ┌──────────────┐
│Prototype│ │    FAISS     │
│Similarity│ │ Retrieval     │
│         │ │   (k=30)      │
└────┬────┘ └───────┬──────┘
     │              │
     ▼              ▼
┌─────────┐ ┌──────────────┐
│ Global  │ │    Local     │
│  Match  │ │ Consistency  │
└────┬────┘ └───────┬──────┘
     │              │
     │              ▼
     │       ┌──────────────┐
     │       │ Uncertainty  │
     │       │  Estimation  │
     │       └───────┬──────┘
     │              │
     └──────┬───────┘
            ▼
     ┌─────────────┐
     │  Decision   │
     │   Fusion    │
     │ (Rule-based)│
     └──────┬──────┘
            │
            ▼
     ┌─────────────┐
     │  Known?     │
     └──────┬──────┘
            │
      ┌─────┴─────┐
      │           │
      ▼           ▼
┌─────────┐ ┌─────────────┐
│ Known   │ │  UNKNOWN    │
│ Class   │ │  ATTACK     │
└─────────┘ └─────────────┘


---

## Datasets

| Dataset                         | Purpose                         | Samples | Description |
|--------------------------------|----------------------------------|---------|-------------|
| `training_dataset.csv`          | Training & prototype generation | 4,500   | 9 known classes |
| `master_security_dataset.csv`   | RAG index construction          | 356,734 | Mixed logs |
| `sample_security_dataset.csv`   | Unknown test samples            | 807     | 8 novel attacks |
| `UNSW_NB15_testing-set.csv`     | Optional test set               | 82,332  | Multiple categories |

### Known Attack Classes (9)
- DNS Fast-Flux  
- DoS  
- DoS + Brute-Force  
- FTP Brute-Force / Data Exfiltration  
- HTTP C2  
- ICMP Flood  
- IRC C2  
- P2P / UDP Scan  
- Spam  

### Unknown Attack Types (8)
- Anomaly  
- Benign  
- Bot  
- Brute_Force  
- DDoS  
- GoldenEye  
- Portscan  
- Web_Attack  

---

## Getting Started

### Prerequisites

- **Recommended:** Google Colab (GPU)
- **Local setup:**
  - Python 3.9+
  - CUDA GPU (T4 or better)
  - 16GB+ RAM

---

### Installation

```bash
git clone https://github.com/yourusername/ood-rag-ids.git
cd ood-rag-ids
pip install -r requirements.txt
```
Required Files

Upload the following:

training_dataset.csv
master_security_dataset.csv
sample_security_dataset.csv
UNSW_NB15_testing-set.csv (optional)

## Data Format

Each CSV must include:

- `Log` → network log (string)  
- `Attack Type` → label  

### Example
id: 1.234 | dur: 0.001234 | proto: tcp | service: http | state: CON | spkts: 5 | dpkts: 3 | sbytes: 500 | dbytes: 200


---

## Results Interpretation

### OOD-RAG (Finetuned)

| Metric            | Value  |
|------------------|--------|
| Unknown Detection| 100%   |
| Known Accuracy   | 61.6%  |
| F1 Macro         | 0.702  |

---

## Ablation Study

| Configuration      | Known Acc | Unknown Acc | F1 Macro |
|--------------------|----------|-------------|----------|
| Prototype Only     | 62.2%    | 94.8%       | 0.685    |
| + FAISS            | 61.9%    | 97.6%       | 0.694    |
| + Uncertainty      | 61.7%    | 98.8%       | 0.699    |
| **Full Fusion**    | 61.6%    | 100%        | 0.702    |

---

## Inference Performance

| Model                | Time / Sample | Logs/sec |
|---------------------|--------------|----------|
| Random Forest       | 0.06 ms      | ~16,667  |
| Logistic Regression | ~0 ms        | ~250,000 |
| SVM                 | 0.48 ms      | ~2,083   |
| OOD-RAG             | 27.99 ms     | ~36      |

**Note:** Optimized for detection reliability over throughput.

---

## Output Files

| File                              | Description                     |
|-----------------------------------|---------------------------------|
| `ood_final_comparison.csv`         | Model comparison               |
| `ood_unknown_detection.csv`        | Unknown detection per type     |
| `ood_performance_comparison.csv`   | Speed comparison               |
| `ood_research_report.txt`          | Full report                   |
| `ood_confusion_matrix.png`         | Visualization                 |
| `ood_rag_system_[timestamp].zip`   | Full results                  |

---

## Research Summary

**Paper:** *"Detecting the Unknown: RAG-Enhanced Framework for Intrusion Detection"* (NBiS 2026)

### Contributions

- First framework combining **ModernBERT + Prototype Learning + RAG**  
- Achieves **100% unknown attack detection**  
- Includes detailed ablation and trade-off analysis  

### Research Questions

- **RQ1:** Unknown detection? → 100%  
- **RQ2:** Effect of fine-tuning? → Clear separation (Δ = 0.290)  
- **RQ3:** RAG contribution? → Boosts to 100%  
- **RQ4:** Comparison vs traditional? → Traditional = 0% unknown detection  

---
## Contact

- **Oscar G. Lira** — oscargonzalez@galileo.edu  
- **Alberto Marroquin** — amarroquin@galileo.edu  
- **Marco Antonio To** — marcoto@galileo.edu  

**Research Laboratory in Information and Communication Technologies (RLICT)**  
Universidad Galileo, Guatemala  

---

## Acknowledgments

- Hugging Face (Transformers, ModernBERT)  
- FAISS (Similarity Search)  
- Google Colab (GPU resources)  

