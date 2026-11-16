# Camera Shift Effect in YOLO-Based Detection of Tuberculosis Bacteria in Sputum Microscopy
**Track:** Health

**Team:** Rafa deGoma
## The problem
TB sickened 10.7M people, mostly in low-middle-income countries, where many clinics do not have the resources to conduct recommended, faster molecular TB diagnostics, instead using Ziehl-Neelsen (ZN) staining and counting TB bacteria (MTB) from microscope images by eye, which is error-prone. While this can be assisted by CV, camera setup shifts in AI are known to hurt performance: F1 score on mitosis detection dropped from 0.683 to 0.325 when the testing dataset used different scanner than the one trained on (Aubreville et al. 2021). I hypothesize that a similar pattern will apply to MTB detection in sputum smear images.
## What we built
A microscope ZN smear image is fed into the fine-tuned model for inference, and the model outputs bounding box predictions for MTB, which are counted to get the predicted MTB count, used by lab technicians at underresourced clinics while reading ZN smears to expedite diagnosis. **A notebook copy with all outputs loaded can be found [here](https://github.com/luminescent11/MTB-Detection/blob/main/MTB_Detection.ipynb)**
## What we added
Our own idea.
## How it works
I used the [ZN-stained sputum smear images dataset](https://data.mendeley.com/datasets/34gymtj5yc/9), which used two camera systems: Hayear and Optilab. The original dataset contained 1438 images with 11447 examples at a roughly 80 / 10 / 10 split. I excluded 170 / 25 / 17 ambiguous images that shared labels with another image, as well as corrected a corrupted label.

Two experiments to measured change in performance from acquisition shift between these two cameras: experiment A trained and validated on both systems, and B trained and validated only on Hayear. The baseline was the median count for each model's training set. I fine-tuned a YOLO26n model (because of its aptitude at small object detection) and froze the confidence cutoff chosen on the validation set (A 0.34, B 0.30) before testing. Nano size was chosen for low-resource hardware.
## Results
### Metrics
Model A scored 1.66 overall MAE on held-out test set (68% decrease from baseline). Model B's MAE for Optilab was 27% lower than for Hayear and matched model A's, not supporting the original hypothesis. This may have been because of YOLO augmentations simulating camera differences or leakage between images of the same slide.

| Test set | Model | MAE ↓ | Prec. | Recall | Misses | False alarms |
|---|---|---:|---:|---:|---:|---:|
| Hayear (67) | Baseline | 5.25 | – | – | – | – |
| | A: both cameras | 1.87 | 0.77 | 0.72 | 170 | 127 |
| | B: Hayear only | 1.99 | 0.74 | 0.73 | 164 | 151 |
| **Optilab (63)** | Baseline | 5.03 | – | – | – | – |
| | A: both cameras | 1.44 | 0.80 | 0.72 | 137 | 90 |
| | **B: unseen camera** | **1.44** | 0.76 | 0.72 | 140 | **115** |
| Overall (130) | Baseline | 5.15 | – | – | – | – |
| | A: both cameras | 1.66 | 0.78 | 0.72 | 307 | 217 |
| | B: Hayear only | 1.72 | 0.75 | 0.72 | 304 | 266 |

### Failure Examples - highest absolute error images from model B predicting on Optilab images:

| Dense clusters get merged or missed | Blurry fields with uneven background are mostly missed |
|:---:|:---:|
| <img width="324" alt="failure 1" src="https://github.com/user-attachments/assets/4ba24e69-abb5-468d-aa3d-397ad7a4b258" /> | <img width="324" alt="failure 2" src="https://github.com/user-attachments/assets/c616892a-42ae-4831-bfbf-fbf04640bc5c" /> |

### Limitations
A and B differ in training size, not just camera. Small test sets: 63–67 images per camera, so metrics are noisy. Detections are acid-fast bacilli assumed as MTB, so positives need confirmation.

## What we'd do next
Make this ready for use in the form of a mobile app or collect data from new cameras, regions, and populations.

## Setup
Make a folder in your Google Drive named "MTB Detection", download the ZIP file of the data from the [dataset](https://data.mendeley.com/datasets/34gymtj5yc/9), and drag the ZIP into the Drive folder. Then add the two YAML files in this repo to that Drive folder. Set RUN_TRAINING and RUN_PREDICT to True. Run the notebook sequentially (MTB_Detection.ipynb) on a GPU runtime.

## Disclaimer
This project is a prototype and is not medical advice.

## Sources
[1]	H. Yuniarti, C. Fatichah, R. Sigit, S. Arifin, and E. da Costa, “A Comprehensive Raw Dataset of Ziehl-Neelsen-Stained Sputum Smear Microscopy Images for Mycobacterium tuberculosis Detection.” Version 9, Mendeley Data, May 23, 2026. doi: [10.17632/34GYMTJ5YC.9](https://doi.org/10.17632/34GYMTJ5YC.9). Licensed under CC BY 4.0.

[2]	H. Yuniarti, C. Fatichah, R. Sigit, S. Arifin, and E. da Costa, “A comprehensive raw dataset of Ziehl-Neelsen-stained sputum smear microscopy images for Mycobacterium tuberculosis detection,” Data Brief, vol. 67, p. 112908, Aug. 2026, doi: [10.1016/j.dib.2026.112908](https://doi.org/10.1016/j.dib.2026.112908). 

[3] Anthropic, "Claude, Opus 5.5," 2026. [Online]. Available: https://claude.ai. Accessed: November 14, 2026.

[4]	“10 facts on tuberculosis.” Accessed: Nov. 15, 2026. [Online]. Available: https://www.who.int/news-room/facts-in-pictures/detail/tuberculosis 

[5]	M. Aubreville et al., “Quantifying the Scanner-Induced Domain Gap in Mitosis Detection,” presented at the MIDL: Medical Imaging with Deep Learning 2021, MIDL Foundation, 2021. Accessed: Nov. 14, 2026. [Online]. Available: https://opus4.kobv.de/opus4-haw/frontdoor/index/index/docId/1243 

[6]	“Tuberculosis (TB).” Accessed: Nov. 15, 2026. [Online]. Available: https://www.who.int/news-room/fact-sheets/detail/tuberculosis 

[7]	Ultralytics, “YOLO26: Smarter Training with ProgLoss, STAL & MuSGD,” Ultralytics. Accessed: Nov. 14, 2026. [Online]. Available: https://www.ultralytics.com/blog/how-ultralytics-yolo26-trains-smarter-with-progloss-stal-and-musgd 
