---
title: Adversarial Examples Are Not Bugs, They Are Features
date: 2020-11-20 20:00:00
comments: true
author : YiWei
categories:
- meeting
tags:
- Adversarial
---


# Adversarial Examples Are Not Bugs, They Are Features
###### tags: `adversarial`
Andrew Ilyas, Shibani Santurkar, Dimitris Tsipras, Logan Engstrom, Brandon Tran, Aleksander Madry (ALL in MIT 麻省理工)
NIPS 2019 - 329次引用
由於對抗性的例子引起的極大關注，但是其存在性和普遍性的原因仍不清楚。
在這篇論文中證明adversarial example 通常是non-robust feature存在的原因。
並且利用一些設置去觀察 the misalignment between the (human-specified) notion of robust and the inherent geometry of the data.

## Introduction
Previous view : Adversarial vulnerability is “輸入空間中的高維性質” 或是 “在資料上的統計波動(statistical fluctuations?)”
Author view : “Adversarial vulnerability is a direct result of our models’ sensitivity to well-generalizing features in the data.” 
對抗性弱點是模型對數據中的一般特徵的敏感性的直接結果

那什麼是 robust feature 和 non-robust feature?
人認為的feature v.s. 模型認為的feature 
驗證non-robust feature的存在：(a)他會造成模型的易碎性 (b)他可能比robust feature 更容易被電腦接受

我們通常會訓練一個模型能夠最大化的提高準確度，因此其傾向於使用許多方向的特徵點，即使是人類無法理解的特徵。
我們認為一般模型的學會去利用這些non-robust feature 從而導致我們的模型被攻擊。

故adversarial transferability, 是由於兩個模型所學習到的non-robust feature 太過相似導致能夠被傳遞。
但是non-robust feature 和 robusts feature都同樣重要

## The Features Model 
![](https://i.imgur.com/XRQFFae.png)

![](https://i.imgur.com/BebPWHc.png)

## Finding Features
Robust Feature 的製造, 使用adversarial training model 去創造 $\hat D_R$
利用梯度迭代原本圖像的方式去生出robustness feature
![](https://i.imgur.com/ymuzLm1.png)

Non-Robust feature 的製造
利用misunlabeled 的方式製造不一樣的non-robustness feature
![](https://i.imgur.com/2yj0Hgw.png)

從哪些圖片來得到的$\hat D_R$和 $\hat D_{NR}$
＊未經更動的原始 Cifar10 Standard Dataset, $D$
＊Robust Features from Robust Model with original label,$\hat D_R$
＊Non-robust Feature from Adversarial Training, $\hat D_{NR}$
![](https://i.imgur.com/tvO9rTe.jpg)

這個圖像的表示是不管使用了哪種資訊，仍然擁有不錯的standard learning，並且non-robust的 adversarial accuracy是比其他的feature還要慘的，代表比較容易受到攻擊影響。
![](https://i.imgur.com/iqTpnCA.png)


首先以 $\hat D_{rand}$ 及 $\hat D_{det}$ 兩種方式產生 Non-robust Feature，差別在於前者是隨機產生標籤，所以 Robust Feature 在此完全失效；而後者僅是偏移到下一個標籤，Robust Feature 和偏移後的標籤還是有微小相關性的。
![](https://i.imgur.com/DiHA0id.png)

![](https://i.imgur.com/cIIYNFv.png)
The Transferability
![](https://i.imgur.com/SolH4aT.png)
Five different model in $\hat D_{det}$, Adversarial transferability arises rom utilizing similar non-robust features.
High accuracy -> High transfer rate with adversarial example
![](https://i.imgur.com/DsZTIui.png)

## Similar Work

[Yin, Dong, et al. "A fourier perspective on model robustness in computer vision." Advances in Neural Information Processing Systems. 2019.](https://papers.nips.cc/paper/2019/file/b05b57f6add810d3b7490866d74c0053-Paper.pdf)
The hackmd with myself
-> [A Fourier Perspective on Model Robustness in Computer Vision](/A-77kPv6SQqz9_CxhR-sRw)
