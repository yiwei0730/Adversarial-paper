---
title: Toward Adversarial Robustness via Semi-supervised Robust Training
date: 2021-01-29 13:40:00
comments: true
author: Yi-Wei
categories:
- meeting
- read note
tags:
- Adversarial
- note
---
# Toward Adversarial Robustness via Semi-supervised Robust Training
###### tags: `adversarial`
Adversarial training 是 adversarial defense 方法中 最好之一，鼓勵 x 在他的$\epsilon$ ball內都能被預測為原始類別。
Robust training通過最小化分別針對良性示例和其鄰域的兩個分開的風險($R_{stand}, R_{rob}$)
明確地共同提升準確性和對抗性, 並且也證明了$R_{adv}$的上界為$R_{stand},R_{rob}$

透過minimize $R_{stand}$可以強制預測真實示例，minimize  $R_{rob}$ 則盡量讓接近的預測保持一致。

由於$R_{rob}$不依靠原始類別故為半監督模式(SRT)

# Preliminary
## Preliminaries

classifier $f_w : X\rightarrow [0,1]^{|Y|}$, $X\subset \mathbb{R}^d, Y=\{1,2,...,K\}$ 
$C(x) = argmaxf_w(x)$
Label set $D_L=\{(x_i,y_i)|i=1,...,N_l\}$
adv   set $D'_L=\{x|(x,y)\in D_L\}$
unlabel set $D_U = \{x_i| i= 1,...,N_u\}$
The $\epsilon$-bounded transformation-based neighborhood set of the benign example $x$ : $N_{\epsilon,T}(x) = \{T(x;\theta)|dist(T(x;\theta),x)\leq\epsilon\}$

standard Risk : $R_{stand}(D) = E_{(x,y)~P_D}[I\{C(x)\neq y\}]$
$N_{\epsilon,T}$-based adversarial risk $R_{adv}(D) = E_{(x,y)~P_D}[max_{x'\in N_{\epsilon,T}}\{C(x')\neq y\}]$
$N_{\epsilon,T}$-based robust risk $R_{rob}(D) = E_{(x,y)~P_D}[max_{x'\in N_{\epsilon,T}}\{C(x')\neq C(x)\}]$

With Lemma 1 : $R_{adv}(x) = R_{stand}(x) + (1-R_{stand}(x))R_{rob}(x)$
## Robust training and Semi robust training 
Robust training : if $\lambda=1$ is similar to Adversarial training.
$\min_w R_{stand}(D_L)+\lambda\cdot R_{rob}(D_L)$

Semi-supervised robust training : 
$\min_w R_{stand}(D_L)+\lambda\cdot R_{rob}(D_L\cup D_U)$

![](https://i.imgur.com/ht5UyMG.png)

![](https://i.imgur.com/lPv0Dxw.png)

## Experiments

![](https://i.imgur.com/YdNSz6B.png)
![](https://i.imgur.com/1rvmU4a.png)
![](https://i.imgur.com/ZQou0lt.png)







