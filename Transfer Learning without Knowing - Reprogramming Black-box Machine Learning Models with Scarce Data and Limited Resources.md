--- 
title: Transfer Learning without Knowing - Reprogramming Black-box Machine Learning Models with Scarce Data and Limited Resources
date: 2020-8-21 17:00:00  
comments: true 
author: Yi-wei
categories: 
- meeting
tags: 
- Adversarial
---
# Transfer Learning without Knowing - Reprogramming Black-box Machine Learning Models with Scarce Data and Limited Resources
[論文網址](https://arxiv.org/pdf/2007.08714.pdf)
ICML 2020
black-box adversarial reprogramming (BAR)
Using zeroth order optimization and multi-label mapping techniques, BAR can reprogram a blackbox ML model solely based on its input-output responses without knowing the model architecture or changing any parameter

BAR also outperforms baseline transfer learning approaches by a significant margin, demonstrating cost-effective means and new insights for transfer learning.
###### tags: `adversarial`
<!-- more -->
## introduction
In this paper, we revisit transfer learning to address two fundamental questions: 
(i) Is finetuning a pretrained model necessary for learning a new task? 
(ii) Can transfer learning be expanded to black-box ML models where nothing but only the input-output model responses (data samples and their predictions) are observable?

Indeed, the adversarial reprogramming (AR) method proposed in [(Elsayed et al., 2019)](https://arxiv.org/abs/1806.11146) partially gives a negative answer to Question (i) by showing simply learning a universal target-domain data perturbation is sufficient to repurpose a pretrained sourcedomain model.
But in AR it requires backpropagation of a deep learning model with doesn't address Question(ii)

For bridge this gap, we propose a novel approach named black-box adversarial reprogramming(BAR), to reprogram a deployed ML model for black-box transfer learning.
![](https://i.imgur.com/IlYefoV.png)
The following are the substantial differences and unique challenges:
1. Black-box setting : In AR method assumes complete knowledge of the pretrained model.
2. Data scarcity and resource constraint : For the medical data, it may be expensive and clinical trials, expert annotation or privacy-sensitive data are involve. And for the limitation with the large commercial ML model.

In the experiment, BAR can leverage the powerful feature extraction capability of black-box ImageNet classifiers to achieve high performance in three medical image classification tasks with limited data.

## Main purpose of BAR(black-box Adversarial Reprogramming)
To adapt the black-box setting, we use zeroth-order optimization in black-box transfer learning. Also use multi-label mapping of source-domain and target-domain labels to improve the performance in BAR.
The following are the main contribution:
1. BAR is the first work that expand transfer learning to the black-box setting.
2. Evaluate with three medical dataset in BAR.
(1) autism spectrum disorder (ASD) classification; 
(2) diabetic retinopathy (DR) detectionl;
(3) melanoma detection. The result show that our method consistently outperforms the state-of-the-art methods and improves the accuracy of the finetuning approach by a significant margin.
3. Use the real-life image-classification APIs from Clarifai.com for demonstrating the practicality and effectiveness of BAR.

## Related work
Adversarial reprogramming (AR) is a recently introduced technique that aims to reprogram a target ML model for performing a different task.[(Elsayed et al., 2019)](https://arxiv.org/abs/1806.11146)
Different from typical transfer learning that modify the model architecture or parameters for solving a new task with target-domain data, AR remains the model architecture and parameters unchanged.
Zeroth Order Optimization for Black-box Setting : In vanilla AR method, it lacks the ability to the access-limited ML model in prediction API and the backpropagation in the model. So we use Zeroth Order Optimization for Black-box AR for transfer learning.
## Black box Adversarial Reprogramming(BAR) : Method and Algorithm
(1)setting : 
$F(x),  \forall x \in X$ and the $\nabla F(x)$ is in admissible.
Let $X=[-1,1]^d$, $d$ is the (vectorized) input dimension. 
Target domain by $\{D_i\}^n_{i=1} \in [-1,1]^{d'}$, $d'<d$ and for each data sample $i \in [n]$, where [n] denotes the integer set $\{1,2,...,n\}$. 
And let the $X_i$ be the zero-padded data sample containing $D_i$, embedding a brain-regional correlation graph of size $200 \times 200$ to the center of an $299 \times 299$
The transformed data sample for BAR is defined as : $\tilde X_i = \{D_i\}_{padding} + P$, where $P = tanh(W \odot M)$, Trainable parameters : $W \in \mathbb{R}^d$, Binary Mask : $M\in\mathbb{R}^d$, $M_j=0$ means the area is used for embedding $D_i$

(2) Multi-label mapping(MLM):
For AR, we need to map the source task's output labels(different objects) to the target task's output labels(ASD/non-ASD). Muiltiple-source-labels improve the accuracy is more than one-to-one label mapping.
We use the notation $h_j(*)$ to denote m to 1 mapping function
For example : $h_{ASD}(F(X)) = [F_{a}(X)+F_{b}(X)+F_{c}(X)]/3$
More generally : $S \subset [K]$ map to the target $j \subset [K']$, then $h_j(F(X)) = \frac{1}{|S|}\sum_{s\in S}F_s(X)$, where $|S|$ is a cardinality set.
And a frequency-based label mapping is better than randomly chose.

(3) The Loss function of AR:
Since for the softmax function, we get the model output with $\sum^K_{j=1}F_j(X) = 1$ and $F_j(X) \geq 0, \forall j \in [K]$.
For training the adversarial program P parametrized by W, Used the focal loss to imporve the performance in AR/BAR.
Since the focal loss is to focus on the hard example.

---
small talk in focal loss: 
easy example : prediction 高的
hard example : prediction 低的

在訓練的時候常常都會太關注在easy example上，所以有可能雖然一個hard example 的 loss很大，
但是卻跟1000個easy example 一樣大。最主要就是希望在訓練的時候能夠關注hard example,然後忽略一下easy example
![](https://i.imgur.com/s0R3Ph1.png)
故最簡單的方法就是在Cross-entropy 前面加一個係數
![](https://i.imgur.com/u741f2A.png)
但其實只有提升一點點，因為只是一個比例放大放小的問題，故在focal loss裡加入modulating factor, $r \geq0$:
![](https://i.imgur.com/dlCsOkJ.png)
![](https://i.imgur.com/qjrR0tl.png)
再將它們融合一下就變成
![](https://i.imgur.com/T7zkH5V.png)
For $\alpha=1,\gamma = 0$, it is equal to CE loss.

機器學習最終奧義：把別人的變成是我的special case, 那我就變強了。
----------------------

For the ground truth label $\{y_i\}_{i=1}^n$, and the transformed prediction probability $\{h(F(X_i + P))\}_{i=1}^n$.
And the focal loss for above is ![](https://i.imgur.com/dw4U5D7.png)
where $w_j=\frac{1}{n_j} > 0$ is a class balancing coefficient, $n_j$ is the number of the class $j$, $\gamma =2 \geq 0$ is a focusing parameter which down-weights high-confidence(large $h_j$) samples.

(4) The gradient based on Zeroth Order Optimization for BAR
We use Auto-Zoom for gradient. Gradient estimation of $\nabla f(W)$
For any random gradient $g_j = b \cdot \frac{f(W+\beta U_j) - f(W)}{\beta} \cdot U_j$, and get the mean for the $g_j$, ![](https://i.imgur.com/6Uzs2gs.png)

(5) The BAR algorithm
For the $\bar g$, we use SGD with $\bar g$ to optimize the parameters $W$ in BAR. ![](https://i.imgur.com/y0afOfN.png)
minibatchsize n = 20, $\alpha_t$ is the step size with using exponential decay with initial learning rate $\eta$
Training process with : iteration $\times$ minibatchsize $\times$ (q+1) queries.
![](https://i.imgur.com/6Lf6yB4.png)


## Experiment
1. Reprogramming three pretrained black-box ImageNet classifiers form ResNet50, Inception V3, DenseNet121 for three medical imaging classification tasks.
2. Reprogramming two online classsification APIs for ASD.
3. Testing the q and the MLM size m for BAR.
4. Testing the difference of loss function(CE-loss vs. F-loss)
5. Testing the mapping method(random vs. frequency) 

Three baseline : (1) AR method (2) Transfer learning (3) SOTA

### Three dataset:
1. Autism Spectrum Disorder Classification : (ASD,ABIDE database,2 classes)
split in ten folds and 503 ASD + 531 non-ASD samples, test sample=104, data sample is a 200 $\times$ 200 brain-regional correlation graph of fMRI measurements, which is embedded in each color channel of ImageNet-sized inputs. Set $\eta=0.05$ and $q=25$
Table1 is the test performance:
![](https://i.imgur.com/ytwfmml.png)
We can see the performance is similar to AR with Resnet50 and InceptV3 
2. Diabetic Retinopathy Detection: (DR,5 classes)
5400 training data, 2400 testing data. Set $\eta=0.05$ and $q=55$. Use 10 labels per target class for MLM.
![](https://i.imgur.com/b2Pkih7.png)
The current best performance accuracy is 81.36%.(data augumentation on ResNet50)
3. Melanoma Detection : (ISIC database, 7 classes)
It is a kind of skin cancer. Containing 7 types of skin cancer. Imagesize with 450 $\times$ 600 , resized to 64 $\times$ 64 and the data is imbalanced. Perform the re-sampling on the training data to ensure the same sample size for each class. 
train/test data ~ 7800/780.  Use 10 labels per target class for MLM.
![](https://i.imgur.com/rvjecaH.png)
The Best reported accuracy is 78.65%.(data augumentation on DenseNet)

### Reprogramming The Real life Prediction APIs
we want to reprogram them for Autism spectrum disorder classification task.
1. Clarifai Not Safe For Work API : (NSFW API) 
Recognize Image or videos with in appropriate contents.(labels : NSFW or SFW)
2. Clarifai Moderation API : 
Recognize whether images or videos have contents such a “gore”, “drugs”, “explicit nudity”, “suggestive nudity” or “safe”.(labels : 5 classes)
3. Microsoft Custom Vision API : 
use this API to obtain a black-box traffic sign image recognition model (with 43 classes) trained with GTSRB dataset.

Then we seperated the dataset into train/test ~ 930/104(ASD) and 1500/2400(DR). Use random label mapping instead of frequency mapping to avoid extra query cost. 
![](https://i.imgur.com/fY1d3Rg.png)



### Ablation Study and Sensitivity Analysis
1. Number of random vectors (q) and mapping size (m) : 
Resnet 50 ImageNet model to perform ASD classification, DR detection, and Melanoma detection with different q and m values.
![](https://i.imgur.com/U99VVEC.png)
2. Random and frequency multi-label mapping : 
Random mapping : Randomly assign m seperate labels from thje source domain.
Frequency mapping : (1)Obtain the source-label prediction distribution of the target-domain data before reprogramming in each task. (2) Assign the most frequent m source-labels.
For both AR and BAR, frequency mapping roughly 3% to 5% gain in test accuracy.
![](https://i.imgur.com/vJpAwBA.png)
3. Cross entropy loss (CE-loss) and focal loss (F-loss) : 
The Performance increase when using F-Loss in BAR.
F-loss greatly improves the accuracy of BAR by 3%-5%.
![](https://i.imgur.com/6l3ebPx.png)

4. Visualization : 
ResNet 50 feature maps of the pre-logit layer
![](https://i.imgur.com/0pqj8Qh.png)


## Conclusion
1. Proposed a novel black-box adversarial reprogramming framework for limited data classification tasks.
2. Used the multi label mapping and gradient free approach to handle the black-box model without knowing the pre-trained model.
3. Reprogramming the black-box model for three medical imaging tasks and outperformed the general transfer learning model.


## Appendix
![](https://i.imgur.com/Is7aUYT.png)













