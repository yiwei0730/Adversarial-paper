---
title: A Defense Against Adversarial Attacks Using Deep Denoising Sparse Autoencoder(DDSA)
date: 2020-11-03 13:10:00
comments: true
categories:
- meeting
tags:
- Adversarial
---

# DDSA: A Defense Against Adversarial Attacks Using Deep Denoising Sparse Autoencoder
###### tags: `adversarial`
精心製作的細緻擾動會大大降低其性能並導致毀滅性後果，尤其是對於自動駕駛汽車，醫療保健和面部識別等對安全要求嚴格的應用而言
使用了基於DDSA的方式在前處理的步驟執行，其在白,灰,黑盒子上都有不錯的效果。
## Introduction 
在很多對安全敏感的應用上出現了很多DNN模型，因此robustness就會是一個對於抵抗adversarial attack的關鍵。
在圖像分類上所製造出來的adversarial attack被人類正確分類但機器錯誤分類。
這是可能的，因為所有的DNN model 都是基於(Independent and Identically Distributed)IID 的假設，這就代表其實正常的數據集都是落在相似的數據分佈或相同的流形上。因此攻擊方就是去最大化他的loss，但要讓他的擾動不被察覺並且還要導致分類錯誤。
最主要的想法也就是在尋找最短的方向，push input image out to the decision boundary to the target class(target attack) or any other class(untarget attack).(See Figure 1)
![](https://i.imgur.com/IayB4rW.png)

這裡有三種不同攻擊方式
1. white box attack : 對方知道你所有的防禦方法，模型架構以及你的參數。
2. black box attack : 對方不知道你的模型架構和參數，攻擊方只會知道你的模型輸出(標籤或是信心值), 攻擊方可能會訓練另外的模型或是替代模型，利用adversarial example的傳遞性去實作。
3. gray box attack : 通常來說介於白盒和黑盒之間，在這篇論文下，攻擊方可以完全訪問模型的體系結構和參數，但對防禦技術一無所知。
![](https://i.imgur.com/8MspWOZ.png)
在本文中使用DDSA的圖像預處理，再輸入階段降低噪聲，再將轉換過後的圖片做輸入丟入分類器
在一般的模型情況下，隨著圖像在網絡中傳播，對抗性擾動會逐漸增加，這會導致噪聲較大的特徵圖和不適當的激活，從而有分類錯誤的風險，故在這裡使用了稀疏約束的架構，他要確保神經元僅針對有意義的Neuron去觸發，從而限制那些被觸發的adversarial pattern. 利用稀疏約束的效果去抵禦有挑戰性的攻擊，得到還不錯的robustness.
![](https://i.imgur.com/mJrYqfV.png)

## Related work
先介紹一下什麼是adversarial attack(problem formulation)，接著介紹一些攻擊的方法，再介紹一些防禦的方法
### Problem Formulation
給你一張圖片的Space $X = [0,1]^{H\times W\times C}$, target classification model $f(\cdot)$. 我們的正常輸入圖片為$x \in X$而 adversarial example $x' \in X$ 但目標是 $f(x) \neq f(x')$ and $d(x,x') \leq \epsilon$, where $d(\cdot)$ is the distance function with three types $L_0$ distance, Euclidean distance($L_2$) and Chebyshev distance($L_\infty$) and the $\epsilon \geq 0$ is the threshold.
### Adversarial attack
1. Fast Gradient Sign Method(FGSM) : $x' = x+\epsilon sign(\nabla_x J_\theta(x,y))$, $\theta$ 是模型的參數,  $\nabla J(\cdot)$計算gradient w.r.t x, $sign(\cdot)$為方向函數, $\epsilon$為小的係數。利用損失函數的一階近似來得到adversarial examples.
2. Boosting Adversarial Attacks with Momentum(Momentum Iterative method MIM) : 增進FGSM攻擊的有效性利用Momentum的習性，$g_{t+1} = \omega g_t + \frac{\nabla_x J_\theta(x'_t,y)}{\|\nabla_x J_\theta(x'_t,y)\|_1}$, $g_t$ 是第t個迴圈的梯度, $\omega$是係數, $\|\cdot\|_1$ is the $L_1$ distance.
最後他的遞迴方程式為 $x'_{t+1} = x'_t + \epsilon sign(g_{t+1})$
3. PGD 
4. CW  
5. RAND+FGSM

### Defense 
1. adversarial training : 
2. defense distillation : 
3. Magnet :


## Proposed Model DDSA
Threat Model : Assume that the attacker has full access to the classification model. (gray box attack), and the adversarial example $x'$ is satisfied $f(x) \neq f(x')$ and $d(x,x') \leq \epsilon$. We don't consider the specific classification model, we defend all types of the classification model against any attack

Deep Denoising sparse auto-encoder(DDSA) : 
This is the whole model, DDSA+classifier(Figure 3)
![](https://i.imgur.com/490NvHr.png)
This is a auto-encoder block in front of the classification model(Figure 4)
![](https://i.imgur.com/LDb8V9L.png)
(Autoencoder 介紹)
自動編碼器利用數據分佈集中在低維流形周圍的事實，並旨在學習該流形的結構。
故目標也是將數據推回原本流形架構上。
在denoising autoencoder下的idea 是我想要學習在autoencoder下的壓縮表示對於adversarial perturbation 是robust的
cost function $J = \frac{1}{n} \sum^n_{i=1}(x_i-f_\theta(x'_i))^2$, $n$ is the number of samples and $x'$ is the perturbed version of $x$.(Use PGD to train this, 因為PGD比較能夠給予更好的wide range)
The training 
$$
\theta^* = \arg\min_\theta[E_{(x,f_\theta(x'))\in\hat p_{data}}(\max_{\delta \in S}J(x,f_\theta(x')))+ E_{(x,f_\theta(x))\in\hat p_{data}}(J(x,f_\theta(x)))]
$$
並且利用增加sparsity constraint to the Fully Connected layers of the proposed DDSA.
希望能夠去限制那些神經元不常被觸發方便去提取有意義的點，並且稀疏約束允許強制隱藏單元的激活等於某個目標激活$\mu$。 當我們使用ReLU激活函數時，$\mu$的值設置為0.1（即接近0的值)
$E_{x\sim D}[a_i^{(j)}]=\mu$, where $x$ is the input image sampled from a distribution $D$ and $a_i^{(j)}$ is the activation of the $i^{th}$ hidden unit at the $j^{th}$ hidden layer, which is the 2nd one in our case.
A running-estimate  $\hat \mu_i$初始值=0
$$
\hat \mu_i = 0.999\hat \mu_i+0.001a_i^{(j)}-(1), b_i^{j-1} = b_i^{j-1} -\alpha \beta (\hat \mu_i-\mu) - (2)
$$ 
the bias term $b_i^{j-1}$ is used as a regulator for our constraint and $\alpha$ is the auto-encoder learning rate.
Thus, when the condition $\hat \mu_i > \mu$ is valid, then we decrease the activation by decreasing $b_i^{j-1}$ and vice versa, with $\beta$ is the learning rate trying to satisfy the **sparsity constraint**.
For gradient step
We first update the weight with $\theta^*$, and then update the $\hat \mu_i, b_i^{j-1}$ using (1) and (2).

## Experiment 
Dataset : MNIST and CIFAR-10
### Results on Black box 
Attacks with FGSM, Rand+FGSM, MIM, PGD, C&W as black-box attackers.
由於這些都是白盒攻擊的例子，故我們使用替代模型去模擬黑盒攻擊, 使用隨機的150筆資料(test set)並利用分類器直接做標記當作我們替代模型的訓練。
MNIST
![](https://i.imgur.com/c9NMabH.png)
DDSA vs DDA

### Results on Gray box
MNIST with DDSA穩定性佳
![](https://i.imgur.com/xLDz4qU.png)

觀察DDSA在不同的擾動情況下的比較
![](https://i.imgur.com/pPDZev6.png)

迴圈數與正確率的圖， more robust than DDA defense.
![](https://i.imgur.com/U5JZ69q.png)

CIFAR-10
![](https://i.imgur.com/IkYmwOh.png)

還原效果
![](https://i.imgur.com/IXeYXjw.png)

### Results on White box
![](https://i.imgur.com/1nyCvqD.png)
