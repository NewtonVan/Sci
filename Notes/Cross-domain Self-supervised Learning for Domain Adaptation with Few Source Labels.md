# Cross-domain Self-supervised Learning for Domain Adaptation with Few Source Labels

***

## Motivation

***

* State of art UDA train the model on the target domain using full supervision often start with ImageNet pre-trained initialization, and they transfer the knowledge from the source to target by **aligning features**, typically by minimizing the some norm of distributional distance. The ImageNet pre-trained **may not learn discriminative features** on the source domain in the  problem setup of this work.
* Existing SSL methods  with auxiliary task do not consider the **domain gap** and cannot ensure domain-invariant features
* When label source is limited, existing methods fail to learn discriminative features applicable for both source and target domains
* Cross-Domain Self-supervised(CDS) learns features not only **domain-invariant** but also **class-discriminative**

## Proposed Method

***

* domain adaptation framework of this paper
  * Pre-training stage with CDS stage: get pre-trained weights robust to domain shift and efficiently generalizable for later domain adaptation. **purely SSL**
  * domain adaptation stage: apply existing DA model with few source labels

### Pre-training with CDS

***

#### in-domain self-supervision
***

* ID can incorrectly embed image of different categories belonging to different domains nearby each other due to spuriously shared visual characteristics.
* ID discards clustering effects by making every distance far away, which does not learn the feature both class-discriminative and domain-invariant
* in-domain instance discrimination alleviate these problems of ID method, where negative pairs are from in-domain samples in each domain-specific memory bank defined below
* compute similarity distributions by measuring the pairwise similarities between features and the corresponding memory bank
  * $P_i^s=\frac{e^{\frac{(v_i^s)^Tf_i^s}{\tau}}}{\sum\limits_{k=1}^{N_s+N_{su}}e^{\frac{(v_k^s)^Tf_i^s}{\tau}}}$ , and $P_j^t=\frac{e^{\frac{(v_j^t)^Tf_j^t}{\tau}}}{\sum\limits_{k=1}^{N_{tu}}e^{\frac{(v_k^t)^Tf_j^t}{\tau}}}$    
  * $v_i$ is come from the memory bank(initialized by feature extractor $F(\cdot)$, the updated by momentum)
  * $f_i^s=F(x_i^s), f_j^t=F(x_j^t)$
  * $\tau$ is temperature parameter determine the concentration level of the similarity distribution
* perform in-domain discrimination by minimizing the averaged cross entropy losses of each domain in a batch $\mathcal{L}_{W-INS}=\frac{1}{|B^s+B^t|}(\sum\limits_{i\in B^s}\mathcal{L}_{CE}(P_i^s, i)+\sum\limits_{j\in B^t}\mathcal{L}_{CE}(P_j^t, j))$

#### Across-domain Self-supervision

***

* > To explicitly ensure domain aligned and discriminative features between the two different domains, we perform cross domain feature matching.

* To find a match, we minimize the entropy of the pairwise similarity distribution between a feature in one domain and features in the other domain memory bank.

  * $P^{s\rightarrow t}_{i',i}=\frac{e^{\frac{(v_{i'}^t)^Tf_i^s}{\tau}}}{\sum\limits_{k=1}^{N_{tu}}e^{\frac{(v_k^t)^Tf_i^s}{\tau}}}$ and $P^{t\rightarrow s}_{j',j}=\frac{e^{\frac{(v_{j'}^s)^Tf_j^t}{\tau}}}{\sum\limits_{k=1}^{N_s+N_{su}}e^{\frac{(v_k^s)^Tf^t_j}{\tau}}}$ 

