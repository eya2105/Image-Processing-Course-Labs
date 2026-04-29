#  TP4 — Segmentation d'Images

**Image segmentation pipeline** applied to four categories of images using classical computer vision techniques — implemented entirely from scratch in Python/OpenCV as part of a university lab.

---

## Overview

This notebook covers a complete image processing and segmentation pipeline in two parts:

**Part I — Classic techniques on test images** (`outil.png`, `feuille.png`, `mandrillgray.bmp`)
- Histogram analysis & CLAHE contrast enhancement
- Gaussian vs. mean smoothing filters
- Sobel gradient computation
- Edge detection: gradient thresholding vs. Canny
- Hough line transform
- Thresholding (manual & Otsu), K-Means, Region Growing

**Part II — Full pipeline on 4 real datasets**
- Preprocessing: CLAHE, Gaussian, Median, Bilateral filters
- Segmentation: Otsu, Adaptive thresholding, K-Means (k=2, k=3)
- Quantitative evaluation: **Jaccard Index** and **Dice coefficient**
- Systematic comparison across all method × preprocessing combinations

---

## Datasets

| Category | Description | Difficulty |
|----------|-------------|------------|
| `1objet` | Single object on uniform background | Easy |
| `2objets` | Two distinct objects | Medium |
| `textfort` | Strongly textured background | Medium |
| `Embryons` | Biological embryo images, low contrast | Hard |

Each category contains **10 image / ground-truth mask pairs**.

---

## Methods

### Preprocessing
| Method | Use case |
|--------|----------|
| None (baseline) | Clean, well-contrasted images |
| CLAHE | Low global contrast, uneven lighting |
| Gaussian filter | Gaussian noise reduction |
| Median filter | Salt-and-pepper noise, textured backgrounds |
| Bilateral filter | Edge-preserving smoothing |

### Segmentation
| Method | Family |
|--------|--------|
| Otsu thresholding | Global threshold |
| Adaptive thresholding | Local threshold |
| K-Means (k=2) | Unsupervised clustering |
| K-Means (k=3) | Unsupervised clustering (3 classes) |
| Region Growing | Region-based |

### Evaluation Metrics
- **Jaccard Index (IoU)** = |Pred ∩ GT| / |Pred ∪ GT|
- **Dice Coefficient** = 2 × |Pred ∩ GT| / (|Pred| + |GT|)

---

## Results

### Best configuration per category

| Category | Best Method | Best Preprocessing | Jaccard | Dice |
|----------|------------|-------------------|---------|------|
| 1objet | Otsu | Gaussian | 0.813 | **0.896** |
| 2objets | Otsu | None | 0.901 | **0.943** |
| Embryons | KMeans-3 | CLAHE | 0.208 | **0.344** |
| textfort | Otsu | Median | 0.764 | **0.841** |

### Average Dice by method (all preprocessings)

| Category | Otsu | KMeans-2 | KMeans-3 | Adaptive |
|----------|------|----------|----------|----------|
| 1objet | 0.879 | 0.879 | 0.793 | 0.585 |
| 2objets | 0.933 | 0.933 | 0.927 | 0.860 |
| Embryons | 0.335 | 0.335 | 0.340 | 0.323 |
| textfort | 0.780 | 0.780 | 0.718 | 0.540 |

---

## Setup & Usage

### Run on Kaggle (recommended)
1. Import the notebook into [Kaggle](https://www.kaggle.com)
2. Add the dataset and the images 
3. Run all cells

### Run locally
```bash
pip install numpy pandas matplotlib opencv-python scikit-image scikit-learn Pillow scipy
jupyter notebook Image Segmentation-Preprocessing-Segmentation Methods.ipynb
```
> Adjust image paths at the top of the notebook to point to your local files.

---

## Key Findings

1. **Otsu ≈ KMeans-2** on grayscale images — they converge to the same threshold when k=2.
2. **Preprocessing matters**: Gaussian filter improves Dice by ~1% on `1objet`; Median filter is most effective on `textfort`.
3. **CLAHE can hurt**: on images with already-uniform backgrounds, CLAHE amplifies local artifacts that confuse Otsu's global threshold.
4. **Adaptive thresholding underperforms** consistently — it over-segments images whose backgrounds are globally uniform.
5. **Embryons are unsolvable with classical methods** (best Dice ≈ 0.34). The structural similarity between embryo interior and background requires supervised deep learning approaches (U-Net, etc.).

---

## Technologies

![Python](https://img.shields.io/badge/Python-3.10-blue)
![OpenCV](https://img.shields.io/badge/OpenCV-4.x-green)
![scikit-image](https://img.shields.io/badge/scikit--image-0.21-orange)
![scikit-learn](https://img.shields.io/badge/scikit--learn-1.x-orange)
![Kaggle](https://img.shields.io/badge/Platform-Kaggle-20BEFF)


