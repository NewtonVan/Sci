# Faster R-CNN: Towards Real-Time Object Detection with Region Proposal Networks

***

* Motivation

  * Re-implementation ignores the down-stream detection network and misses important opportunities for sharing computation

  * >  Observation: the convolutional feature
    > maps used by region-based detectors, like Fast RCNN, can also be used for generating region proposals.

* Key contribution

  * RPN: adding a few additional convolutional layers that **simultaneously** regress region bounds and objectness scores at each location on a regular grid.
  * RPN is a kind of fully convolutional network
  * RPN can be trained end-to-end specifically for the task for generating detection proposals
  * RPNs are designed to efficiently predict region proposals with a wide range of scales and aspect ratios

* Faster R-CNN consists of **RPN **and the **Fast-RCNN** detector that uses the proposed regions

* RPN

  * Both RPN and Fast-RCNN share a common set of convolutional layers
  * Generate region proposals by sliding a small network over the convolutional feature map output by the last shared convolutional layer
  * Feature is fed into two sibling fully connected layers—a **box-regression** layer (reg) and a **box-classification** layer (cls).
  * Anchors: The k proposals are parameterized relative to k reference boxes at each location
    * An anchor is centered at the sliding window in question, and is associated with a **scale** and **aspect ratio**
    * An important property of our approach is that it
      is **translation invariant**, both in terms of the **anchors** and the functions that compute proposals relative to the anchors.
    * translation invariant property also reduce the model size in an orders of magnitude aspect.
    * Novel scheme for addressing multiple scales(and aspect ratios). The other two existed popular way: *image/feature pyramids* and *pyramids of filters*. Anchor-based method is built on a **pyramid of anchors**, which is more cost-efficient. 
    * The design of multiscale anchors is a key component for sharing features without extra cost for addressing scales.
  * Loss function
    * positive label to two kinds of anchors
      * Highest Intersection-over-Union(IoU) overlap with a ground-truth box
      * IoU overlap higher than 0.7 with any ground-truth box
      * assign a negative label if its IoU ratio is lower than 0.3 for all ground-truth boxes.
      * Anchors that are neither positive nor negative do not contribute to the training objective
    * Loss function: 
      * $L(\{p_i\}, \{t_i\})=\frac{1}{N_{cls}}\sum\limits_iL_{cls}(p_i, p_i^*)+\lambda\frac{1}{N_{reg}}\sum\limits_ip_i^*L_{reg}(t_i, t_i^*)$
      * $t_i$ is a vector representing the 4 parameterized coordinates of the predicted bounding box, $t_i^*$ is that of the ground-truth box associated with a positive anchor
      * $p_i^*L_{reg}$ means the regression loss is activated only for positive anchors($p_i^*$ is 1 if the anchor is positive)
      * *cls* term is normalized by the mini-batch size and *reg* term is normalized by the number of anchor locations

* Sharing Features for RPN and Fast R-CNN

  * three ways for training networks with features shared:
    * Alternating training
    * Approximate joint training
    * Non-approximate joint training
  * In this paper, author adopt a pragmatic *4-step Alternating Training*

* Experiments

  * pretrained 

  * PASCAL VOC

    * > Using RPN yields a much faster detection system than using either SS or EB because of shared convolutional computations; the fewer proposals also reduce the region-wise fully-connected layers’ cost 

    * Ablation Experiments on RPN.

      * > First, we show the effect of sharing convolutional layers between the RPN and Fast R-CNN detection network. 
        >
        > * Using separate networks reduces the result slightly to 58.7%. 
        > * We observe that this is because in the third step when the detector tuned features are used to fine-tune the RPN, the proposal quality is improved.

      * > Next, we disentangle the RPN’s influence on training the Fast R-CNN detection network.
        >
        > Replacing SS with 300 RPN proposals at test-time
        > leads to an mAP of 56.8%. The loss in mAP is because of the inconsistency between the training/testing proposals. 

      * > Somewhat surprisingly, the RPN still leads to a competitive result (55.1%) when using the top-ranked. Somewhat surprisingly, the RPN still leads to a competitive result (55.1%) when using the top-ranked

      * > NMS does not harm the detection mAP and may reduce false alarms

      * > cls scores account for the accuracy of the highest ranked proposals.

      * > the high quality proposals are mainly due to the regressed box bounds. 

    * Performance of VGG-16

      * significant cost-efficient

    * Sensitivities to Hyper-parameters

      * using anchors of multiple sizes as the regression references is an effective solution.
      * scales and aspect ratios are not disentangled dimensions for the detection accuracy.
      * the result is insensitive to $\lambda$ in a wide range.

    * Analysis of Recall-to-IoU

      * > It is noteworthy that the Recall-to-IoU metric is just loosely [19], [20], [21] related to the ultimate detection accuracy. It is more appropriate to use this metric to diagnose the proposal method than to evaluate it.

      * > the RPN method behaves gracefully when the number of proposals drops from 2000 to 300. This explains why the RPN has a good ultimate detection mAP when using as few as 300 proposals.

    * One-Stage Detection vs. Two-Stage Proposal + Detection.

      * ours is a two-stage cascade consisting of class-agnostic proposals and class-specific detections.

      * > This experiment justifies the effectiveness of cascaded region proposals and object detection. 

      * > We also note that the one-stage system is slower as it has considerably more proposals to process.

  * MS COCO

    * RPN performs excellent for improving the localization accuracy at higher IoU thresholds. 
    * time-consuming is lower than other method

* Conclusions

  * sharing features with the down-stream detection network, the region proposal step is nearly **cost-free**
  * enables a unified, deep-learning-based object detection system to run at near real-time frame rates
  * learned RPN also improves region proposal quality and thus the overall object detection accuracy.