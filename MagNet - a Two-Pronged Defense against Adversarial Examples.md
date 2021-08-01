---
title: MagNet - a Two-Pronged Defense against Adversarial Examples
date: 2020-09-14 16:50:00
comments: true
author: Yi-Wei
categories:
- meeting
- read note
tags:
- Adversarial
- note
---


# MagNet: a Two-Pronged Defense against Adversarial Examples

###### tags: `adversarial`

利用MagNet來防禦他人攻擊，其包含了多個檢測器網路和一個重整網路，由於白盒網路的防禦困難性，這裡改成使用灰盒的防禦制度，並且應用了密碼學上的隨機性使得其網路產生多樣性，可以發現在黑盒和灰盒的防禦效果是不錯的
目前來說對抗對抗性範例的防禦方法分為三種情形
(1) 利用adversarial training 訓練分類器
(2) 訓練分類器去分辨正常的圖片和adversarial examples的分類器
(3) 讓攻擊者很難去利用梯度去攻擊，例如:defensive distillation

-----------------------
speculate that for many AI tasks, their relevant data lie on a manifold that is of much lower dimension than the full sample space [23]. This suggests that the normal examples for a classification task are on a manifold, and adversarial examples are off the manifold with high probability.

-----------------------
## Contribution
1. 重新定義了對於防禦對抗性例子的標準
2. 一個新方法去防禦
3. 由於對抗白盒是很困難的，故我們使用灰盒的方式去執行，並使用多樣性的方法去建構防禦。

## Related work
4 attacks:
(1)FGSM : $x′ = x + ϵ sign(∇_xLoss(x, l_x))$
(2)Iterative gradient sign Method : $x′_{i+1} = clip_{ϵ,x}(x_i' + α · sign(∇_xLoss(x, l_x )))$
(3)DeepFool: linear programming 
(4)CW attack : 
$minimize_δ ∥δ ∥_2 + c · f (x + δ)$
such that $x + δ ∈ [0, 1]^n$
4 defends:
(1) Adversarial training
(2) Defensive distillation
(3) Detecting adversarial examples.

## Model 
![](https://i.imgur.com/kltb5ey.png)
defense-gan 的偵測系統 是利用GAN製造出來的圖片差距來看到底有沒有被攻擊。

Threat model : 
Blackbox attack - Don't know the parameters and everything.
whitebox attack - Know the parameters and everything.
Graybox attack - Except for the parameters, know the other things.

MagNet 包含兩個步驟
1. 拒絕那些不再邊界上的圖形
2. 利用重整器找到正常的圖形，並且判斷為何者分類

這裡的偵測系統是利用一個autoencoder，並且loss = $L(X_{train})=\frac{1}{|X_{train}|}\sum_{x\in X_{train}} ||x-ae(x)||_2$ and the reconstruction error $E(x)=||x-ae(x)||_p$(利用p=1 or 2的norm更好)

然後我們使用了diversity in graybox attacks, 根據密碼學的隨機想法，利用多種表現不同的autoencoder(架構相同)去做reformer。

## Result
![](https://i.imgur.com/JKN6C2C.png)
