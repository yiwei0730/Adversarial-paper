---
title: MMA TRAINING - DIRECT INPUT SPACE MARGIN MAXIMIZATION THROUGH ADVERSARIAL TRAINING
date: 2021-07-08 21:30:00
comments: true
author: Yi-Wei
categories:
- meeting
- read note
tags:
- Adversarial
- note
---

# MMA TRAINING: DIRECT INPUT SPACE MARGIN MAXIMIZATION THROUGH ADVERSARIAL TRAINING
###### tags: `adversarial`
https://arxiv.org/pdf/1812.02637.pdf
研究表明說，利用最小化決策邊界上的adversarial loss可以對於最短的成功擾動來達到成效，並且也表示了adversarial loss和邊界上關聯性

這裡提出了的方法是利用maximum margin，利用最大化的邊距來實現adversarial robustness，並以此來做adversarial training。
最後，在實驗上我們在CIFAR-10和MNIST上都有不錯的效果。

```
Instead of adversarial training with a fixed, 
MMA offers an improvement by enabling adaptive selection of
the “correct” as the margin individually for each data point.
MMA 不是使用固定的對抗訓練，而是通過啟用自適應選擇“正確”作為每個數據點單獨的邊距來提供改進。
```

##  MAX-MARGIN ADVERSARIAL TRAINING
利用分組，先分出是分類被攻擊後正確的組別，和不正確的組別，被攻擊後仍然正確的組別為第一項，利用MAX MARGIN去做平衡，希望分類的MARGIN都要比預設的threshold還要大，而分類不正確的組別，則仍然使用一般的adv-training。
![](https://i.imgur.com/UJfHaX0.png)
-感覺中間算是還有很多能討論的

## Algorithm
1.
![](https://i.imgur.com/A0BSWTX.png)
2.
![](https://i.imgur.com/sBJHCjl.png)


