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
- cleaned by removing _ ambiguous images (several label files were paired with two different images)
Model: I fine-tuned a YOLO26n model, using two separate experiments to conduct an acquisition test by splitting the data splits based on camera.
- Nano size was chosen to prioritize efficiency, which translates to the computational budgets and cellular connection limits in low-resource settings.
- YOLO26 introduced the novel Small-Target-Aware Label Assignment (STAL) for reducing false positives on small targets, and MTB are very small in smear images.
## Results Our baseline scored X. Our model scored Y on held-out test data.
Our baseline scored _ MAE. Our model scored _ MAE on held-out test data.
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
