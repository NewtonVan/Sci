# Test-Time Training with Self-Supervision for Generalization under Distribution Shifts

***

* Motivation: 

  * Generalization does not anticipate the distribution shifts, but instead learns from them at test time. 
  * *By contrast, Adversarial robustness and domain adaptation are  but a few existing paradigms that try to anticipate differences between the training and testing distribution with either topological structure or data from the test distribution available during training.* 
  * The unlabeled test sample x presented at test time gives us a hint about the distribution from which it was drawn.

* Proposed idea

  * Allowing the model parameters $\theta$ to depend on the test sample $x$, but not its unknown label $y$.
  * design a feedback mechanism from $x$ to $\theta(x)$
  * test-time training method creates a **self-supervised** learning problem based on this single test sample $x$, updating $\theta$ at test time before making a prediction.
  * **self-supervised** method: using an auxiliary task that **automatically creates labels** from unlabeled inputs. *In this paper, the author use the task of rotating each input image by multiple of 90 degrees and predicting its angle*

* Method

  * Training(standard empirical risk): $\min\limits_{\theta}\frac{1}{n}\sum\limits_{i=1}^{n}l_m(x_i, y_i; \theta)$
  * Testing(*self-supervised auxiliary task*, *standard test error*): $l_s(x_i; \theta_s, \theta_e)= \mathbb{E}(l(x, y), \theta)$
  * Joint training problem: $\min\limits_{\theta_e, \theta_m, \theta_s}\frac{1}{n}\sum\limits_{i=1}^{n}l_m(x_i, y_i; \theta_m, \theta_e)+l_s(x_i; \theta_s, \theta_e)$

    * $\theta_e$ is shared feature extractor, which is shared by both main task and auxiliary task
    * $\theta_s$ is task specific parameters, which is called *self-supervised task branch*
    * $\theta_m$ is owned by main task only, which is called *main task branch*
    * The joint architecture is a *Y*-structure with shared bottom and 2 branches.
  * TTT fine-tunes the shared feature extractor $\theta_e$ by minimizing the auxiliary task loss on x, which can be formulated as $\min\limits_{\theta_e}l_s(x; \theta_s, \theta_e)$. Empirically, the difference is negligible between minimizing this equation over $\theta_e$ versus over both $\theta_e$ and $\theta_s$.

* Online Test-Time Training

  * Non-online TTT method discard $\theta_e^*$ after making a prediction on $x$
  * The online version will change its $\theta_e$ after the prediction

* Experiment

  * Use GN instead of BN

  * Object Recognition on Corrupted Images

    * > CIFAR-10-C, TTT improve over the object recognition task only baseline by a large margin. The stardard version always iproves significantly (>10%) over joint training and never hurts by more than 0.2%. Specifically, TTT-Online contributes >24% on the three noise types and 38% on pixelation

    * >  TTT further improves on the original test set by 0.2% consistently over multiple independent trials. This suggests that our method does not choose between specificity and generality.

    * > ImageNet-C: The general trend is roughly the same as on CIFAR-10-C. The standard version of TTT always improves over the baseline and joint training, while the online version only hurts on the original by 0.1% over the baseline, but significantly improves (by a factor of more than three) on many of the corruption types.

    * > ImageNet-C, the accuracy (averaged over a sliding window) of the online version changes:
      >
      > * On the original test set, there is no visible trend in performance change after updating on the 50,000 samples.
      > * With corruptions, accuracy has already risen significantly after 10,000 samples, but is still rising towards the end of the 50,000 samples, indicating room for additional improvements if more samples were available

    * > Comparison with unsupervised domain adaptation: TTT-Online outperforms UDA-SS on 13 out of the 15 corruptions as well as the original distribution. 
      >
      > TTT-Online has the flexibility to forget the training distribution representation, which is no longer relevant. This suggests that in our setting, forgetting is not harmful and perhaps should even be taken advantage of.

    * > Gradually changing distribution shifts: TTT-Online still improves upon joint training (and our standard version) with this relaxed assumption, and even upon UDA-SS for the first two noise types.

  * Object Recognition on Video Frames

    * The Robust ImageNet Video Classiﬁcation (VID-Robust)
    * Videos of objects in motion
    * 7 classes from CIFAR-10
    * 30 classes from ImageNet
    * Train on CIFAR-10/ImageNet
    * Test on video Frames

* Theoretical Results: Theorem mentioned in this section reveals gradient correlation as a determining factor of the success of TTT in smooth and convex case. 