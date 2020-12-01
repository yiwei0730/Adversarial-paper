---
title: A Fourier Perspective on Model Robustness in Computer Vision
date: 2020-11-30 20:00:00
comments: true
author : YiWei
categories:
- meeting
tags:
- Adversarial
---

# A Fourier Perspective on Model Robustness in Computer Vision
###### tags: `adversarial`
在影像視覺上如何提高robustness是一個長期挑戰的目標。
常常大家在使用data augmentation的方式來實行提高robustness, 並且最近常有的是利用Gassuian set Gaussian data augmentation and adversarial training.
這些方法通常會對低頻區域的robustness更好，但對高頻區域的robustness更加淒慘。
若有一個折衷的辦法是可以使資料有多元性的data augmentation，因此我們使用了 [AutoAugment](https://arxiv.org/abs/1805.09501) 這篇論文的方法，state-of-the-art in the [CIFAR-10-C](https://arxiv.org/abs/1903.12261) benchmark.

AutoAugment : E. D. Cubuk, B. Zoph, D. Mane, V. Vasudevan, and Q. V. Le. Autoaugment: Learning augmentation policies from data. In Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition (CVPR), pages 113–123, 2019.
CIFAR-10-C: D. Hendrycks and T. Dietterich. Benchmarking neural network robustness to common corruptions and perturbations. In Proceedings of the International Conference on Learning Representations (ICLR), 2019.

## Introduction
可以看到說其實大部分的Data Augmentation的策略，會增加一些robustness，但不一定是全部層面的，例如:在這篇[Adversarial examples are a natural consequence of test error in noise, ICML2019](http://proceedings.mlr.press/v97/gilmer19a/gilmer19a.pdf)上他也有提到說一般的Gaussian data augmentation和adversarial training會提高模型的robustness在noise和blurring上，但是對於霧的效果就會降低。

**這裡就引出了一個想法，到底Data Augmentation去提高性能和使用策略去降低性能他們之間的差別是什麼?**
了解這些的差別已制定更好的策略來實現模型。
假設-在不同頻率下的損毀會導致不同情形的折衷。
在這篇文章內利用了多次實驗去找到大多的增強過程都在中低頻率上。
```
In particular adversarial perturbations of a naturally trained model are more 
highfrequency in nature while adversarial training encourages these perturbations 
to become more concentrated in the low frequency domain.
```

## Fourier transform
為了驗證模型在訓練過程中也參考了頻率資訊，此圖將圖片透過 Fourier Transform 轉換到頻譜上，直接輸入模型做辨識，模型依然能夠有接近 80% 的正確率，即便圖片經過轉換(高頻率)已經是一張灰色圖片，必須要經過 normalization 才能夠視覺化！
![](https://i.imgur.com/6udFTyu.png)

在圖2中，我們將自然圖像的傅立葉統計數據以及常見損壞的平均增量可視化。
可以看到說其他的大部分在高頻區域被改動，但frog,constrast這兩種同樣在低頻區域上座擾動。
![](https://i.imgur.com/u8RGNgJ.png)

自然訓練的模型對除最低頻率以外的所有頻率的加性攝動高度敏感，而高斯數據增強和對抗訓練均可以顯著提高較高頻率的魯棒性。
![](https://i.imgur.com/bCUNmqm.png)

Does low frequency data augmentation improve robustness to low frequency corruptions?

我們就在訓練資料中加入 fog noise，這樣訓練出來的模型就可以對 fog noise 更 Robust 了吧！(其實並沒有)
![](https://i.imgur.com/gNJVDRS.png)

```
We hypothesize that the story is more complicated for low frequency corruptions 
because of an asymmetry between high and low frequency information in natural images. 
Given that natural images are concentrated more in low frequencies, 
a model can more easily learn to “ignore” high frequency
information rather than low frequency information. 
Indeed as shown in Figure 1, model performance drops off far more rapidly 
when low frequency information is removed than high.

我們假設由於自然圖像中高頻信息和低頻信息之間的不對稱性，
導致低頻失真的故事更為複雜。 如果自然圖像更多地集中在低頻上，
則模型可以更輕鬆地學習“忽略”高頻信息，而不是低頻信息。 
確實如圖1所示，當去除低頻信息時，模型性能下降的速度要比高頻信息下降得更快。
```
## Conclusion
儘管數據增強可能是我們目前針對魯棒性問題最有效的方法，但似乎僅靠數據增強不可能提供完整的解決方案。為此，開發正交方法非常重要，例如具有更好的歸納偏置或損失函數的體系結構，當與數據增強結合使用時，鼓勵外推而不是內插。


















