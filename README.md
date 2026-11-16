# MTB detection in sputum smears with a camera shift test 
**Track:** Health

**Team:** Rafa deGoma
## The problem - Who has this problem, and why does it matter? (2-3 sentences)
- diagnosing TB in low-resource, high-burden settings is difficult
- 1.13 mil died in 2025 from TB -> harms SDG 3, 3.5 years away
- Only 54% of TB testing in 2024 was done with WHO-recommended rapid (molecular) tests such as the Xpert MTB/RIF test.
- 2.2M undetected cases in 2025, over 20% of the total.
- 86% of TB patients are in LMICs, 55% of global cases come from 5 countries (India, Indonesia, the Philippines, Pakistan, and Nigeria)
- many under-resourced clinics in LMICs do not have the resources to conduct recommended, faster molecular tests, instead using Ziehl-Neelsen (ZN) staining and counting mycobacterium tuberculosis (MTB).
- counting is done by hand, and in high-burden settings, is thus error-prone.
## What we built - What goes in, what comes out, and how someone would use it.
- a microscope ZN smear image is fed into the fine-tuned model for inference
- model outputs bounding box predictions for MTB, which are counted to get the predicted MTB count
- clinicians use the MTB count from the model to determine whether to diagnose a patient with TB
- the dual experiment approach used in the development of the model quantifies change in performance as a result of acquisition shift between the Hayear and Optilab camera systems in MTB micrscopy.
## What we added
Our own idea.
## How it works
Data: which dataset, how many examples, how we cleaned it.
- Used the "Comprehensive Raw Dataset of Ziehl-Neelsen-Stained Sputum Smear Microscopy Images for Mycobacterium tuberculosis Detection" recently published alongside a paper
- Contained microscope images of sputum smears using ZN staining and used two different camera systems roughly equally: Hayear and Optilab.
- Original dataset contained 1438 images with 11447 examples in total, split by image into train / val / test at a roughly 80 / 10 / 10 split.
- cleaned by removing 170 / 25 / 17 ambiguous images (several label files were paired with two different images)
- also corrected a corrupted label that was 1 and should have been 0
- total used: 979 / 117 / 130
Baseline: predicted a calculated constant that was the median count of cells in each image for the training dataset of each of the two experiments:
- experiment A's baseline prediction: 6
- experiment B's baseline prediction: 6
Model: I fine-tuned a YOLO26n model, using two separate experiments to conduct an acquisition test by splitting the data splits based on camera.
- experiment A
- experiment B
- Nano size was chosen to prioritize efficiency, which translates to the computational budgets and cellular connection limits in low-resource settings.
- YOLO26 introduced the novel Small-Target-Aware Label Assignment (STAL) for improving small object detection, and MTB are very small in smear images.
- Data augmentation flipud=0.5

- Iterated over confidence thresholds to get best MAE on val: 0.34 for A and 0.3 for B
## Results - Our baseline scored X. Our model scored Y on held-out test data.
The baseline scored _ MAE. My model scored _ MAE on held-out test data.

| Test set | Model | Count MAE ↓ | vs. baseline | Precision | Recall | F1 | Misses (FN) | False alarms (FP) |
|---|---|---:|---:|---:|---:|---:|---:|---:|
| Hayear (n=67) | Baseline (median = 6) | 5.25 | — | — | — | — | — | — |
| Hayear (n=67) | A (both cameras) | 1.87 | −64% | 0.77 | 0.72 | 0.74 | 170 | 127 |
| Hayear (n=67) | B (Hayear only) | 1.99 | −62% | 0.74 | 0.73 | 0.74 | 164 | 151 |
| **Optilab (n=63)** | Baseline (median = 6) | 5.03 | — | — | — | — | — | — |
| **Optilab (n=63)** | A (both cameras) | 1.44 | −71% | 0.80 | 0.72 | 0.76 | 137 | 90 |
| **Optilab (n=63)** | **B (Hayear only, unseen camera)** | **1.44** | **−71%** | **0.76** | **0.72** | **0.74** | **140** | **115** |
| Overall (n=130) | Baseline (median = 6) | 5.15 | — | — | — | — | — | — |
| Overall (n=130) | A (both cameras) | 1.66 | −68% | 0.78 | 0.72 | 0.75 | 307 | 217 |
| Overall (n=130) | B (Hayear only) | 1.72 | −67% | 0.75 | 0.72 | 0.74 | 304 | 266 |

