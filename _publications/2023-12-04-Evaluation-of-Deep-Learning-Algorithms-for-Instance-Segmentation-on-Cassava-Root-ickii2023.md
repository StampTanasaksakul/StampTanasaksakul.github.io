---
title: "Evaluation of Deep Learning Algorithms for Instance Segmentation on Cassava Root"
collection: publications
permalink: /publication/https://doi.org/10.1109/ICKII58656.2023.10332573
excerpt: False
date: 2023-12-04
venue: 'Proceedings of the 2023 8th International Conference on Machine Learning Technologies (ICMLT 2023)'
paperurl: 'https://doi.org/10.1109/ICKII58656.2023.10332573'
citation: 'C. Tanasaksakul, K. Akkarajitsakul and T. Wojciechowski, "Evaluation of Deep Learning Algorithms for Instance Segmentation on Cassava Root," 2023 IEEE 6th International Conference on Knowledge Innovation and Invention (ICKII), Sapporo, Japan, 2023, pp. 392-397, doi: 10.1109/ICKII58656.2023.10332573.keywords: {Training;Deep learning;Measurement;Technological innovation;Shape;Computational modeling;Predictive models;Instance segmentation;Object detection;Convolutional neural network;Cassava roots},'

---

Abstract
======
Different algorithms were developed and evaluated for segmenting images of cassava roots. Cassava CSRs (CSR) have different shapes and sizes. Existing methods rely on image processing algorithms to segment CSR, but these often have problems with overlapping fibrous roots. In this study, deep learning architectures for instance segmentation HTC, SOLOv2, and Mask R-CNN were applied to CSR data to investigate whether these models can overcome the specific challenges of this domain. We created a custom dataset with 1,227 training, 100 validation, and 100 test images of CSRs with different root systems and high-quality segmentation masks. Then, we compared the result of the SOLOv2 model to the Mask R-CNN and HTC models using the same network architecture. The standard metric for comparing instance segmentation models in computer vision such as mean average precision, was used for the evaluation. The results showed that all tested models applied to the considered area showed good prediction accuracy. SOLOv2 showed the best results on the test set with the computation of the model less than that of HTC. Increasing the model complexity and size did not impact the prediction accuracy in the instance segmentation of CSR. HTC achieved almost the same result as that of the SOLOv2 model but required almost doubling the number of parameters. The visualization of the predictions showed quality differences in terms of the accuracy of the segmentation masks. The SOLOv2 model showed the best masks overall. However, each model had problems in assigning the segmentation masks due to the overlap of a CSR. Many root systems still need to be measured for gene discovery and subsequent breeding. The results demonstrated the potential of deep learning instance segmentation models for CSRs needs to be increased with further research.

[Download paper here (pre-print version)](http://stamptanasaksakul.github.io/files/(Preprint)Evaluation-of-Deep-Learning-Algorithms-for-Instance-Segmentation-on-Cassava-Root.pdf)

<!---
Recommended citation: C. Tanasaksakul, K. Akkarajitsakul and T. Wojciechowski, "Evaluation of Deep Learning Algorithms for Instance Segmentation on Cassava Root," 2023 IEEE 6th International Conference on Knowledge Innovation and Invention (ICKII), Sapporo, Japan, 2023, pp. 392-397, doi: 10.1109/ICKII58656.2023.10332573.keywords: {Training;Deep learning;Measurement;Technological innovation;Shape;Computational modeling;Predictive models;Instance segmentation;Object detection;Convolutional neural network;Cassava roots},
--->