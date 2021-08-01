---
title: MagDR - Mask-guided Detection and Reconstruction for Defending Deepfakes
date: 2021-05-15 20:00:00
comments: true
author: Yi-Wei
categories:
- meeting
- read note
tags:
- Adversarial
- note
---

# MagDR: Mask-guided Detection and Reconstruction for Defending Deepfakes


###### tags: `adversarial`

[論文連結](https://arxiv.org/pdf/2103.14211.pdf)

其核心思想在於使用一些非監督性指標對對抗樣本在 Deepfake 中所生成的結果進行敏感性評估，並且利用人臉屬性區域作為輔助信息以及通過對最優防禦方法進行搜索組合的方式檢測和重建圖片，以期能夠達到淨化原圖並保持 Deepfake 輸出真實性的目的

## method
使用檢測器和重構器
使用檢測器去檢測是否有改造過，有的畫利用重構器去將他回復

# Experiment
該研究選取了Deepfake 中較為重要的三個任務進行攻防實驗，分別為換臉、人臉屬性修改以及表情變換

表1：不同攻擊方法和深層偽造的檢測性能比較。 請注意，所有這些擾動都是在沒有防禦的情況下產生的。檢測是否有被攻擊
![](https://i.imgur.com/Nt0S1Ry.png)

