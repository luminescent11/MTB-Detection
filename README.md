# MTB detection in sputum smears with a camera shift test 
**Track:** Health

**Team:** Rafa deGoma
## The problem
Many under-resourced clinics in LMICs, making up 86% of the 10.6M TB patients, do not have the resources to conduct recommended, faster molecular TB diagnostics, instead using Ziehl-Neelsen (ZN) staining and counting mycobacterium tuberculosis (MTB) from microscope images by hand, which is error-prone. While counting can be assisted by computer vision, camera setup shifts in AI are known to hurt performance: a study showed that F1 score on mitosis detection dropped from 0.683 to 0.325 when the testing dataset was on a different scanner than the one trained on (Aubreville et al. 2021). I hypothesize that a similar pattern will apply to MTB detection in sputum smear images.
## What we built - What goes in, what comes out, and how someone would use it.
A microscope ZN smear image is fed into the fine-tuned model for inference, and the model outputs bounding box predictions for MTB, which are counted to get the predicted MTB count. Clinicians use the MTB count from the model to expedite counting and diagnosis; formal diagnosis stays with human professionals.
## What we added
Our own idea.
## How it works
I used the Comprehensive Raw Dataset of Ziehl-Neelsen-Stained Sputum Smear Microscopy Images for Mycobacterium tuberculosis Detection, which contained microscope images of sputum smears using ZN staining and used two different camera systems roughly equally: Hayear and Optilab. The original dataset contained 1438 images with 11447 examples in total, split by image into train / val / test at a roughly 80 / 10 / 10 split. I cleaned it by removing 170 / 25 / 17 ambiguous labels which were paired with two different images, also correcting a corrupted label. Total used: 979 / 117 / 130.

Model development used two separate experiments to quantify change in performance as a result of acquisition shift, specifically between the Hayear and Optilab camera systems in MTB micrscopy, by splitting the data splits based on camera. Experiment A trained and validated on both systems, and B trained and validated only on Hayear. The baseline was the median count for each model's training set. I fine-tuned a YOLO26n model and obtained the optimal confidence threshold from iterating over 86 choices. Nano size was chosen to prioritize efficiency, which translates to the computational budgets and cellular connection limits in low-resource settings. YOLO26 introduced the novel Small-Target-Aware Label Assignment (STAL) for improving small object detection, and MTB are very small in smear images.
## Results
### Metrics
The baseline scored 5.15 overall MAE. Model A scored 1.66 overall MAE on held-out test data (68% decrease). Model B's MAE for Optilab was 28% lower than for Hayear, disproving the original hypothesis. This may have been because of YOLO augmentations simulating camera differences or leakage between images of the same slide. Overall recall for both models remains at 0.72, missing 28% of cells.

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

### Failure Examples
These were the highest absolute error images from model B predicting on Optilab images; green is human ground truth, red is model prediction:
Absolute error = 6

<img width="647" height="504" alt="image" src="https://github.com/user-attachments/assets/4ba24e69-abb5-468d-aa3d-397ad7a4b258" />

Densely clustered MTB are merged into one prediction or result in false negatives.


Absolute error = 5

<img width="647" height="504" alt="image" src="https://github.com/user-attachments/assets/c616892a-42ae-4831-bfbf-fbf04640bc5c" />

Very blurry images with lots of background color variation appear to also primarily result in false negatives, but also a few false positives.

### Limitations
A and B differ in training size, not just camera. Small test sets: 63–67 images per camera, so metrics are noisy.

## What we'd do next
One thing I would do next is make this ready for use in the form of a mobile app. However, to improve the model, I would attempt to collect more data with more variety, including different camera systems, geographic origins, and patient populations.
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
