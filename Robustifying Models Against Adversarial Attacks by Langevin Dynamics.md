---
title: Robustifying Models Against Adversarial Attacks by Langevin Dynamics
date: 2020-10-11 21:50:00
comments: true
author: Yi-Wei
categories:
- meeting
- read note
tags:
- Adversarial
- note
---

# Robustifying Models Against Adversarial Attacks by Langevin Dynamics
2018 
(Local smoothing : make use of the nearby pixels to smooth each pixel, can be designed as Gaussian smoothing, mean smoothing or the median smoothing method，用一個2X2然後取右下角是要改變的，最後看中位數是(兩個則取最大的))
###### tags: `adversarial`
In a similar spirit this paper proposes a novel effective strategy that allows to relax adversarial samples onto the underlying manifold of the (unknown) target class distribution. 
“將對抗性樣本放寬到目標類別的基礎流行上”，所以透過MALA將不在流行上的adv sample推回去目標生成的高密度區域

## Introduction
MALA需要能量函數的梯度，該梯度對應於輸入分佈的對數概率（也稱為得分函數）的梯度∇xlog p（x）。但是，天真地應用MALA會有一個明顯的缺點：
如果存在彼此靠近但不共享同一標籤的高密度區域（群集），則MALA會將樣品驅動到另一個群集中（請參見圖1中的綠線），這會降低分類的準確性。
為克服此缺點，我們用給定標籤y的條件分佈p（x | y）代替（邊際）分佈p（x）。更具體而言，我們的新型防禦方法（稱為防禦的MALA（MALADE））通過對條件梯度使用新穎的估算器來基於條件梯度∇xlog p（x | y）放寬對抗樣本，而無需知道測試樣品。
因此，MALADE將對抗性樣本推向原始類別的數據生成分佈的高密度區域（請參見圖1中的藍線），在該區域中訓練有素的分類器可以預測正確的標籤。
與之前的方法不同的是
考慮到感知邊界，MALADE旨在將輸入樣本驅動到原始類別的數據流形中。
顯著的分散，MALADE不一定要把他回復到原本的樣子而是讓她回復到原群集中的隨機點(更大的隨機性質)-Significant dispersion effectively prevents any whitebox attack from aligning the input so that the MALADE stably moves it to a targeted untrained spot, while the awareness of the perceptual boundary keeps the sampling sequence within the cluster with the correct label.(明顯的分散有效地防止了任何白盒攻擊使輸入對齊，從而使MALADE穩定地將其移動到目標未經訓練的位置，同時感知邊界的感知使採樣序列保持在具有正確標籤的聚類內。)


## Method
1. Metropolis-adjusted Langevin Algorithm (MALA) : MALA is an efficient Markov chain Monte Carlo (MCMC) sampling method which uses the gradient of the energe($x_t+1 = x_t +\alpha∇_x logp(x_t) + k$, $\alpha$ is stepsize, $k \in N(0,\delta^2 I_L)$)
2. MALA defense(MALADE) : 為了克服MALA的缺點，所以加上了標籤，利用p(x)變成p(x|y) $x_t+1 = x_t +\alpha\mathbb{E}_{p(y|x_t)}[∇_xlog p(x_t|y)] + k$，為了計算要訓練一個supervised DAE by minimizing $\mathbb{E}_{p'(x,y)p'(v)}[||r(x+v)-x||^2-2\sigma^2J()r(x+v),y]$