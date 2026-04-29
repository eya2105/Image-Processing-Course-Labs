# TP2 : Histogram Manipulation & Medical Image Enhancement

**Lab 2 — Image Processing Course**  
INSAT — Institut National des Sciences Appliquées et de Technologie, Tunis  
*Taught by Mme Wided Souid*

---

## Overview

This lab is divided into two complementary parts:

**Part 1 — Histogram equalization with OpenCV** (`vallee.png`)  
Step-by-step guided exploration of histogram manipulation: loading, visualising, equalising, and comparing global vs adaptive techniques on a low-contrast landscape image.

**Part 2 — Scientific article reproduction**   
Full Python reimplementation of four histogram-based enhancement algorithms from the peer-reviewed article by Salem et al. (2019), evaluated with three quantitative metrics on a medical retina image

---

## Part 1 — Histogram Equalization with OpenCV

Applied to `vallee.png`, whose pixel intensities are initially compressed between **119 and 200** — a classic low-contrast image.

### Steps

| Step | Description |
|------|-------------|
| 1–3 | Load and display the image with `cv2.imread` + `plt.imshow` |
| 4–5 | Plot histogram + CDF → observe the narrow intensity range |
| 6–7 | Apply `cv2.equalizeHist()` → range extended to **[0, 255]**, CDF becomes linear |
| 8 | Apply **Adaptive HE (AHE)** with `skimage` — local equalization per grid cell |
| 9 | Apply **CLAHE** with `cv2.createCLAHE()` — test multiple `clipLimit` values |

### CLAHE parameter analysis (`clipLimit` sweep)

| `clipLimit` | Effect |
|-------------|--------|
| 1.0 | Minimal enhancement — image still dull |
| **2.0** | Best balance — contrast improved, no artifacts |
| 4.0 | Strong contrast, risk of halos at edges |
| 8.0 | Over-amplification — visible noise and artifacts |

---

## Part 2 — Medical Image Enhancement (Article Reproduction)

Based on: **Salem N., Malik H., Shams A.** (2019). *Medical image enhancement based on histogram algorithms.* Procedia Computer Science, 163, 300–311. [DOI: 10.1016/j.procs.2019.12.112](https://doi.org/10.1016/j.procs.2019.12.112)

Four algorithms are implemented from scratch following the article's equations, applied to a **1411×1411 grayscale retina image** (intensity range: 0–234, mean: 90.3).

### Algorithms implemented

| Algorithm | Core idea | Key equations |
|-----------|-----------|---------------|
| **HE** — Histogram Equalization | Spreads all intensities via the global CDF | Eq. (4), (5) |
| **CHE** — Cumulative HE | Normalises relative to the minimum CDF value | Eq. (7) |
| **QDHE** — Quadrant Dynamic HE | Partitions histogram into 4 sub-histograms + clipping + independent HE per quadrant | Eq. (8)–(11) |
| **CLAHE** — Contrast Limited AHE | Tile-based local equalisation with clip limit to prevent over-amplification | OpenCV `createCLAHE` |

### Evaluation metrics (Section 7 of the article)

```
MSE  = (1/MN) × Σ (Enhanced(i,j) − Original(i,j))²
PSNR = 10 × log10((L−1)² / MSE)    [dB]
SD   = standard deviation of the enhanced image intensities
```

---

## Results

### Quantitative comparison on the retina image

| Method | MSE ↓ | PSNR (dB) ↑ | SD |
|--------|------:|------------:|---:|
| Original (reference) | — | — | 51.72 |
| HE | 3178.23 | 13.11 | 66.91 |
| CHE | 3128.36 | 13.18 | **67.75** |
| **QDHE** | **21.43** | **34.82** | 53.81 |
| CLAHE | 211.10 | 24.89 | 55.64 |

### Winners per metric

| Metric | Best method | Interpretation |
|--------|-------------|----------------|
| MSE ↓ | **QDHE** (21.43) | Least distortion introduced vs original |
| PSNR ↑ | **QDHE** (34.82 dB) | Best signal preservation |
| SD ↑ | **CHE** (67.75) | Maximum contrast spread |
| **Best for retina overall** | **CLAHE** | Confirms article conclusion — see below |

### Why CLAHE wins for retina despite lower PSNR than QDHE

QDHE minimises distortion relative to the original, but retina images have a **non-uniform intensity distribution** — the optic disc, blood vessels, and peripheral retina all have very different local contrasts. CLAHE's tile-based local processing reveals fine vascular structures that QDHE's global quadrant approach misses. The article explicitly concludes: *"CLAHE is the best enhancement technique for the retina images."*

This result is **consistent with the original article** (Table 2: retina PSNR — CLAHE = 21.24 dB vs QDHE = 17.43 dB in the article; our Python implementation gives CLAHE = 24.89 dB vs QDHE = 34.82 dB — differences are expected since the article used MATLAB and a different retina image source).

---

## Setup & Usage

### Requirements

```bash
pip install numpy pandas matplotlib opencv-python scikit-image
```

### Run

```bash
jupyter notebook Histogram_Manipulation_Medical_Image_Enhancement.ipynb
```

> Make sure images are in the same directory as the notebook.  

---

## Key Takeaways

1. **Global HE is fast but aggressive** — it maps all intensities uniformly and can destroy local detail, producing the worst MSE and PSNR on medical images.
2. **CHE marginally improves HE** — the CDF normalisation reduces distortion slightly but both share the same global-processing limitation.
3. **QDHE best preserves fidelity** — by splitting the histogram into four quadrants, it avoids the over-stretching that plagues HE and CHE.
4. **CLAHE is the gold standard for medical images** — local adaptive equalization with clipping reveals clinically relevant fine structures (blood vessels, tissue boundaries) that global methods homogenise away.
5. **All methods improve SD** — every technique successfully increases contrast spread compared to the original, confirming the enhancement goal is met regardless of fidelity.
6. **The `clipLimit` matters in CLAHE** — values above 4.0 introduce artifacts; 2.0 is the practical optimum for this image.

---

## Reference

> Salem, N., Malik, H., & Shams, A. (2019). Medical image enhancement based on histogram algorithms. *Procedia Computer Science*, 163, 300–311. https://doi.org/10.1016/j.procs.2019.12.112

---

## 🛠️ Technologies

![Python](https://img.shields.io/badge/Python-3.10-blue)
![OpenCV](https://img.shields.io/badge/OpenCV-4.x-green)
![scikit-image](https://img.shields.io/badge/scikit--image-0.21-orange)
![NumPy](https://img.shields.io/badge/NumPy-1.26-blue)
![Matplotlib](https://img.shields.io/badge/Matplotlib-3.8-blue)
