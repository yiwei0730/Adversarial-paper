---
title: Loss function and activation pruning 
date: 2020-12-23 22:20:00
comments: true
author: Yi-Wei
categories:
- meeting
- read note
tags:
- Adversarial
- note
---

# Loss function and activation pruning 
###### tags: `adversarial`
1. k-WINNERS-TAKE-ALL（k-WTA）ICLR 2020: ![](https://i.imgur.com/385EnZ9.png)
將relu 換成 k-WTA, 並且會將k個最大的以下全部歸零
https://openreview.net/pdf?id=Skgvy64tvr
2. STOCHASTIC ACTIVATION PRUNING (SAP) ICLR 2018:https://arxiv.org/pdf/1803.01442.pdf
![](https://i.imgur.com/gZiYlGq.png)
在每一層的後面做pruning, 重複$r^i$次數（代表最後只會留下$r^i$個有數字），利用Draw s~catagorical($p^i$)去找出要保留的s(隨機選擇stochastic
6個不同的方法介紹
- RANDOM NOISY WEIGHTS (RNW)：$M(W^i)_j = (W^i)_j + η, η ∼ N (0, s^2).$
- RANDOMLY SCALED WEIGHTS (RSW)：$M(W^i)_j = η*(W^i)_j  , η ∼ N (1, s^2).$
- DETERMINISTIC WEIGHT PRUNING (DWP) : [Deep Compression: Compressing Deep Neural Networks with Pruning, Trained Quantization and Huffman Coding, Song Han](https://arxiv.org/abs/1510.00149)取最大k%
- STOCHASTIC WEIGHT PRUNING (SWP) : 與SAP相似但是是對於weight去做事情![](https://i.imgur.com/tRbr2Wy.png)

- RANDOM NOISY ACTIVATIONS (RNA) ： $M(h^i)_j = (h^i)_j + η, η ∼ N (0, s^2).$
- RANDOMLY SCALED ACTIVATIONS (RSA) : $M(h^i)_j = η*(h^i)_j, η ∼ N (1, s^2).$

3. Adversarial Defense by Restricting the Hidden Space of Deep Neural Networks ICCV 2019 : https://arxiv.org/pdf/1904.00887.pdf
![](https://i.imgur.com/d8Ei74V.png)
CE loss + PC loss
$x\in\mathbb{R}^m$ is the input, $y\in\mathbb{R}^k$ is the output label, $F_θ(x)$是模型, $θ$是模型參數, The DNN outputs a feature representation $f\in\mathbb{R}^d$, parameters of the classifier can then be represented as $W = [w_1, . . . , w_k]\in\mathbb{R}^{d\times k}$, $w^c$ denotes the trainable class centroids.
![](https://i.imgur.com/i9ubD4Y.png)
![](https://i.imgur.com/SOmK9Rf.png)

4. IMPROVING ADVERSARIAL ROBUSTNESS VIA CHANNEL-WISE ACTIVATION SUPPRESSING (openreview ICLR2021): https://openreview.net/pdf?id=zQTezqCCtNx
![](https://i.imgur.com/z4NAAKU.png)
第l層的activation layer output of network $F$ 為 $f^l \in \mathbb{R}^{H\times W\times K}$
並且在GAP operation下的 channel-wise acitivation $\hat{f^l} \in\mathbb{R}^K$
GAP function ![](https://i.imgur.com/Hvd4mGo.png)
且 $M^l=[M^l_1,M^l_2,...,M^l_C]\in\mathbb{R}^{K\times C}$, $C$ is the number of classes
故上方的Loss為![](https://i.imgur.com/VE080EQ.png)
$\hat{p}^l = softmax(\hat{f^l}M^l)\in\mathbb{R}^C$
並且還有一個$M^l_{y/\hat{y}^l}$, 其training可以利用已知的y去找上面的最重要的$M^l_y$以及testing利用上面的結果去找到最重要的$M^l_{\hat y^l}$，最後去計算出$\tilde{f^l}$
![](https://i.imgur.com/y1x2sWz.png)
將會把$\tilde{f^l}$送到下一層

最後總loss為![](https://i.imgur.com/tLVUAkq.png)













也就是說，如果某個通道的激活計數大於所有512個通道的最大激活計數的1％，則將其確定為已激活。