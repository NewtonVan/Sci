# Unsupervised Learning of Visual Representations by Solving Jigsaw Puzzles

***

## Motivation

***

* > By following the principles of self-supervision, we build a convolutional neural network (CNN) that can be trained to solve Jigsaw puzzles as a pretext task, which requires no manual labeling, and then later repurposed to solve object classification and detection. To maintain the compatibility across tasks we introduce the context-free network (CFN), a siamese-ennead CNN

* self-supervise learning: The main idea is to exploit different labelings that are freely available besides or within visual data, and to use them as intrinsic reward signals to learn general-purpose features. 

* Jigsaw puzzle reassembly problem can be used to teach a system that
  an object is made of parts and what these parts are

## Contribution

***

* CFN architecture
* Jigsaw puzzle proxy task
* Permutation Set analysis
* preventing shortcut 



## Method

***

* context free architecture:

  * > We build a siamese-ennead convolutional network , where each row up to the first fully connected layer (fc6) uses the AlexNet architecture [25] with shared weights. Similar schemes were used in prior work [10,39,2]. The outputs of all fc6 layers are concatenated and given as input to fc7. All the layers in the rows share the same weights up to and including fc6.

* Training Method

  * feed multiple jigsaw puzzles of the same image
  * sufficiently large average Hamming distance*(In this way the same tile would have to be assigned to multiple positions (possibly all 9) thus making the mapping of features $F_i$ to any absolute position equally likely.)*
  * leave random gap among the tiles
  * jitter the color channels and use grayscale images
  * label is index of permutation(set before training)

## Experiments

***

### Transfer Learning

***

* details:

  * Notice that while during the training of the Jigsaw task we use stride 2 in the first layer of our CFN, standard stide is 4

* Pascal VOC

  * > outperforming all other methods and closing the gap with features obtained with supervision

* ImageNet Classification

  * > This shows that the conv5 layer starts to be specialized on the Jigsaw puzzle reassembly task.

  * > we can see that semantic training is quite helpful towards recognizing object parts. Indeed, the performance is very high up to conv4

### Ablation studies

***

* Permutation Set

  * Cardinality

    * > We find that as the total number of permutations increases, the training on the Jigsaw task becomes more and more difficult.

    * > Also, we find that the performance of the detection task increases with a growing number of permutations.

  * Average Hamming distance

    * > We find that as the average Hamming distance increases, the CFN yields lower Jigsaw puzzle solving errors and lower object detection errors with fine-tuning.

    * > From those tests we can see that large Hamming distances are desirable.

    * **Greedy algorithm that generate proper Hamming distance need to be checked**

  * Minimum hamming distance

    * > As argued before, the minimum distance helps to make the task less ambiguous.

    * > The best performing permutation set is a trade off between the number of permutations and how dissimilar they are from each other

  * The outcome of this ablation study seems to point to the following final consideration: **A good self-supervised task is neither simple or ambiguous**

* Preventing Shortcuts

  * Low level statistics

    * > This allows the model to find the arrangement of the patches

    * > To avoid this, we normalize the mean and the standard deviation of each patch independently.

  * Edge continuity

    * gap

  * Chromatic Aberration

    * > This type of distortion helps the network to estimate the tile positions. 

    * > crop the central square of the original image and resize it

    * > We train the network with both color and grayscale images. Our training set is a composition of grayscale and color images with a ratio of 30% to 70%

    * > We (spatially) jitter the color channels of the color images of each tile randomly by ±0, ±1, ±2 pixels.

### CFN filter activations

***

* > These activations show that the CFN features correspond to patterns sharing similar shapes and that there is a good correspondence based on object parts 

* > Some channels seem to be good face detectors, and others seem to be good texture detectors

* > We can see that these filters are quite strong and our transfer learning experiments in the next sections show that they are as effective as those trained in a supervised manner.

### Image Retrieval

***

* > the features of the CFN are very sensitive to objects with similar shape and often these are within the same category

## Conclusion

***

* The key idea is that by solving Jigsaw puzzles the CFN learns to identify each tile as an object part and how parts are assembled in an object.