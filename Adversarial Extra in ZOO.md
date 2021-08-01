---
title: Adversarial Extra
date: 2020-05-27 23:20:00
comments: true
author: Yi-Wei
categories:
- meeting
- read note
tags:
- Adversarial
- note
---

###### tags: `adversarial`
# Adversarial
# Adversarial example in some norm
在之前的ZOO可以看到我們創造的是$norm_2$的adversarial example
在JSMA創造的是$norm_\infty$
在EDA創造的是$norm_1$的例子，![](https://i.imgur.com/J8HVHkp.png)

# Adversarial examples in more type
- The use in CNN+RNN : 
![](https://i.imgur.com/yulmN9j.png)
![](https://i.imgur.com/HURZs7U.png)

- The use in speech recognition
![](https://i.imgur.com/dm3GPTQ.png)

- The use in NLP with Sentiment Analysis,Fake-News Detection, Spam Filtering.
![](https://i.imgur.com/Hl93Ovp.png)


# The improve of the ZOO -> AUTOZOO
- [(Auto encoder based Z eroth Order Optimization Method for Attacking Black box Neural Networks)](https://arxiv.org/pdf/1805.11770.pdf)
主要做的有兩點：
1. 使用了更新的gradient算法:
The Old one 
![](https://i.imgur.com/HKUYtV2.png)
The New one : $b \in (1, dimensions)$
![](https://i.imgur.com/eckxV1u.png)
The Sucessful rate in iterations
![](https://i.imgur.com/XNF0E0x.png)

2. Use the dimension reduction:
Here is the two method
Left : Autoencoder
Right: Biliner
![](https://i.imgur.com/ogRDhXg.png)

![](https://i.imgur.com/NsGETCr.png)

# STRUCTURED ADVERSARIAL ATTACK

**ADMM** used to solve the subproblem
![](https://i.imgur.com/76tn9g6.png)
‘ostrich’ is the original label, and ‘unicycle’ is the misclassified label.

將圖片分成一些區塊，並且將那些區塊做loss處理，並且用ADMM將方程式做處理，處理過後的方程式就不列上了，有點長
[連結在此](https://arxiv.org/pdf/1808.01664.pdf)
![](https://i.imgur.com/quQnW0O.png)

![](https://i.imgur.com/kDxduaz.png)
