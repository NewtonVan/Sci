# Adapting Object Detectors with Conditional Domain Normalization



* target: Incorporate **Conditional Domain Normalization** into various convolution stages of an object detector to adaptively address the domain shifts.  Solve the problem mentioned in the following motivation part.

* Motivation: Existing adversarial methods suffer from several problems
  * Semantic features, which is used to directly fed into a domain discriminator to conduct domain confusion learning, contain both image contents and domain attribute information. Focusing on removing domain-specific information without inducing undesirable influence on the images contents is difficult.
  * Using domain confusion learning at one or few convolution stages to handle the distribution mismatch, which is the usual choice of existing adversarial adaptation methods,  ignored the differences of domain shifts at various representation levels. 
  
* Steps:

  * CDN utilizes a domain embedding module to learn a domain-vector to characterize the domain attribute information, through disentangling the domain attribute out of the semantic features. 
  * Encode the semantic features of another domain's inputs, using domain-vector learnt in step 1, via conditional normalization.
  * Then different domain features carry the same domain attributes information. 

* Method

  * General Adversarial Adaptation Framework
  * Conditional  Domain Normalization(Have math problem here, how to understand the meaning of the formula in the article)
  * Adapting Detector with Conditional Domain Normalization(Unfamiliar with GAN, to be continued)

* Experiments

  * > Extensive experiments show that CDN outperforms existing methods remarkably on both real-to-real and synthetic-to-real adaptation benchmarks, including 2D image detection and 3D point cloud detection.

  * In my opinion, not that significant.

  * Results on Cityscapes to Foggy Cityscapes: 

    * good generalization capability  

    * > CDN outperforms previous state-of-the-art methods by a large margin of 1.8% mAP

  * Results on KITI to Cityscapes:

    * > CDN achieves 1.7% mAP improvements over the state-of-the-art methods

    * instance feature alignment contributes to a larger performance boost than global counterpart

  * Results on SIM10K to Cityscapes

    * > outperforms the previous state-of-the-art method by 1.6% mAP

  * Results on Synthetic to Real Data: 

    * > CDN consistently outperforms SWDA under different backbones, which achieves average 2.2% mAP and 2.1% mAP improvements on Faster-R18 and Faster-R50 respectively

  * Results on 3D Point cloud Detection:

    * > constantly outperforms the state-of-the-art method PointDAN  across all categories, with an average improvement of 1.9% AP

    * instance alignment contributes to a larger performance boost than global alignment

* Analysis on Domain Discrepancy (to be continued)

* Ablation Study

  * > adopting CDN in each convolution stage is a better choice than only aligning domain distributions at one or two specific convolution stages

  * > Leveraging the domain-vector to align the different domain distributions can be more efficient.

  * > the learned domain embedding well captures the domain attribute information, and it is free from some undesirable regularization on specific image contents.

  * > stronger instance regularization largely contributes to detection performance