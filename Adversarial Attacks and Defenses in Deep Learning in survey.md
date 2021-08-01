---
title: Adversarial Attacks and Defenses in Deep Learning in survey
date: 2020-09-24 20:30:00
comments: true
author: Yi-Wei
categories:
- meeting
- read note
tags:
- Adversarial
- note
---


# Adversarial Attacks and Defenses in Deep Learning
2020 Janurary
###### tags: `adversarial`

## Attack
1. L-BFGS : ![](https://i.imgur.com/F8Fe6Ff.png)
2. FGSM : untarget-![](https://i.imgur.com/oHayXhQ.png) and target-![](https://i.imgur.com/aoOQmA6.png)
3. BIM : ![](https://i.imgur.com/6z6r9ok.png)
4. PGD : ![](https://i.imgur.com/z2dK5wj.png)
5. Momentum iterative FGSM : ![](https://i.imgur.com/dvIswKa.png)
6. Distributionally adversarial attack : ![](https://i.imgur.com/NZlY7Eq.png)
7. CW attack : ![](https://i.imgur.com/c63NO68.png)
8. JSMA : ![](https://i.imgur.com/gHe9Fia.png)
9. DeepFool : ![](https://i.imgur.com/Gk7fmbP.png)
10. Elastic-net attack : ![](https://i.imgur.com/YGPwO3y.png)
11. Universal adversarial learning : https://arxiv.org/abs/1610.08401
12. adversarial patch : In the restricted region/segment of the benign samples can also fool DL models. 
13. GAN-based attacks : propose the generation of adversarial samples with generative adversarial networks (GANs)
14. Practical attacks :[Robust Physical-World Attacks on Deep Learning Models](https://arxiv.org/abs/1707.08945) successfully generates printable adversarial perturbations on real-world traffic signs and achieved more than 80% attack success rate overall.
15. Obfuscated-gradient circumvention attacks : For the defenses that yield exploding or vanishing gradients caused by optimization loops, it proposes to make a change of variable such that the optimization loop will be transformed into a differentiable function. Using the gradients approximated by those three methods, it successfully breaks seven out of nine heuristic defenses in ICLR2018.

## Defend
### Adversarial Training : 
An minmax game with ![](https://i.imgur.com/rwE5mZZ.png), The J is the adversarial loss.
- FGSM adversarial training : 有效地增加防禦效果，但是遇上iterative/optimization-based algorithm 就沒轍。
- PGD adversarial training : PGD攻擊可能是通用的一階$L_{\infty}$攻擊, 可以防禦$L_{\infty}$的攻擊(FGSM,PGD and $CW_{\infty}$)，但是對於其他種類的例如:EAD and $CW_2$
- Ensemble adversarial training : 由於上述方法容易造成黑盒攻擊的問題，此方法從多個預先訓練的模型中轉移的對抗性樣本可以在某些條件下對於黑盒有好的防禦優於PGD adversarial training
- Adversarial logit pairing : [論文](https://arxiv.org/abs/1803.06373)，除了增加了adversarial 的loss 還額外增加了兩個結果相似性的$L_2$ loss.
- Generative adversarial training : Specifically, the proposed work sets up a generator, which takes the gradients of the trained classifier with respect to benign samples as inputs and generates FGSM-like adversarial perturbations.

### Randomization : 
由於DNN對於隨機擾動的防禦是有robustness，黑盒和灰盒的效果不錯，但是在白盒的效果差了一點
- Random input transformation : 利用兩個隨機變換（隨機大小調整和填充）來減輕推理時的對抗性影響.![](https://i.imgur.com/GebCrQZ.png)
- Random noising : random self-ensemble (RSE) add a noise layer in the front of the convolution.![](https://i.imgur.com/nvesUYd.png) Differential privacy(DP)-based defense is called PixelDP.PixelDP incorporates a DP noising layer inside DNN to enforce DP bounds on the variation of the distribution over its predictions of the inputs with small, norm-based perturbations 
- Random feature pruning : [有一種是把模型參數給修剪](https://arxiv.org/pdf/1803.01442.pdf)，[有一種是把模型輸入給掩蓋](https://arxiv.org/abs/2007.14249)
### Denoising : 
有兩個方向，一個是輸入去噪試圖減少輸入圖案的擾動，一個是特徵去噪減少模型學習對於對抗性擾動的影響力。
- Conventional input rectification : In [Xu W](https://arxiv.org/abs/1704.01155)論文中使用了兩種方法去噪 “bit-reduction and image-blurring”, 通過轉換圖像和原始圖像的預測差別去分辨是否是對抗性示例。![](https://i.imgur.com/GWKNsvw.png) 但是 [Sharma and Chen PY](https://openreview.net/pdf?id=SkV0ptkvf) also show that EAD and $CW_2$ can bypass the input squeezing system with increasing adversary strength.
- GAN-based input cleansing : 兩種基本的論文一個是defense-gan(利用GAN去做重構後循環一直使用GAN創造原生影像)![](https://i.imgur.com/NwuYJAW.png)，一個是APE-GAN(訓練的時候將adv image和 origin image 分開使用不同架構training 後再使用相同架構做training)![](https://i.imgur.com/XQWCVmY.png)
- Auto encoder-based input denoising : MagNet
- Feature denoising : [Liao and Wagner](https://arxiv.org/pdf/1712.02976.pdf) propose a high-level representation guided denoiser (HGD), instead of denoising in the pixel level, trains a denoising u-net.
### Provable defenses : 
由於以上的攻擊都是需要實驗來驗證的，接下來會舉出一些例子可以證明的防禦方法。
- Semidefinite programming-based certificated defense : [Certified defenses against adversarial examples.](https://arxiv.org/abs/1801.09344)提出了一種針對兩層網絡上對抗性示例的可證明防禦方法, 
- Dual approach-based provable defense : 提出了一個雙重問題，以限制對抗性多態性。他們表明，可以通過在另一個深度神經網絡上進行優化來解決對偶問題。
- Distributional robustness certification : Optimization over this distributional objective is equivalent to minimizing the empirical risk over all the samples in the neighbor of the benign data—that is, all the candidates for the adversarial samples. 

### Weight-sparse DNNs
https://arxiv.org/pdf/1810.09619.pdf
證明了sparse and robustness之間的關聯性
https://papers.nips.cc/paper/6821-formal-guarantees-on-the-robustness-of-a-classifier-against-adversarial-manipulation
minimizing the Lipchitz constant can help improve network robustness
同樣證明了權重稀疏性是會有助於解決問題
https://arxiv.org/pdf/1702.01135.pdf
利用L1-regularization 去增進網路稀疏性，並且權重稀疏度顯著加快了線性規劃求解器的速度以驗證網絡的健壯性

### KNN-based defenses
[論文１](https://arxiv.org/abs/1706.03922) 首先開發了一個分析K-NN robustness，並且用1-knn去找出鄰近的點，並且拒絕相反的點，較小攻擊較弱，較大攻擊較強
[論文２](https://arxiv.org/abs/1803.04765) DKNN 通過每層的knn數據觀察同一標籤並不接近時，視為異常，在CW攻擊下，其防禦有明顯的提升。(若檢測出錯誤，則利用KNN去尋找相似點)
### Bayesian model-based defenses
利用Bayesiam model + adversarial training 創造出一個在對抗攻擊下最優的模型分佈

### Consistency-based defenses
Use The consistency imformation 去分辨良性或對抗性圖像
在語義分割任務上，如果對某個地方做了改動，可能會導致附近的一致性也被破壞，這也就可以利用來分辨
在語音識別上，利用一致性的策略可以做到90%的偵測效果。


## Discussion
### White-box and black-box attacks
White Box : 可進入觀察參數以及防禦模型，但是由於此原因所以被針對性攻擊的機率就會很高
Black Box : 防禦性高，對方必須從有限的訊息出推算梯度，最佳解可用zero-order optimization 但是其訪問次數過多也是一種問題。

### Differences between the trends of adversarial attacks and defenses

第一個方向是設計更有效，更強大的攻擊，以評估各種新興的防禦系統

第二個方向是在物理世界中實現對抗攻擊, 有成功的案例是攻擊了LiDAR的攻擊系統

但防禦方面，大多數的啟發式防禦都無法有效防禦白盒攻擊，其防禦上的可伸縮性，邊界分析都是DNN上的一個挑戰
主要也是因為防禦比攻擊更容易接受到挑戰

### Recent advances in model robustness analysis
由於高深度的深度學習模型是很難去分析其robustness 由於其本身的complicated non-convex properties. 從分析簡單的模型開始，即使是KNN模型，也需要越高的k才能有越好的robustness
shows that the adversarial robustness scales in $O(\sqrt\frac{1}{d})$ compared with the robustness to uniform random noise.(d is the dimension of data)

### Main unresolved challenges
以前可能認為是需要有策略性和適當的架構才能提升robustness，但近期研究發現模型的脆弱性可能與訓練數據不夠或是高微幾何空間上的訓練有關係。
那是否可以用特定的訓練方法來取得分類的健壯決策邊界？ 答案為否
有被證明出$L_2$ and $L_{\infty}$數據集越大那他們之間的邊界就越長的不一樣。
### Effective and efficient defense against white-box attacks
目前來說有效性和效率之間都無法做出一個平衡。
基於隨機和基於降噪的方法都很迅速但也被證明出是不有效的。


## Conclusion 
仍然沒有同有擁有速率和效率的防禦機制
即使是對抗式訓練，其所需要的計算量是很昂貴的，並且許多啟發式的防禦都已被證明會被白盒攻擊所擊潰。
