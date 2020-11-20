---
title: Feature Denoising for Improving Adversarial Robustness
date: 2020-11-20 20:00:00
comments: true
author : YiWei
categories:
- meeting
tags:
- Adversarial
---


# Feature Denoising for Improving Adversarial Robustness
###### tags: `adversarial`
Cihang Xie(1,2) Yuxin Wu(2) Laurens van der Maaten(2) Alan Yuille(1) Kaiming He(2)
1 Johns Hopkins University 2 Facebook AI Research
2019 CVPR - 228次引用
由於圖像攻擊對卷積網路來說產生了挑戰，並且卷積網路可能會提取那些噪音的特徵。
基於這種觀察，我們開發了新的網絡體系結構，該結構通過執行特徵去噪來提高對抗性。

使用了一個塊狀結構去去噪，內部使用了non-local mean or 其他的filter.
Trained End-to-End. 與adversarial training 結合時可以提高robustness.
原本十個迴圈才只有27.9%的準確率，現在可以提高到55.7%，即使最高的2000次迴圈，也可以擁有42.6%準確率。

## Introduction 
此圖為ResNet50所提取的特徵圖可以看到再PGD $\epsilon=16$的擾動下，會讓ResNet注意到其餘不好的雜訊
![](https://i.imgur.com/Y3XxLPC.png)

再結果上來看 最好表現的去噪方法是再network裡面使用non-local means for feature denoising 相關於 self-attention Network and non-local network.
後面的Ablattion study show that using mean filters, median filters, and bilateral filters 都能提高adversarial robustness.

## Related work
Adversarial training 防禦那些 adversarial perturbation by training model on adversarial images
[6] I. J. Goodfellow, J. Shlens, and C. Szegedy.
Explaining and harnessing adversarial examples. In ICLR, 2015.
[10] H. Kannan, A. Kurakin, and I. Goodfellow.
Adversarial logit pairing. arXiv preprint arXiv:1803.06373, 2018.
對x and x'之間的預測做logit相似.
![](https://i.imgur.com/floR3mm.png)
[16] A. Madry, A. Makelov, L. Schmidt, D. Tsipras, and A. Vladu. 
Towards deep learning models resistant to adversarial attacks. In ICLR, 2018. 

pixel denoising 
[15] F. Liao, M. Liang, Y. Dong, and T. Pang. 
Defense against adversarial attacks using high-level representation guided denoiser. In CVPR, 2018.
In constrast, our denoising in feature.

Transform method with non-differentiable image preprocessing
[[8] C. Guo, M. Rana, M. Cisse, and L. van der Maaten. 
Countering adversarial images using input transformations. In ICLR,
2018.](https://arxiv.org/pdf/1711.00117.pdf)
這篇他使用了許多transformations 的方法像是image quilting, total variance minimization and quantization. 但這些方法大多只適用於灰盒(自訂)和黑盒上，白盒上例如[Obfuscated Gradients Give a False Sense of Security:Circumventing Defenses to Adversarial Examples](https://arxiv.org/pdf/1802.00420.pdf)這篇的攻擊BackPropagation Differentiable Approximation(BPDA)就可以輕鬆擊潰。
[4] A. A. Efros and W. T. Freeman. 
Image quilting for texture synthesis and transfer. In SIGGRAPH, 2001.
Efros present a simple image-based method of generating novel visual appearance in which a new image is synthesized by stitching together small patches of existing images. They call this process image quilting.
Efros提出一個簡單的圖像基底的方法, 把一些現有的圖像, 相同的縫製在一起, 何成一個新的圖像, 產生與眾不同的視覺外貌。 他們稱這個方法叫做圖像縫製。
[17] L. I. Rudin, S. Osher, and E. Fatemi. 
Nonlinear total variation based noise removal algorithms. Physica D: nonlinear
phenomena, 1992
但在這篇我們是differentiable, and still improve adversarial robustness in white box attack.

## Feature Noise 
當圖片透過網路傳播的時候，adversarial perturbation 會逐漸增加，使得特徵圖中原本不存在的激活產生幻覺而激活，而導致做出錯誤的判斷。

從下圖中可以發現在沒有特徵的地方會有激發的現象
![](https://i.imgur.com/6aEt5i1.png)

下圖為我們嘗試使用特徵去噪的結果，左邊是adversarial image 中間是 feature map 右邊是denoising feature map.
![](https://i.imgur.com/d8FjxBY.png)
這可以顯示他將噪聲的地方很好的抑制住了。

再開始描述方法前，還是要提醒一下，雖然我們可以很容易的觀察到一定的feature noise, 但是他卻很難被測量出來。
並且比較不同模型和架構下或是不同的訓練方式(standard or adversarial)之間的特徵噪聲階層並非易事。
We found it is nontrivial to compare feature noise levels between different models, in particular, when the network architecture and/or training methods (standard or adversarial) change.
儘管如此我們還是相信觀察到的feature noise 可以反映出與對抗圖像的真實模樣。

## Feature denoising paper
1. In Defense against Adversarial Attacks Using High-Level Representation Guided Denoiser(Fang-zhou Liao) purpose a High-Level Representation Guided Denoiser(HGD) to polish the feature affected by the adversarial perturbation. HGD trains a denoising U-Net using a feature-level loss function to minimize the feature-level difference between benign and adversarial examples
![](https://i.imgur.com/rIRIv1U.png)
2. In Towards Robust Neural Networks via Random Self-ensemble(Xuanqing Liu) propose the noise layer and the Ensemble learning. It need to train a model to fit the noise layer model(Randomness) and finally use the ensemble answer to print out the answer(Ensemble).
![](https://i.imgur.com/9SHQ8S3.png)
![](https://i.imgur.com/kUK1Z9x.png)
3. In Adversarial Defense by Restricting the Hidden Space of Deep Neural Networks (ICCV’19)(Aamir Mustafa) for counter adversarial attacks, it propose Prototype Conformity Loss to class-wise disentangle intermediate features of deep neural networks. From the figure, the existence of such adversarial examples is the close proximity of learnt features in the latent feature space.
![](https://i.imgur.com/Q8ZvwWi.png)
![](https://i.imgur.com/IdMtCIN.png)

## Method : Denoising Feature maps 

在卷積層的後面加上去噪的方塊去增加模型的robustness，使用對抗訓練的方式，train in end to end model with all layer of the model. 利用end to end adversarial training 的方式有助於消除依賴於數據上的特徵圖噪聲。通過對更早曾的更動也能更好的處理後面幾層的噪聲。

經驗上來說，最好的消除方法是用於機器翻譯的[self-attention transformers](https://arxiv.org/abs/1706.03762)和用於影像分類的[non-local networks](https://arxiv.org/abs/1711.07971)
在這裡我們專注於去噪方塊的研究，並且也在裡面嘗試的加入了bilateral filtering, mean filtering, and median filtering inside our convolutional networks.

### Denoising Block 
基本的去噪方塊如下圖所示，Denoising operation 是 non-local means 或是其他變體的去噪方法所做，然後再讓其通過$1\times 1$的convolutional layer，然後利用殘差連接做相加。
![](https://i.imgur.com/Cvowv7T.png)
以上主要去噪的只有該denoising operation 其餘的都只是在做特徵的組合。
但是為何不直接執行去噪就好（？），因為去噪也會影響原本的特徵訊息，所以利用殘差連接去保留信號，並且利用$1\times 1$來去做去噪以及保留信號的折衷。
Ablation 會告訴你這兩個是有效的。

### Denoising Operations
1. Non-Local means : compute a  denoising feature map $y$ of an input feature map $x$ by taking a weighted mean of features in all spatial locations $L$.
$$
y_i=\frac{1}{C(x)}\sum_{\forall j\in L}f(x_i,x_j)\cdot x_j
$$
where $f(x_i,x_j)$ is a feature-dependent weighting function(特徵加權函數) and $C(x)$ is a normalization function(正則化函數), $L$ 是空間座標點.
![](https://i.imgur.com/fPL6X14.png)
以下還考慮兩種feature-dependent weighting function form
    - Gaussian(softmax) set $f(x_j,x_i)=e^{\frac{1}{\sqrt(d)}\theta(x_i)^T\phi(x_j)}$, where $\theta(x)$ and $\phi(x)$ 都是convolutional function with $1\times 1$的kernel, $d$是channel數量. $f/C$is the softmax function.
    - Dot Product set $f(x_j,x_i)=x_i^Tx_j$ and $C(x)=N$, $N$ is the number of pixels in x. 不像上面會是weight sum = 1, 但是實驗證明可以抑制噪聲並且提高robustness.
這個版本可以不用使用額外的參數去做訓練。
![](https://i.imgur.com/miZ4cVU.png)
2. Bilateral filter : 其實就是上面non-local means的局部版本
$$
y_i=\frac{1}{C(x)}\sum_{\forall j\in \Omega(i)}f(x_i,x_j)\cdot x_j
$$
$\Omega(i)$ is a local region(eg. a $3\times 3$ patch) around the pixel i.

3. Mean filter : 這個是最簡單的方式均值濾波器，他可以降噪但是會使結構平滑。
![](https://i.imgur.com/wgQC3my.png)
![](https://i.imgur.com/27cy7rC.png)
4. Median filter : Defined as $y_i = median\{\forall j\in \Omega(i):x_j\}$
The median is over a local region $\Omega(i)$ 並且是針對每個channel都去做。
這個方法是很好的去防禦salt and pepper noise 和 相同的異常值。
![](https://i.imgur.com/3LNi79t.png)

### Adversarial training 
這裡使用PGD adversarial training, 這是用來訓練出我們的網路並且也要訓練他本身的去噪能力。

PGD 是個迭代的攻擊方法，將所產生的adversarial image 投影到可行的解決方案空間中，在原始的圖像的每個最大像素擾動範圍內（受到$L_\infty$約束）

Perturbation $\epsilon=16$, stepsize =$\alpha=1$, iteration $n=30$
圖形的初始點可以是乾淨的(20%)，也可以是randomly within allowed $\epsilon$ (80%).
![](https://i.imgur.com/IeZLecK.png)

我們的訓練再mini-natch上，我們使用PGD來生成圖像，並且使用SGD去更新weight (在這裡的訓練資料全部都是PGD生成的adversarial image)
他使用128個Nvidia V100 GPUs, 每個mini-batch 32張圖片
再35,70,95的時候會把learning rate 下降十倍（總共110 epochs）
ResNet101 - 36hr
ResNet152 - 52hr
Dataset : 128萬訓練 images in 1000 classes. 5萬測試集
A label smoothing [C. Szegedy, V. Vanhoucke, S. Ioffe, J.Shlens, and Z. Wojna.Rethinking the inception architecture for computer vision. In CVPR, 2016.](https://arxiv.org/abs/1512.00567) of 0.1 is used.
![](https://i.imgur.com/9wkHa8N.png)

並且攻擊時的white-box PGD $\alpha=1$ (除了10次迭代的用$\alpha=1.6$)),$\epsilon =16$
iteration = 10~2000
## Experiment with result 
將ALP與其他三個模型去比較，由於ALP做了10次的PGD attack有27.9%的成功率。(在[Evaluating and Understanding the Robustness of Adversarial Logit Pairing](https://arxiv.org/pdf/1807.10272.pdf))這篇文章中，提及ALP其實並不robustness有可能會下降到0.6%在PGD的攻擊之下的)
![](https://i.imgur.com/lBrXPXB.png)
baseline(adversarial training without denoising block)
ResNet152 baseline 52.5% -> ResNet152 Denoising 55.7%
![](https://i.imgur.com/6x3ASCv.png)

在長時間的PGD攻擊下，此模型還能有42.6的成功率(+3.4)
[A. Athalye, N. Carlini, and D. Wagner. Obfuscated gradients give a false sense of security: Circumventing defenses to adversarial examples. In ICML, 2018](https://arxiv.org/abs/1802.00420)這個論文告訴我們pixel denoising 的方法可以在白盒的設置下去被破解的
![](https://i.imgur.com/ZJkCNPU.png)

相比較於pixel denoising， feature denosing可以持續的增進，這表明特徵去噪塊使欺騙網路更加的困難。

### Ablation study:
不同種類的去噪方塊，我們嘗試了幾種其他的去噪方塊和原本的比較，$3\times 3$ mean filtering, $3\times 3$ median filtering, $3\times 3$ bilateral filtering (Eqn. (2)), and non-local filtering(原本的)。
並且還與加上其他的東西來比較例如說bottleneck或是沒有denoising operation 只有$1\times 1$的block
![](https://i.imgur.com/06JABHA.png)
下圖可以看到其他種類的去噪方塊都大於此三種(1)baseline (2)加上4個的bottleneck (3)加上四個沒有denoising operation 只有 $1\times 1$的block
最好的結果就是使用non-local mean filter +gaussian，但有趣的是很多參數的Gaussian version只比dot product 好了一點點。
![](https://i.imgur.com/ctL5nH8.png)


The Ablation with denoising block
觀察在這個裡面如果不要其他東西的效果
雖然這兩個東西並沒有去噪的功能，但是卻是不可或缺的一部分
可以看到若沒有$1\times 1$的最後一部分，那麼他的準確度會下降很多
若沒有殘差性的話，他的訓練效果會不穩定
原因 ： 抑制噪音也會抑制我們的特徵，因此有效地把我們的降噪後的特徵和輸入的特徵做正確的組合顯得更為重要。
![](https://i.imgur.com/4kb3lla.png)

### Black-Box attack : 
這裡使用5個2017CAAD攻擊者來使用，並且依照CAAD2018的標準，要全部都抵禦才算成功(全有或全無)
根據他們黑盒的設定最大的$\epsilon=32$，但這裡模型是用$\epsilon=16$來訓練的

下圖表示了在ImageNet上的黑盒攻擊的結果，CAAD2017的獲獎者只有0.04%, 刪除兩個後還是只有13.4%, 這裡我們得到的準確率是43.1%的baseline 以及 46.4%的標準4個去噪塊
其他均值，中值和雙邊濾波器也嘗試了一下，只有43.6%~44.4%的準確度，表示針對黑盒的攻擊，非本地的去噪比本地的去噪更為重要。
並且最後也對每個residual block後面都加上一層denoising block, 可以達到49.5%
![](https://i.imgur.com/NOEs1GQ.png)

### CAAD 2018 attack

“every defense entry needs to defend against 48 unknown attackers submitted to the same challenge”
![](https://i.imgur.com/gH2WpKS.png)

在此比賽(黑盒)得到50.6%的成功率，
在白盒10次PGD攻擊和100次PGD攻擊下，其準確率分別達到56.0％和40.4％


### Denoising Blocks in Non-Adversarial Settings
這裡要去觀察如果加上去噪塊，可是我們卻是在正常的資料下去做training 並且用正常的資料testing 那麼還會有差不多的準確率嗎？ 答案是差不多的
![](https://i.imgur.com/yOvTLnl.png)


但問題是在最後，我們報告說，在經過清晰訓練的圖像上進行測試後，經過對抗訓練的ResNet-152基準具有62.32％的準確性，而經過“清晰”訓練的對等基準則具有78.91％。
對於降噪版本（非本地，高斯），在乾淨圖像上，經過對抗訓練的網絡的準確性為65.30％，而經過乾淨訓練的對等網絡的準確性為79.08％。先前已經觀察到了對抗訓練和乾淨訓練之間的權衡（例如，在[D. Tsipras, S. Santurkar, L. Engstrom, A. Turner, and A. Madry. There is no free lunch in adversarial robustness (but there are unexpected benefits). arXiv:1805.12152, 2018.](https://arxiv.org/abs/1805.12152v5)中）；我們希望這種權衡成為未來研究的主題
## Conclusion 
我們發現特徵去噪的潛力，可以提高卷積網絡的對抗魯棒性
研究表明，某些架構設計（即降噪塊）對於對抗魯棒性特別有用，即使與“乾淨”訓練和測試場景中的基線模型相比，它們(一般模型)也無法提高準確性。 
當與對抗訓練相結合時，這些特定的架構設計可能更適合於建模對抗圖像的基礎分佈。
我們希望我們的工作將鼓勵研究人員開始設計具有“固有”對抗性魯棒性的捲積網絡架構。

1. The robustness potential for the feature denoising.
2. The Feature noise is big to observed in feature level.
3. The pixel denoising is insufficient, we need feature denoising.
4. When combined with adversarial training, these particular architecture designs may be more appropriate for modeling the underlying distribution of adversarial images.
