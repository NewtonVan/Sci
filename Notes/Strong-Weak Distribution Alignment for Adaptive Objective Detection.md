# Strong-Weak Distribution Alignment for Adaptive Objective Detection

***

* Motivation: 

  * **Domain Adaptive Faster R-CNN for Object Detection in the Wild**. 
  * Global matching likely to hurt performance for larger shifts that affect the layout of the scene, the number of objects and/or their co-occurrence. 
  * Strong alignment of local features would match the texture or color of the domains and should improve performance in most cases. (*local scale in this paper: ~~instance(object)~~ scale but rather texture or color features with small receptive fields*)

* Method

  * Extract global features just before RPN(region proposal networks) and local features from lower layers

  * Perform weak global alignment in the high-level  feature space and strong local alignment in the low-level feature space.

  * Stabilize the training of domain classifiers with the detection loss

  * Weak Global Feature Alignment(key contribution)

    * domain classifier

      * align the target features with the source for the global-level feature alignment
      * train a domain classifier to ignore easy-to-classify examples
      * Because cross-entropy puts non-negligible values of easy-to-classify examples, add a modulating factor $f(p_t)$ to the cross-entropy loss instead of using CE directly. One example of such a loss function is Focal Loss: $f(p_t)=(1-p_t)^\gamma$ , $\gamma$ controls the weight on hard-to-classify examples.
      
    * Feature extractor $F$
    
      * > Our learning formulation optimizes F so that the features are discriminative for the primary task of object detection, but are uninformative for the task of domain classification.
    
  * Weak Global Feature Alignment(key contribution)

    * > The gradients of this loss should change the parameters of low-level layers, which should also align low-level features, but the effect may not be strong enough

    * The architecture of the local domain classifier, $D_l$ is a fully convolutional network with kernel-size equal to one

    * The feature extractor $F$ (*used by Weak Global Feature Alignment*) is decomposed as $F_2\circ F1$, and strong local feature alignment use $F_1$.

    * The loss is designed to align each receptive field of features with the other domain. 

  * Overall Objective 

    * $\mathcal{L}_{det}$ contains the loss for RPN and final classification and localization error. 
    * $\mathcal{L}(F, D)=\mathcal{L}(F_1, D_l)+\mathcal{L}(F, D_g)$
    * overall objective is $\max\limits_D \min\limits_{F, R}\mathcal{L}_{cls}(F, R)-\lambda\mathcal{L}_{adv}(F, D)$
    * On the last formula, $\lambda$ controls the trade-off between detection loss and adversarial training loss.

* Experiments

  * Adaptation between dissimilar domains

    * From *Pascal VOC* to *Clipart, Watercolor*

    * > only with focal loss method, MAP has improved by 10.8% (25.6 to 36.4) compared with **Domain Adaptive Faster R-CNN for Object Detection in the Wild** on clipart

    * >  indicates that strictly aligning feature distributions between different domains can disturb the training for object detection.

    * context-vector based regularization and local-alignment further improve the performance

  * Adaptation between similar domains

    * Only strong local alignment had made significant performance

  * Adaptation from synthetic to real images

    * From Sim10k to Cityscape
    * Replace $f(p_t)$ with another proper function $e^{\eta p_t}$, which also fit the constraint set by the author, also achieved good performance.
    * No positive effect of instance-level adaptation mentioned in **Domain Adaptive Faster R-CNN for Object Detection in the Wild**.  