- failure examples
error 6

<img width="647" height="504" alt="image" src="https://github.com/user-attachments/assets/4ba24e69-abb5-468d-aa3d-397ad7a4b258" />


error 5

<img width="647" height="504" alt="image" src="https://github.com/user-attachments/assets/c616892a-42ae-4831-bfbf-fbf04640bc5c" />


error 5

<img width="647" height="504" alt="image" src="https://github.com/user-attachments/assets/6322e8f9-2e0c-4e1a-aa02-e97f10d2d1e1" />

## What we'd do next - One or two concrete next steps with more time.
- Make this ready for use in the form of a mobile app
- Collect more data with more variety
## Sources
[1]	H. Yuniarti, C. Fatichah, R. Sigit, S. Arifin, and E. da Costa, “A Comprehensive Raw Dataset of Ziehl-Neelsen-Stained Sputum Smear Microscopy Images for Mycobacterium tuberculosis Detection.” Version 9, Mendeley Data, May 23, 2026. doi: [10.17632/34GYMTJ5YC.9](https://doi.org/10.17632/34GYMTJ5YC.9). Licensed under CC BY 4.0.

[2]	H. Yuniarti, C. Fatichah, R. Sigit, S. Arifin, and E. da Costa, “A comprehensive raw dataset of Ziehl-Neelsen-stained sputum smear microscopy images for Mycobacterium tuberculosis detection,” Data Brief, vol. 67, p. 112908, Aug. 2026, doi: [10.1016/j.dib.2026.112908](https://doi.org/10.1016/j.dib.2026.112908). 

[3] Anthropic, "Claude, Opus 5.5," 2026. [Online]. Available: https://claude.ai. Accessed: November 14, 2026.

[4]	“10 facts on tuberculosis.” Accessed: Nov. 15, 2026. [Online]. Available: https://www.who.int/news-room/facts-in-pictures/detail/tuberculosis 

[5]	M. Aubreville et al., “Quantifying the Scanner-Induced Domain Gap in Mitosis Detection,” presented at the MIDL: Medical Imaging with Deep Learning 2021, MIDL Foundation, 2021. Accessed: Nov. 14, 2026. [Online]. Available: https://opus4.kobv.de/opus4-haw/frontdoor/index/index/docId/1243 

[6]	“Tuberculosis (TB).” Accessed: Nov. 15, 2026. [Online]. Available: https://www.who.int/news-room/fact-sheets/detail/tuberculosis 

[7]	D. Baba and B.-E. Atinga, “Tuberculosis in Low- and Middle-income Countries: A Critical Analysis of Health System Barriers and the Promise of Artificial Intelligence”, doi: 10.2174/0118749445431680260121064724. 

[8]	Ultralytics, “YOLO26: Smarter Training with ProgLoss, STAL & MuSGD,” Ultralytics. Accessed: Nov. 14, 2026. [Online]. Available: https://www.ultralytics.com/blog/how-ultralytics-yolo26-trains-smarter-with-progloss-stal-and-musgd 

## Setup
Make a folder in your Google Drive named "MTB Detection", download the ZIP file of the data from the [dataset website](https://data.mendeley.com/datasets/34gymtj5yc/9), and drag the ZIP into the Drive folder.
Run the notebook sequentially (MTB_Detection.ipynb).

## Disclaimer
This project is a prototype and is not medical advice.