* Then we minimize the averaged entropy of the similarity distributions in
  a batch, which clusters source and target features and encourages distribution alignment, as follows: 

  * $\mathcal{L}_{CDM}=\frac{1}{|B_s+B_t|}(\sum\limits_{i\in B^s}H(P_i^{s\rightarrow t})+\sum\limits_{j\in B^t}H(P_j^{t\rightarrow s}))$
  * $H(P_i^{s\rightarrow t})=-\sum\limits_{i'}^{N_{tu}}P_{i',i}^{s\rightarrow t}\log P_{i',i}^{s\rightarrow t}$
  * $H(P_j^{t\rightarrow s})=-\sum\limits_{j'}^{N_{su}+N_s}P_{j',j}^{t\rightarrow s}\log P_{j',j}^{t\rightarrow s}$

* The overall objective for CDS is to minimize

  * $\mathcal{L}_{CDS}=\mathcal{L}_{W-INS}+\mathcal{L}_{CDM}$

* After updating the model with $\mathcal{L}_{W-INS},\mathcal{L}_{CDM}$, update the memory banks with the features in the batch with a momentum (mentioned in explaining $v_i$) $\eta$ following:

  * $v_i^s=(1-\eta)v_i^s+\eta f_i^s$
  * $v_j^t=(1-\eta)v_j^t+\eta f_j^t$



### Domain adaptation

***

* We obtain a pretrained network learned by cross-domain self-supervised learning at stage 1
* Then we apply learning objective functions of a **domain adaptation** method with **labeled source** and **unlabeled target** examples.
* For the **unlabeled source domain**, we apply a **semi-supervised learning** method 
* loss function
  * $\mathcal{L}=\mathcal{L}_{DA}(\mathcal{D}_s,\mathcal{D}_{tu})+\lambda\mathcal{L}_{su}(\mathcal{D}_{su})$
  * $\lambda$ is the hyper-parameter that controls the importance of the semi-supervised learning loss

## Experiment

***

### DA with fewer source labels

***

* Adaptation Results with Few Source Labels

  * > CDS shows a comparable accuracy

  * > As the number of labeled examples decreases, CDS shows higher performance gains against the baselines

  * > These results show that our self-supervised learning scheme is more effective than just naive adaptation of ImageNet pretrained weights, which is generally used in domain adaptation works

* Effects of Different Semi-supervised Learning Methods.

  * > the semi-supervised learning does not help much on the source accuracy for Source-only 
    >
    > entropy minimization can harm accuracy when there are only few labels

  * > However, it is interesting to see that the increased source accuracy harms the target accuracy on the A→D and A→W settings in Table 8-(a), which suggesting that source accuracy is not necessarily consistent with target accuracy

  * > CDS can improve the source and target accuracy at the same time on the A→D and A→W settings with domain-invariant features.

  * > Lastly, we observe that, with CDS, the performance gap of the target accuracy between SO and MME is significantly and consistently smaller than that of ImageNet pre-trained network

* **Self-supervised Learning Method Comparison**

  * > CDS significantly outperforms these baselines by a large margin in all cases.

  * > source labels. This directly assesses the quality of the learned representation. The results evidently show that the performance gain of the domain adaptation test mainly comes from the quality of the representation as shown by large performance margins in Feature Analysis

### Additional Analyses

***

* Information we get from the figure

  * CDS clusters the same class examples in the feature space; thus, CDS favors more discriminative features
  * CDS generates well-aligned and domain-invariant features while preserving the class-discriminative power

* aspect of feature enhanced w.r.t ImageNet pretrained

  * > ImageNet are sensitive to biased color and texture information,

  * > our pretrained weights tend to capture better shape representation with a proper **balance** of color and texture information.

* some methods using proxy task, which is mentioned in this paper, decrease in accuracy over training due to overfitting to the respective proxy task

* CDS consistently outperforms the competing baseline and stably improves the accuracy as the number of source labels is increased; Our CDS has a favorable sample efficiency

* confusion loss(higher, align better) analyses show the CDS achieve more domain-invariant feature than others

### Traditional Domain Adaptation with Full Source Labels

***

* > CDS improves the performance of the state-of-the-art domain adaptation methods including MDD