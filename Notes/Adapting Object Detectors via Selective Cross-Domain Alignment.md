# Adapting Object Detectors via Selective Cross-Domain Alignment

***

* Problem to solve: 
  * conventional domain adaptation methods strive to align images as a whole result in quite limited improvement. 
  * difficulties when the model which was trained on public datasets applied to a different domain
  * existed, they can only cover a very limited range of scenarios. In real-world deployment, the changes in environmental conditions, e.g. imaging sensors, weather, and illumination, can cause significant domain shift, and thus substantial performance degradation
* Introduction
  * conventional domain adaptation methods typically consider the input image as a whole when trying to bridge the domain gap, while neglecting the local nature of object detection.
* **Proposed Method**: 
  * basic idea: reposition the focus of the adaptation process, from global to local
  * new framework consists of two key components : **region mining** and **region level alignment**
  * **region mining**: solve **where to look** , resorts to grouping strategy to identify the most important local regions
  * **region level alignment** : solve **how to align**, first leverages the region proposal in the source domain to reweigh the target region proposals overcoming the difficulty caused by the lack of target annotations
  * Then performs the region-level domain alignment in an adversarial manner.
* key point: focus on the regions of interest
* steps
  * Faster RCNN Backbone: extract the features by VGG16, using RPN generate proposals
  * "where to look": In this paper, simply used k-means method
  * "how to align": address the problem using the thoughts from GAN
* Experiment result
  * Cityscapes to Foggy Cityscapes，SIM10K to Cityscape(on car)and Kitti to Cityscapes(on car) 
  * We can conclude from the result, with the same adversarial manner, repositioning the focus of the adaptation process, from global to local made pretty good improvement
  * ROI-based Grouping, which is also proposed in the paper, also made significant  progress