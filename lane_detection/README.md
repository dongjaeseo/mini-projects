# Lane Detection Using Classical Vision + Mask R-CNN

This project implements a full pipeline for lane detection using both  
**traditional computer vision techniques** (calibration, perspective transform, thresholding)  
and **deep learning–based instance segmentation** (Mask R-CNN).  

The model detects lane regions frame-by-frame and extracts the **steering angle** based on the lane geometry.

---

# 1. Project Overview

The goal of this project is to:
- Detect lane boundaries on the road
- Identify whether lanes are solid or dashed
- Segment lane regions using Mask R-CNN
- Compute **steering angle** changes from lane curvature
- Apply this to video frames for continuous prediction
<img width="690" height="407" alt="image" src="https://github.com/user-attachments/assets/8cf9448f-bcfe-4b53-bd53-d474a8343333" />


This project combines:
1. **Classic CV pipeline** (chessboard calibration → bird’s-eye-view → color thresholding → sliding window)
2. **Mask R-CNN** for robust lane segmentation, especially under:
   - light reflection  
   - varying line thickness  
   - dashed vs solid lines  
   - noisy road markings  

---

# 2. Development Environment

- Python  
- OpenCV  
- NumPy  
- TensorFlow / Keras  
- Mask R-CNN (Matterport implementation)  
- VIA (VGG Image Annotator) for labeling  

---

# 3. Data Collection & Preprocessing

### **3.1 Camera Calibration**
- Used `cv2.findChessboardCorners()` to remove camera distortion  
- Produced camera matrix and distortion coefficients

### **3.2 Bird’s-Eye-View Transform**
Converted camera perspective into a top-down view:
- Removes perspective distortion  
- Makes lane geometry easier to detect  
- Useful for curvature estimation

### **3.3 Pixel Thresholding**
Used multiple color spaces to highlight lane markings:
- **HSV**  
- **HLS**  
- **LAB**  

Applied:
- Histogram equalization with **CLAHE**
- Binarization to isolate lane pixels  
<img width="1344" height="660" alt="image" src="https://github.com/user-attachments/assets/f4f1f5d8-9911-4abd-a94f-a5ee5cd4ce52" />

---

# 5. Mask R-CNN Lane Segmentation

### Why Mask R-CNN?
Histogram methods fail easily in real driving.  
Mask R-CNN offers:
- Pixel-level segmentation  
- Robustness to lighting and lane shape  
- Handles both solid/dashed lanes  
- Works well on curved roads  

### Data Labeling
- VIA annotator used for polygon masks  
- Exported annotations as JSON  
- Trained Mask R-CNN on lane masks

### Inference Output
Mask R-CNN returns:
- Multiple candidate masks  
- Each mask has True/False per pixel  
- Selected **the mask with the largest area** as final lane mask

Output mask shape:  
(height, width, 1)
---

# 6. Steering Angle Estimation

Using the predicted lane mask:
1. Extract lane centerline  
2. Track lane curvature across frames  
3. Convert curvature to steering angle  
4. Visualize angle on each video frame

Observations:
- Straight sections → angle stable  
- Left turns → angle decreases sharply  
- Right turns → angle increases  

---

# 7. Video Pipeline

- Split video into frames  
- Run Mask R-CNN on each frame  
- Collect mask outputs into an array  
- Compute steering angle per frame  
- Render overlay (line + angle text)
<img src="https://github.com/user-attachments/assets/11fe6896-8509-4c5f-856c-44c7b53f661d" width="400"/>
<img src="https://github.com/user-attachments/assets/92799826-fde5-4036-8cf0-8f6eedf16994" width="400"/>



---

# 8. Results

### Mask R-CNN Segmentation
- Accurate instance segmentation of lane region  
- Handles dashed and solid lanes  
- Resistant to lighting variations  
- Example scores:
  - lane: **0.881**
  - lane: **0.650**

### Final Output
The system produces:
- Lane mask  
- Lane centerline  
- Steering angle overlay  
- Smooth angle transitions across frames  

This creates a functional lane detection + steering estimation module.
<img width="564" height="996" alt="image" src="https://github.com/user-attachments/assets/7eafb159-eb76-4205-b91b-a0cfb4cd4717" />

---

# 9. Summary

This project demonstrates:
- A complete lane detection system combining classical CV and deep learning  
- Robust performance with Mask R-CNN instance segmentation  
- Reliable steering angle extraction from segmented lanes  
- Full video-processing pipeline
