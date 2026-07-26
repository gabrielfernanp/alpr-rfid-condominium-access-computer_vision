# Vehicle Access Control Automation in Condominiums Using RFID and Computer Vision

Automated vehicle access control system for condominiums, integrating **RFID/NFC** and **Computer Vision** for redundant and secure vehicle authentication. The project combines radio-frequency electronic reading with license plate detection and recognition (Brazilian and Mercosur standards), reducing human error and strengthening security mechanisms in residential environments.

Undergraduate Thesis (TCC) — Computer Engineering, Centro Universitário FAESA.

## Table of Contents

- [About the Project](#about-the-project)
- [System Architecture](#system-architecture)
- [Dataset](#dataset)
- [Methodology](#methodology)
- [Results](#results)
- [Tech Stack](#tech-stack)
- [Repository Structure](#repository-structure)
- [Getting Started](#getting-started)
- [Future Work](#future-work)
- [References](#references)
- [Authors](#authors)
- [License](#license)

## About the Project

Access control systems based solely on RFID present well-known limitations, such as incorrect entry/exit records and the absence of visual evidence for auditing. This project proposes a **functional redundancy** architecture, in which computer vision acts as an independent validation layer for RFID/NFC readings, inspired by fault-tolerance principles and the *recovery blocks* concept (RANDELL, 1975).

The computer vision pipeline consists of four main stages:

1. **Vehicle and license plate detection** in the scene, using a convolutional neural network (YOLOv11).
2. **Localization and geometric correction** of the plate region (perspective rectification).
3. **Optical Character Recognition (OCR)**, converting the rectified image into an alphanumeric string.
4. **Visual re-identification** of the vehicle, associating detections of the same vehicle over time based on attributes such as color, shape, and proportions.

The gate-opening decision is made through cross-validation between the RFID/NFC tag read and the recognized license plate, with three possible operating modes: RFID only, plate recognition only, or both combined (requiring the tag and plate to match the same vehicle).

## System Architecture

The system is structured in a modular way, separating the detection, authentication, and logging stages:

- **Hardware & Capture**: security camera and vehicular RFID reader.
- **AI Service**: YOLOv11 detection engine (`best.pt`) and EasyOCR recognition engine, responsible for locating and reading license plates.
- **Validation & Redundancy Module**: matching logic between the recognized plate and the RFID/NFC tag, with event logging to a database.
- **Access Actuation**: physical gate release upon successful validation.

## Dataset

Model training and evaluation used the **RodoSol-ALPR** dataset (Automatic License Plate Recognition), composed of real-world vehicle images captured on roadways under varying lighting, positioning, and distance conditions, covering cars, motorcycles, and medium-sized trucks in the following plate standards:

- Traditional Brazilian (`ABC-1234`)
- Mercosur (`ABC1D23`)

This dataset was chosen for its similarity to a residential condominium scenario and the availability of complete annotations for vehicles, plates, and characters, enabling supervised training.

> RodoSol-ALPR — R. Laroca et al. *On the Cross-Dataset Generalization in License Plate Recognition*. VISAPP, 2022.

## Methodology

- **Detection model**: YOLOv11 (YOLO11n), initialized from the pre-trained `yolo11n.pt` weights, configured for 2 classes: `plate_mercosur` and `plate_brazilian`.
- **Training environment**: Kaggle, with a Tesla T4 GPU, Ultralytics 8.4.41, Python 3.12.12, and PyTorch 2.10.0 (CUDA).
- **Hyperparameters**: 50 epochs, 640px image size, batch size 16, data organized via `data.yaml` (train/validation/test splits).
- **Plate pre-processing**: cropping of the detected region, perspective correction, and binarization before sending it to the OCR module.
- **OCR**: EasyOCR, applied to the rectified plate image to extract the full alphanumeric sequence.
- **External set**: 30 unseen images, not used during training, to evaluate the system's generalization.

### Metrics

- **Detection (YOLOv11)**: precision, recall, mAP50, mAP50-95, and average detection confidence.
- **OCR**: full-plate accuracy (a reading is counted as correct only when the entire recognized alphanumeric sequence exactly matches the expected plate).

## Results

### License plate detection (validation set — 4,000 images)

| Metric | Result |
|---|---|
| Precision | 0.998 |
| Recall | 0.997 |
| mAP50 | 0.995 |
| mAP50-95 | 0.948 |

The confusion matrix showed low confusion between the Mercosur and Brazilian standards: 1,994/2,000 correct classifications for `plate_mercosur` and 1,997/2,000 for `plate_brazilian`, with errors mostly concentrated in background false positives.

### Integrated experiment (detection + OCR)

| Evaluated set | Sample | Avg. detection confidence | OCR text accuracy |
|---|---|---|---|
| Internal validation | 30 images | 91.00% | 65.67% |
| External set | 30 images | 87.00% | 59.25% |

The detection stage showed consistent and stable performance across both sets. The OCR stage, in turn, proved more sensitive to visual variations (angle, resolution, lighting, and noise), being identified as the main area for improvement toward real-world production deployment.

## Tech Stack

- **Python**
- **YOLOv11** (Ultralytics) — vehicle and license plate detection
- **EasyOCR** — optical character recognition
- **OpenCV** — image pre-processing
- **PyTorch** — deep learning backend
- **RFID/NFC** (ISO/IEC 14443, 13.56 MHz) — electronic authentication
- **Kaggle** (Tesla T4 GPU) — training environment

## Repository Structure

```
├── datasets/              # Data structure (data.yaml, train/val/test splits)
├── training/              # YOLOv11 training notebooks and scripts
├── ocr/                   # Optical character recognition module (EasyOCR)
├── preprocessing/         # Perspective correction, cropping, and binarization of plates
├── reidentification/      # Visual vehicle re-identification module
├── rfid/                  # RFID/NFC reader integration
├── models/                # Trained weights (best.pt)
├── docs/                  # Documentation, figures, and thesis paper
├── requirements.txt
└── README.md
```

> Adjust this section to match the actual folder organization of your repository.

## Getting Started

```bash
# Clone the repository
git clone https://github.com/<your-username>/<repository-name>.git
cd <repository-name>

# Create a virtual environment and install dependencies
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install -r requirements.txt

# Run inference with the trained model
python detect.py --weights models/best.pt --source path/to/image.jpg
```

> Adjust the commands above to match the scripts actually present in the repository.

## Future Work

- Expand the dataset with images captured in different real-world contexts.
- Improve the OCR module to increase text recognition accuracy.
- Integrate additional sensors and more advanced visual recognition models.
- Validate the system in real operational condominium scenarios.

## References

- AMIRI, A.; KAYA, A.; KECELI, A. S. *A Comprehensive Survey on Deep-Learning-Based Vehicle Re-Identification: Models, Data Sets and Challenges*. arXiv preprint, 2024.
- CHETOUANE, F. *An Overview on RFID Technology Instruction and Application*. IFAC-PapersOnLine, v. 48, n. 3, p. 382–387, 2015.
- LAROCA, R. et al. *An Efficient and Layout-Independent Automatic License Plate Recognition System Based on the YOLO detector*. IET Intelligent Transport Systems, v. 15, n. 7, p. 871–889, 2021.
- LAROCA, R. et al. *On the Cross-Dataset Generalization in License Plate Recognition*. VISAPP, 2022, p. 166–178.
- LECUN, Y.; BENGIO, Y.; HINTON, G. *Deep Learning*. Nature, v. 521, n. 7553, p. 436–444, 2015.
- RANDELL, B. *System structure for software fault tolerance*. International Conference on Reliable Software, ACM, 1975, p. 437–449.
- SILVA, S. M.; JUNG, C. R. *License Plate Detection and Recognition in Unconstrained Scenarios*. ECCV, Springer, 2018, p. 593–609.

## Authors

- Gabriel Fernandes Pereira Valentino
- João Marcos dos Santos Gil
- Renan Bispo Pessoa

Advisor: Prof. Msc. Gabriel Soares Baptista — Centro Universitário FAESA

## License

Academic project developed for an undergraduate thesis (TCC). Choose a license of your preference (e.g., [MIT](https://choosealicense.com/licenses/mit/)) if you wish to make the code publicly available.
