---
title: Adversarial Reprogramming of Neural Networks
date: 2020-08-25 16:50:00
comments: true
author: Yi-Wei
categories:
- meeting
- read note
tags:
- Adversarial
- note
---


# Adversarial Reprogramming of Neural Networks

###### tags: `adversarial`
簡單介紹 ： 這個attack可以在更改模型的輸入下改變模型的輸出分佈，即使這個模型沒有被這個輸入訓練過。
![](https://i.imgur.com/VZZuk48.png)
Adversarial Reprogramming 有點類似 parasitic computing.

## Method 
$P = tanh(W \odot M)$ with the adversarial program, $W\in \mathbb{R}^{n\times n\times 3}$是要被訓練的, $M = 0 or 1$ 是Masking Matrix. $M$ 並不是必須品，只是把中間區域空下來，讓圖片保持純淨，並且利用tanh()讓函數界線在(-1,1)
$\tilde x \in  \mathbb{R}^{k\times k\times 3}$是我們的目標輸入, k<n是必須要的

$X_{adv} = h_f(\tilde x;W) = \tilde X + P$

hard-coded mapping function : 設定的有點寬鬆，假設我目標label有十類，那我可能可以就取前十類去對應，也可以隨便選十個，也可以多個對應一個。

Adversarial goal is to maximize $P(h_g(y_{adv})|X_{adv})$, So we set up the optimize problem with ![](https://i.imgur.com/peWisFO.png).
## Results
實驗了六種結構在ImageNet上，以及三種資料集：counting squares, MNIST classification, and CIFAR-10 classification.
圖片的展示：![](https://i.imgur.com/eS63DHs.png)
簡單拼裝過程：
首先原圖假設為$36 \times 36 \times 3$ ![](https://i.imgur.com/uRCwPXl.png)
接下來將其拼裝成$299 \times 299 \times 3$, 並將中間留給原本的圖片![](https://i.imgur.com/cOp2Afi.png)
接著只訓練一個adversarial program per ImageNet model, 並且利用mapping 對應target label.
![](https://i.imgur.com/QKhvxVs.png)
結果顯示MNIST, CIFAR-10 都是可以reprogramming in ImageNet.
並且即使使用了adversarial training 依樣很容易能夠破解，並且成功率只下降一點點。(當然原因不是因為對抗性攻擊無效，是因為目標的不同，這個目標是我要讓網路的用途不一樣而不是去攻擊網路)
當然這裡也展示了，如果你的模型本來就很差(untrained)那再編程的效果也不會好。

接下來，問題1 是否縮小擾動範圍會有問題，答案是不會(如下圖a)
問題2 是否降低擾動的大小會有問題，這裡限制的擾動為$L_{inf}$ norm，答案是不會(如下圖b)
問題3 shuffle image 有助於 把攻擊的圖片給結構溶解，但這樣的話還能reprogramming? 答案是可以，但是準確率會下降。
![](https://i.imgur.com/VRKlS8l.png)


## Discussion
1. 圖片的靈活性，並且完整訓練的模型，更容易受到重新編程的攻擊。
2. 其他領域的reprogramming的可能性，應該是有可能的，但是例如ＲＮＮ來說可能就要有特別一點的設計。並且也有可能造成一些危害，例如有人濫用運算資源去搞事情。