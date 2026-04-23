# Region-Aware Mamba Encoding (RAME) with Anatomical Routing and Dual-Section Decoding for Radiology Report Generation

This repository contains my CV project on automated chest X-ray report generation.

- `Report.pdf` is the final report for this project.
- `CXR2_BaselinePaper.pdf` is the baseline paper reference used for comparison.

## Dataset

### Curated CXR Report Generation Dataset

Kaggle link: https://www.kaggle.com/datasets/financekim/curated-cxr-report-generation-dataset

Created by: MedViLL team (`financekim` on Kaggle)

Dataset used in this project (after filtering missing text entries):
- Train: 113,915 samples
- Validation: 15,658 samples
- Test: 32,711 samples

## Project Summary

This project focuses on complete radiology report generation (both Findings and Impression) from chest X-ray images under limited supervision.

The work has two major parts:
1. A faithful re-implementation of a published baseline on the curated dataset.
2. A unified novel architecture proposed by me.

## Notebook Mapping

### `cv-project-absolutecomparison-2.ipynb`

This notebook is my re-implementation of the paper:
"Anatomy-Guided Radiology Report Generation with Pathology-Aware Regional Prompts" (PARP)

In this re-implementation:
- the same core idea is preserved (anatomical guidance + pathology-aware prompting + report decoding) with some limitations based on the new dataset
- training is done on the Curated CXR dataset derived from MIMIC CXR dataset
- adaptations are made for available annotations and hardware limits

### `cv-project-v1 (7).ipynb`

This notebook is my unified novel architecture.

Main idea: combine anatomical routing, efficient sequence modeling, and dual-section generation in one end-to-end model.

## My Unified Novel Architecture

The proposed model has three main contributions:

1. Anatomical Region Router (ARR)
- U-Net + CBAM segments the image into 5 anatomical zones:
	left lung, right lung, heart, mediastinum, diaphragm
- patches are routed according to anatomical region before encoding

2. Region-Aware Mamba Encoder (RAME)
- encodes anatomically routed patch sequences
- uses Mamba SSM for linear-time sequence modeling

3. Dual-Section Decoder
- generates Findings first
- generates Impression conditioned on Findings output
- mirrors real radiologist workflow

## Key Results (from `Report.pdf`)

Test-set metrics reported in the final report:

| Model | Findings BLEU-1 | Findings BLEU-4 | Findings ROUGE-L | Impression BLEU-1 | Impression BLEU-4 | Impression ROUGE-L |
|---|---:|---:|---:|---:|---:|---:|
| PARP (original paper, reference) | 0.3940 | 0.1260 | 0.3020 | N/A | N/A | N/A |
| PARP re-implementation (ours) | 0.2100 | 0.0800 | 0.2400 | 0.2300 | 0.0900 | 0.2500 |
| Ours (RAME + ARR + Dual) | 0.5738 | 0.2617 | 0.5865 | 0.6098 | 0.3976 | 0.7751 |

## Implementation Notes

- Both notebooks are Kaggle-style, notebook-first implementations.
- Baseline re-implementation and proposed model are compared under the same curated dataset setting.
- Some differences from the original PARP paper are expected due to annotation availability and compute constraints.

## How To Run

1. Open notebook in VS Code Jupyter or Kaggle.
2. Enable GPU runtime.
3. Update dataset paths (`BASE_PATH`, `IMG_BASE`, `MIMIC_PATH`, `SAVE_PATH`).
4. Run cells from top to bottom.

### Run Without Training From Scratch (Using Provided Checkpoints)

If the evaluator only wants inference/evaluation, use these checkpoint files:

- Novel architecture checkpoint: `best_model (2).pt`
- Paper re-implementation checkpoint: `paper_model_best (2).pt`

#### In Kaggle

1. Upload/attach the `.pt` files as Notebook Inputs (Dataset/Model input).
2. Set the checkpoint path in the notebook to the attached input location (usually under `/kaggle/input/...`).
3. Skip long training cells and run only model-build + checkpoint-load + inference/evaluation cells.

#### In Local VS Code Jupyter

1. Keep both `.pt` files in the repo folder.
2. Load checkpoints with the local relative filenames above.
3. Skip training cells and run evaluation/qualitative generation cells.



Recommended order:
1. `cv-project-absolutecomparison-2.ipynb` (baseline re-implementation)
2. `cv-project-v1 (7).ipynb` (my unified novel architecture)

