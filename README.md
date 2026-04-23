# Region-Aware Mamba Encoding (RAME) for Radiology Report Generation

This repository contains my computer vision project on generating radiology reports directly from chest X-ray images.

The goal was to move beyond basic captioning and generate **complete reports**, including both *Findings* and *Impression*, similar to how radiologists actually write them.

---

## Project Files

- `Report.pdf` → Final project report (recommended read)
- `CXR2_BaselinePaper.pdf` → Baseline paper used for comparison

---

## Dataset

**Curated CXR Report Generation Dataset**  
Kaggle: https://www.kaggle.com/datasets/financekim/curated-cxr-report-generation-dataset  

After filtering out incomplete entries:

- Train: 113,915 samples  
- Validation: 15,658 samples  
- Test: 32,711 samples  

This dataset is derived from MIMIC-CXR and provides paired chest X-ray images with corresponding *Findings* and *Impression* sections.

---

## What this project does

This project focuses on generating **full radiology reports** from chest X-rays under limited supervision.

It has two main parts:

1. Re-implementing an existing research paper under dataset and compute constraints  
2. Designing and evaluating a new architecture that improves on those limitations  

---

## Notebooks

### `cv-project-absolutecomparison-2.ipynb`

This notebook contains my re-implementation of the paper:

> *Anatomy-Guided Radiology Report Generation with Pathology-Aware Regional Prompts (PARP)*

Since the original dataset annotations weren’t available, a few adjustments were made:
- region detection is approximated instead of using bounding boxes  
- lesion prompts are generated from classification outputs  
- training is done on the curated dataset instead of full MIMIC  

The idea of the original paper is preserved, but adapted to a more constrained setup.

---

### `cv-project-v1 (7).ipynb`

This is my main model.

Here, I tried to rethink the pipeline and combine:
- anatomical structure
- efficient sequence modeling
- and realistic report generation

into a single end-to-end system.

---

## Proposed Architecture (High-level)

The model is built around three main ideas:

### 1. Anatomical Region Router (ARR)
- Uses U-Net + CBAM to segment the image into 5 regions  
  *(lungs, heart, mediastinum, diaphragm)*
- Each patch is associated with its anatomical region before encoding  

---

### 2. Region-Aware Mamba Encoder (RAME)
- Processes patch sequences using Mamba (state space model)
- Handles long sequences efficiently (linear complexity)
- Incorporates anatomical information through region embeddings  

---

### 3. Dual-Section Decoder
- First generates **Findings**
- Then generates **Impression**, conditioned on Findings  
- Designed to better reflect how reports are written in practice  

---

## Results (from Report)

Performance on the test set:

| Model | Findings BLEU-1 | Findings BLEU-4 | Findings ROUGE-L | Impression BLEU-1 | Impression BLEU-4 | Impression ROUGE-L |
|------|----------------|----------------|------------------|------------------|------------------|------------------|
| PARP (original, reference) | 0.3940 | 0.1260 | 0.3020 | N/A | N/A | N/A |
| PARP (re-implementation) | 0.2100 | 0.0800 | 0.2400 | 0.2300 | 0.0900 | 0.2500 |
| Ours (RAME + ARR + Dual) | 0.5738 | 0.2617 | 0.5865 | 0.6098 | 0.3976 | 0.7751 |

---

## Implementation Notes

- Both models are implemented as notebook-based pipelines (Kaggle-style)
- The baseline and proposed model are trained and evaluated under the same dataset setup
- Differences from the original paper are mainly due to:
  - lack of bounding-box annotations  
  - limited compute resources  

---

## How to Run

1. Open the notebooks in **Kaggle** or **VS Code (Jupyter)**
2. Enable GPU runtime
3. Update dataset paths:
   - `BASE_PATH`
   - `IMG_BASE`
   - `MIMIC_PATH`
   - `SAVE_PATH`
4. Run cells sequentially

---

## Suggested Order

1. Start with:  
   `cv-project-absolutecomparison-2.ipynb`  
   (baseline re-implementation)

2. Then move to:  
   `cv-project-v1 (7).ipynb`  
   (proposed model)

---

## Final Note

This project was done under practical constraints (dataset + compute), so the focus was on:
- making the system work end-to-end  
- keeping the design interpretable  
- and evaluating it fairly against a baseline  

For full details, refer to `Report.pdf`.