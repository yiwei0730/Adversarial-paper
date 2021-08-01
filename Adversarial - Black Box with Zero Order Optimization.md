---
title: Adversarial - Black Box with Zero Order Optimization
date: 2020-05-21 17:20:00
comments: true
author: Yi-Wei
categories:
- meeting
- read note
tags:
- Adversarial
- note
---

# Adversarial - Black Box with Zero Order Optimization
###### tags: `adversarial`
# What is Adversarial attack? 
Original paper : [EXPLAINING AND HARNESSING ADVERSARIAL EXAMPLES
Ian J. Goodfellow, Jonathon Shlens & Christian Szegedy Google Inc., Mountain View, CA  ICLR 2015](https://arxiv.org/abs/1412.6572)
![](https://i.imgur.com/jiuYOw1.png)

## Attack settings : White or Black
White box : 你知道他的結構以及可以使用他中間的參數, 可以使用BP去計算
Black box : 你並不知道他是用什麼的結構去training, 只知道他最後的output的機率分佈
gray box  : 就是介於白盒和黑盒之間(例如：你可能知道一半的架構或是只知道一半的機率分佈等等...)
restricted box : 以圖片來說可能你根本什麼都無法知道（我也不知道這是什麼鬼，沒研究）
![](https://i.imgur.com/2MV8BRD.png)

## The Introduction White Box Attack (最有名的幾個例子)
1. [Fast gradient sign method (FGSM)](https://arxiv.org/abs/1412.6572): Ian J Goodfellow, Jonathon Shlens, and Christian Szegedy.2014. Explaining and harnessing adversarial examples. 
Use the backpropagation from the Target DNN, stepwised adding a little noise.
[這裡給一個知乎的詳解，有興趣的可以點開來詳細觀看](https://zhuanlan.zhihu.com/p/32784766)
2. [Jacobian-based Saliency Map Attack (JSMA) : Nicolas Papernot, Patrick McDaniel, Somesh Jha, Matt Fredrikson, Z Berkay Celik, and Ananthram Swami. 2016. The limitations of deep learning in adversarialsettings.](https://arxiv.org/pdf/1511.07528.pdf)
Constructing a Jacobian-based saliency map for characterizing the input-output relation of a targeted DNN.
[知乎](https://zhuanlan.zhihu.com/p/33501618)
3. [DeepFool : Seyed-Mohsen Moosavi-Dezfooli, Alhussein Fawzi, and Pascal Frossard. 2016.](https://arxiv.org/pdf/1511.04599.pdf) 
Deepfool: a simple and accurate method to fool deep neural networks.
Callculate the Euclidean distance by the dicision boundary for update same with FGSM.

4. [Carlini & Wagner (C&W) Attack : Nicholas Carlini and David Wagner. 2017. Towards evaluating the robustness of neural networks. In IEEE Symposium on Security and Privacy (SP).](https://arxiv.org/pdf/1608.04644v2.pdf)
這個是最重要的一個 C&W
The most strong white box attack with logit layer and L2 norm.

# 有攻擊就有防守 Defend method
1. Detection-based defense : shown to be effective in detecting adversarial examples by projecting an image to a confined subspace 
(e.g., reducing color depth of a pixel) to alleviate the exploits from adversarial attacks.
(C&W attacked)

2. Gradient and representation masking : using distillation based on the original confidence scores for classification (also known as soft labels) and introduced the concept of “temperature” in the softmax step for gradient masking.
And the representation masking aims to replace the internal representations in DNNs (usually the last few layers) with robust representations to alleviate adversarial attacks.
(C&W attacked Gradent masking)

3. Adversarial training : adversarial examples are jointly used to stabilize training, known as the data augmentation method. 
(Good at defense FGSM and C&W).

Hint : model capacity increasing can make  the model strong.

# The Black Box attack

[論文：(ZOO: Zeroth Order Optimization Based Black-box Attacks to Deep Neural Networks without Training Substitute)](https://arxiv.org/pdf/1708.03999.pdf)

通常來說黑盒攻擊通常會比白盒攻擊來得差，但這篇提出了ZOO的演算法讓黑盒能夠做的跟白盒一樣好。
Main abstract:
- Use an effective black-box attack that is also only has access to the input images and the output with the confidence scores of the targeted DNN

- ZOO is to directedly estimate the gradients of the targeted DNN for generating adversarial examples.

- In the experiment results on MNIST, CIFAR-10, ImageNet show the ZOO attack is as effective as the state-of-the-art white box attack.

## C&W attack (white box)
最主要的部分就是他會提取倒數第二層來使用（logit層）
![](https://i.imgur.com/rH2Cl88.png)

## The Black box attack
為了讓黑盒接近白盒，故讓其maximize的function 也一致化。
但我們需要做兩件事情
1. 更換logit層, 在黑盒攻擊的時候並不會知道logit層的長相，只會有最後的機率分佈，故我們加上一個log讓他趨近於白盒的樣子。
2. BP solving 方法 ： 由於我們不知道其網路架構內到底有什麼，所以我們需要使用另外一個演算法去推估該隊哪些點去做gradient.(ZOO algorithm)

![](https://i.imgur.com/5iZ5AS7.png)

## Zero Order Optimization
The Black Box's Optimization

![](https://i.imgur.com/wbay69l.png)
啊！ 每個點都要微分一次 那麼假設圖片是$64*64*3$
那麼我每一個epoch的微小變動就要跑 $2*64*64*3$ = 24596次/epoch 
這樣實在是太昂貴了，所以我們需要使用 Stochastic coordinate descent
每次的 iteration, 都變成是隨機取點去minimize the objective function.（本篇文章是使用 batch_size = 128 去優化）
![](https://i.imgur.com/M2UEJr1.png)
當然這裡的optimization 也可以使用ADAM去做優化。
由於是一般的gradient你也可以選擇使用最簡單的Newton's去做迭代。
這裡附上兩個演算法，~~應該不會有人沒看過ADAM吧~~
~~ADAM快過Newton就跟Classification不再用SVM一樣~~
![](https://i.imgur.com/UE0A2zF.png)

### 本篇會使用到的技術
1. Dimension reduction : 
Q:圖片太大導致運算太慢怎麼辦？
- 前面有講到圖片的大小，若圖片太大也不好處理，而且會導致迭代速度慢，那我們就希望將輸入圖片維度降維
Let $△x=x−x_0∈ℝ^𝑝，𝐷(𝑦)∈ ℝ^𝑚，𝑚<𝑝$
Replace the $𝐷(𝑦)$ into $△x$
![](https://i.imgur.com/fZDIWlE.png)

2. Hierarchical Attack(分層攻擊)：
Q: 該在哪個維度下使用？
- 在高維度的情況下，你可能需要很長的時間才能找到Adversarial Example. 
- 在低維度的情況下，你可能會根本找不到在這個有限的空間內。
- 應用降維技術可以將$229*229*3$的圖片變成$32*32*3$，但會導致訊息可能不夠，故應用分層攻擊，漸漸地增加你的維度，在你的loss的下降已經很不明顯的時候，可以看到下圖維度增加的時候變化也幾乎都在貝果的周圍附近。
![](https://i.imgur.com/2XXelnm.png)
![](https://i.imgur.com/0uxTJE7.png)

3. Important Sampling:
Q : 是否在迭代時做了很多多餘的迭代？
由於迭代每個點是很貴的，那為什麼我們不去迭代那些重要的點就好？
例如：貝果來說，我們想要改變的應該是貝果本身或附近的點，對於角落的背景應該是沒有任何影響的，但若我們隨機迭代，很有可能會選到很差的點。
- 將圖片分割成$8*8$的區塊，然後去計算他的重要性質，若比較重要的點，他的改變率就會較高，若不重要那他的改變率就會比較低。
- 我們對每個區域中的絕對像素值變化進行max pooling，向上採樣至所需尺寸，然後對所有值進行normalized，機率總和為1。
- 每進行幾次迭代，我們就會根據最近的變化來更新這些採樣概率 。
![](https://i.imgur.com/3jfbZBe.png)

4. The reset ADAM states : ADAM states only when $c*f (x,t)$ reaches 0 for the first time.
## 你真的好棒棒時間

1. Attack on MNIST and CIFAR-10
Result : Success Rate and the $𝐿_2−𝑑𝑖𝑠𝑡𝑜𝑟𝑡𝑖𝑜𝑛$ is close to the C&W and have a reasonable avg time.
FGSM ($L_∞−𝑑𝑖𝑠𝑡𝑜𝑟𝑡𝑖𝑜𝑛$) use with substitute model.
![](https://i.imgur.com/EN9LnWr.png)

2. ImageNet
Running 1500 iteration, attack base on $32*32*3$(original image $229*229*3$), and don’t use the Hierarchical Attack. The average $𝐿_2−𝑑𝑖𝑠𝑡𝑜𝑟𝑡𝑖𝑜𝑛$ is 3 times than white box, but adversarial images are still visually indistinguishable.
![](https://i.imgur.com/V9A93Ir.png)

3.The importance of the Technique
![](https://i.imgur.com/qEWrQaI.png)
- 黑色曲線的下降非常緩慢，這表明分級攻擊對於加速攻擊非常重要(左圖)
- 重要性採樣也有所不同，尤其是在10,000次迭代之後(右圖)
- 上上圖清楚顯示了重置ADAM狀態的好處，如果不重置狀態，最終失真和損耗會明顯增加。

4. result:
- ZOO攻擊成功地將原始類別的概率降低了160倍以上（從97％降低到約0.6％），同時將目標類別的概率提高了1000倍以上（從0.0006％提高到超過0.6％)

## The Future work
- 持續減少計算時間。
- 用黑盒製造對抗性資料拿去當training有助於robustness。
- 生成對抗性資料不一定只能在CNN上，RNN或是其他的應用都可以使用到。

