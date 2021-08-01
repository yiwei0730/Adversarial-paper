---
title: TRADES and RST(Unlabeled Data Improves Adversarial Robustness) and MART
date: 2021-02-26 14:40:00
comments: true
author: Yi-Wei
categories:
- meeting
- read note
tags:
- Adversarial
- note
---
# TRADES and RST(Unlabeled Data Improves Adversarial Robustness) and MART
###### tags: `adversarial`

# TRADES :Theoretically Principled Trade-off between Robustness and Accuracy
```
Identify a trade-off between robustness and accuracy
Although this problem has been widely studied empirically,
much remains unknown concerning the theory underlying this trade-off.
```
在本論文中，他將adversarial examples造成的分類錯誤分成兩種錯誤情況，一種為自然分類錯誤，一種為邊界分類錯誤。
利用這個這兩個的權衡去做出一個防禦方法名為“TRADES”
自然分類錯誤是由輔助的surrogate loss function 所產生的，那輸入的特徵接近於決策邊界則是邊界分類錯誤。
宣稱在[Obfuscated gradients give a false sense of security](https://arxiv.org/abs/1802.00420)這邊文章中的環境，原本的防禦只能有47%的成功率，若是利用TRADES去做防禦可以有57%的防禦力。
透過理論上的將其損失分割為兩個部分自然分類錯誤與邊界分類錯誤，其總和表示 accuracy and robustness之間的權衡
在演算法上，利用替代的損失函數，由兩個部分組成，一個為利用經驗最小化去增進自然分類錯誤，利用正則化的效果讓其遠離邊界函數，減少邊界分類錯誤。（如下圖1）
在實例上，贏得NeurIPS 2018 Adversarial Vision Challenge。
![](https://i.imgur.com/I4GbUx5.png)
## Preliminary
Robust error and Boundary error : 
$\mathcal{R}_{\mathrm{rob}}(f):=\mathbb{E}_{(X, Y) \sim \mathcal{D}} 1\left\{\exists X^{\prime} \in \mathbb{B}(X, \epsilon)\right.$ s.t. $\left.f\left(X^{\prime}\right) Y \leq 0\right\}$.
$\mathcal{R}_{\text {nat }}(f):=\mathbb{E}_{(X, Y) \sim \mathcal{D}} 1\{f(X) Y \leq 0\} .$
$\mathcal{R}_{\mathrm{bdy}}(f):=\mathbb{E}_{(X, Y) \sim \mathcal{D}} 1\{X \in \mathbb{B}(\mathrm{DB}(f), \epsilon), f(X) Y>0\} .$
$\mathcal{R}_{\mathrm{rob}}(f)=\mathcal{R}_{\mathrm{nat}}(f)+\mathcal{R}_{\mathrm{bdy}}(f)$
The trade-off between natural and robust error
![](https://i.imgur.com/l7Ut6mA.png)

## Algorithm
![](https://i.imgur.com/AXjMAQc.png)
與adversarial logit paring 的差別是
![](https://i.imgur.com/sG6N2o2.png)


The whole algorithm:
![](https://i.imgur.com/KQoG6lg.png)


## Result
![](https://i.imgur.com/PmCdDgg.png)
![](https://i.imgur.com/RZH1GMr.png)
![](https://i.imgur.com/KKdw1DR.png)


# Unlabeled Data Improves Adversarial Robustness(RST)
在理論上和證明上說明半監督學習是可以增進對抗性的robustness，使用從80milklion Tiny image取出的500K unlabeled image sourced, and use the robust self-training.
原有的標籤製造出準確的分類器，利用未標記的標籤得到偽標籤，並使用自我訓練。
測試了無標籤數據和自我訓練對robustness所會造成的影響，利用Trades for $l_\infty-robustness$, 利用stability training and randomized smoothing for $l_2-robustness$
## Algorithm 
![](https://i.imgur.com/Bx6dego.png)
Standard learning for generating pseudo-labels.
Robust learning for robustness models.

利用self-training wideResNet28-10去訓練一個11-way classifier，並且移除跟test set長得很像的圖像，之後利用分類器選出每類各50000筆資料(被預測的機率值最高的那些)，總共500K的unlabeled data。

## Results
![](https://i.imgur.com/Et9RkK6.png)
![](https://i.imgur.com/e46UI9i.png)

# IMPROVING ADVERSARIAL ROBUSTNESS REQUIRES REVISITING MISCLASSIFIED EXAMPLES

