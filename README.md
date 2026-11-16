# MTB detection in sputum smears with a camera shift test 
**Track:** Health

**Team:** Rafa deGoma
## The problem - Who has this problem, and why does it matter? (2-3 sentences)
- diagnosing TB in low-resource, high-burden settings is difficult
- 1.13 mil died in 2025 from TB -> harms SDG 3, 3.5 years away
- 86% of TB patients are in LMICs, 55% of global cases come from 5 countries (India, Indonesia, the Philippines, Pakistan, and Nigeria)
- many under-resourced clinics in LMICs do not have the resources to conduct recommended, faster molecular tests as recommended by WHO (e.g. Xpert MTB/RIF), instead using Ziehl-Neelsen (ZN) staining and counting mycobacterium tuberculosis (MTB).
- counting is done by hand, and in high-burden settings, is thus error-prone.
## What we built - What goes in, what comes out, and how someone would use it.
- a microscope ZN smear image is fed into the fine-tuned model for inference
- model outputs bounding box predictions for MTB, which are counted to get the predicted MTB count
- clinicians use the MTB count from the model to determine whether to diagnose a patient with TB
## What we added
Our own idea.
## How it works
Data: which dataset, how many examples, how we cleaned it.
- Used the "Comprehensive Raw Dataset of Ziehl-Neelsen-Stained Sputum Smear Microscopy Images for Mycobacterium tuberculosis Detection" recently published alongside a paper
- Contained microscope images of sputum smears using ZN staining and two different camera systems: Hayear and Optilab.
- had _ examples in total, split into train / val / test: _ / _ / _

excluded ambiguous stems by split: {'test': 17, 'train': 170, 'val': 25}
images on disk: 1650 | metadata rows: 1226 | rows with no image: 0
split mismatches: 0
train_all: 979 images
train_hayear: 494 images
train_optilab: 485 images
val_all: 117 images
val_hayear: 57 images
val_optilab: 60 images
test_all: 130 images
test_hayear: 67 images
test_optilab: 63 images


- cleaned by removing _ ambiguous images (several label files were paired with two different images)
- also corrected a corrupted label that was 1 and should have been 0
Baseline: predicted a calculated constant that was the median count of cells in each image for the training dataset of each of the two experiments:
- experiment A's baseline prediction: 6
- experiment B's baseline prediction: 6
Model: I fine-tuned a YOLO26n model, using two separate experiments to conduct an acquisition test by splitting the data splits based on camera.
- experiment A
- experiment B
- Nano size was chosen to prioritize efficiency, which translates to the computational budgets and cellular connection limits in low-resource settings.
- YOLO26 introduced the novel Small-Target-Aware Label Assignment (STAL) for reducing false positives on small targets, and MTB are very small in smear images.
- Data augmentation flipud=0.5

- Iterated over confidence thresholds to get best MAE on val: 0.34 for A and 0.3 for B
## Results Our baseline scored X. Our model scored Y on held-out test data.
Our baseline scored _ MAE. Our model scored _ MAE on held-out test data.

index	camera	experiment	MAE	vs_baseline_%	precision	recall	F1	FN	FP	TP
0	hayear	baseline	5.254	NaN	NaN	NaN	NaN	NaN	NaN	NaN
1	hayear	A	1.866	64.489	0.772	0.717	0.743	170.0	127.0	430.0
2	hayear	B	1.985	62.216	0.743	0.727	0.735	164.0	151.0	436.0
3	optilab	baseline	5.032	NaN	NaN	NaN	NaN	NaN	NaN	NaN
4	optilab	A	1.444	71.293	0.799	0.723	0.759	137.0	90.0	358.0
5	optilab	B	1.444	71.293	0.755	0.717	0.736	140.0	115.0	355.0
6	overall	baseline	5.146	NaN	NaN	NaN	NaN	NaN	NaN	NaN
7	overall	A	1.662	67.713	0.784	0.72	0.75	307.0	217.0	788.0
8	overall	B	1.723	66.517	0.748	0.722	0.735	304.0	266.0	791.0

- fails at: ...
## What we'd do next - One or two concrete next steps with more time.
- Make this ready for use in the form of a mobile app
- Collect more data with more variety
## Sources
[1]	H. Yuniarti, C. Fatichah, R. Sigit, S. Arifin, and E. da Costa, “A Comprehensive Raw Dataset of Ziehl-Neelsen-Stained Sputum Smear Microscopy Images for Mycobacterium tuberculosis Detection.” Version 9, Mendeley Data, May 23, 2026. doi: [10.17632/34GYMTJ5YC.9](https://doi.org/10.17632/34GYMTJ5YC.9). Licensed under CC BY 4.0.

[2]	H. Yuniarti, C. Fatichah, R. Sigit, S. Arifin, and E. da Costa, “A comprehensive raw dataset of Ziehl-Neelsen-stained sputum smear microscopy images for Mycobacterium tuberculosis detection,” Data Brief, vol. 67, p. 112908, Aug. 2026, doi: [10.1016/j.dib.2026.112908](https://doi.org/10.1016/j.dib.2026.112908). 

[3] Anthropic, "Claude, Opus 5.5," 2026. [Online]. Available: https://claude.ai. Accessed: November 14, 2026.

## Setup
Make a folder in your Google Drive named "MTB Detection", download the ZIP file of the data from the [dataset website](https://data.mendeley.com/datasets/34gymtj5yc/9), and drag the ZIP into the Drive folder.
Run the notebook sequentially (MTB_Detection.ipynb).

## Disclaimer
This project is a prototype and is not medical advice.
