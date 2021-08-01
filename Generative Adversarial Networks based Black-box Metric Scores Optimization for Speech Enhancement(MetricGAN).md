---
title:  Generative Adversarial Networks based Black-box Metric Scores Optimization for Speech Enhancement(MetricGAN)
date: 2020-08-10 18:10:00
comments: true
categories:
- read note
tags:
- Adversarial
- note
---


# MetricGAN: Generative Adversarial Networks based Black-box Metric Scores Optimization for Speech Enhancement
###### tags: `adversarial`
Propose a novel MetricGAN approach with an aim to optimize the generator with respect to one or more MetricGAN.
The metrics score can be specified with your own task.
In this reference, we use the speech enhancement task.
Moreover, these metrics are generally complex and could not be fully optimized by $L_p$ or conventional adversarial losses.
## Introduction
The adversarial loss should make the generated data indistinguishable from real data.
For the ASR, simpler regression approaches may be preferable to GAN-based enhancement. This is the reason for the discriminator is to judge the real or fake not fully related to the metrics which we consider. And the Adversarial loss still not matched the evaluation metrics. The problem called discriminator-evaluation mismatch(DEM).(Since the more less adversarial loss($L_p$) not guarantee a higher quality or intelligibillity score)
Among the human perception-related objective metrics, the perceptual evaluation of speech quality (PESQ) and short-time objective intelligibility (STOI) are two popular functions to evaluate speech quality and intelligibility, respectively.
For the white box : The complex evaluation metrics with a hand-craft, but you need to know the details of the matrix.
For the black box : Apply RL-based technique to increase the scores. Because the less efficiency in training, most of them have to be pre-trained by conventional supervised learning.
To Solve the above problem and the DEM problem, we change the discriminator with the evaluation matrix, change the target with discrete to continuous.

The main advantages of MetricsGAN are as follows:
1. The surrogate function(discriminator) : It is still in a black-box setting.(no details of metric function)
2. The results of the efficiency to increase metric score is higher than $L_p$ loss.
3. MetricGAN has the flexibility to generate speech with specific evaluation scores.
4. Under some non-extreme conditions, MetricGAN can even achieve multi-metrics assignments by employing multiple discriminators.

## CGAN for SE (origin)
LSGAN with $loss_G$ : Cheating D with a weighting factor $\lambda$.
![](https://i.imgur.com/JUA0tXQ.png)

LSGAN with $loss_G$ : distinguish the real or fake.
![](https://i.imgur.com/FrwywsA.png)

For the improve, the optimized metric scores, we thought D should be associated with the metric.

## MetricGAN the main algorithm
$I$ : the input of the metric.
$Q,Q'$ : The evaluation metric, The evaluation with the interval in [0,1].
For the D similar to Q, the objective function of D:
![](https://i.imgur.com/qYOULr7.png)
Mapping Q to Q', Since Q'(y,y) = 1  
![](https://i.imgur.com/wOTLQzQ.png)

The two difference of (2) and (4):
1. The target label is 0 or 1 in CGAN. The target label is 0~1 in MetricsGAN.
2. The condition used int the D of CGAN is the noisy speech x, which is different from the condition used in the proposed MetricGAN(clean speech y)

The training of G : Since for the efficiency of adversarial loss in MetricGAN. So we just rely on it. $s$ is the desired assigned score(clean speech as 1).
![](https://i.imgur.com/FHzmJka.png)

For all, the G want to cheat D to reach specified score, but D tries to not be cheated by the true score.

## Experiments 
model : 
generator --> BLSTM with two bidirectional layers with 200 nodes. Followed with two fully connected layers, each with 300 LeakyReLU nodes and 257 sigmoid nodes for mask estimation, respectively.

discriminator --> four 2-D convolutional layers : [15,(5,5)],[25,(7,7)],[40,(9,9)],[50,(11,11)], and a global average polling layer to 50 dimension. Followed with three fully connected layers, each with 50, 10, 1.

Adam with $\beta_1$=0.9 and $\beta_2$=0.999

TIMIT Dataset with PESQ and STOI scores. 
MetricGAN (P) : PESQ optimization (denoted as PE policy grad (P))
MetricGAN (S) : STOI optimization (denoted as ST policy grad (S)).
We can see for the s, when we give the higher s, then we will get the more precisely picture in generated speech.
![](https://i.imgur.com/L0Jbd4w.png)


And the last result
![](https://i.imgur.com/Lr1s1Yz.png)



