---
title: 'Crack Detection on Flare Tip using YOLOv5'
date: 2022-06-30
permalink: /posts/2022/06/crack-detection-yolov5/
external_url: 'https://medium.com/skyller-solutions/crack-detection-on-flare-tip-using-yolov5-9a90419714fd'
tags:
  - computer-vision
  - deep-learning
  - yolo
  - machine-learning
  - object-detection
---

*Originally published on [Medium — SKYLLER Solutions](https://medium.com/skyller-solutions/crack-detection-on-flare-tip-using-yolov5-9a90419714fd)*

A deep-dive into applying **YOLOv5** for automated crack detection on industrial flare tips. This article covers:

- Object Detection fundamentals and the YOLO architecture
- How YOLO's loss function works (bounding box loss, confidence loss, classification loss)
- Mean Average Precision (mAP) evaluation — IoU, Precision, Recall explained
- Data annotation with LabelImg and custom YAML training config
- Training YOLOv5s6 at 1280×1280 resolution for high-resolution drone imagery
- Deploying the model on Skyller's Data Platform for real-time inspection

**Results:** After 100 epochs, the model reached **mAP@0.5 = 0.649** and **mAP@0.5:0.95 = 0.341**, enabling automated crack detection for industrial inspectors.

👉 [Read the full article on Medium](https://medium.com/skyller-solutions/crack-detection-on-flare-tip-using-yolov5-9a90419714fd)
