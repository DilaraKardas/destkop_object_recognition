🇬🇧 English | 🇹🇷 [Türkçe](README_TR.md)

# YOLOv8 Object Detection Project

### 📌 Project Description

This project is an **object detection** study developed in **less than 1 week**  
as part of the **Vision-Language Models** course.

The main objective of the project is to **observe the impact of the created dataset on model performance**  
and examine how data quality is reflected in object detection success.

---

### 🗂️ About the Dataset

The dataset used in this project:

- Was obtained from real objects in the **Computer Engineering laboratories at Bursa Technical University**.
- The dataset includes the following objects:
  - Monitor
  - Keyboard
  - Chair
  - Computer mouse
- All images were **captured manually** (no pre-existing dataset was used).
- **Over 2000 images** were **manually annotated** using the **Roboflow** platform.
- Data augmentation techniques were applied.
- The dataset was split on Roboflow with the following ratios:
  - `70% Train`
  - `20% Validation`
  - `10% Test`

---

### 🎯 Project Objective

The main goal of this study is:

  Rather than focusing on model architecture, To understand the impact of **dataset quality and diversity**
  on the object detection model.

Therefore, the model was:
- Trained using a ready-made architecture (YOLOv8),
- With a **custom dataset completely created by the user**.

---

### 🧠 Technologies Used

- Python 3.x
- Ultralytics YOLOv8
- PyTorch
- OpenCV
- Roboflow

---

### 📂 Project Folder Structure
```text
yolo_webcam_test/
├── best.pt            # Trained YOLOv8 model weights
├── webcam_test.py     # Real-time testing code via webcam
└── README.md          # Project description file
```

---

### 🚀 Model and Dataset
You can access the model and the dataset folder via drive link below:

[Download the model and the dataset](https://drive.google.com/drive/folders/1JQMM69MS2JZxDmcGzi7xCo1TVw3XwC6N?usp=sharing)

### ⚙️ Installation

Install the required libraries:
```bash
pip install ultralytics opencv-python
```

Run the project:
```bash
cd yolo_webcam_test
python webcam_test.py  # test the model with webcam
```
