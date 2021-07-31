---
title: Adversarial Robustness for Deep Learning with Trends and Challenges 
date: 2020-01-13 13:30:00
comments: true
categories:
- meeting

tags:
- Adversarial
- note
---

# Adversarial Robustness for Deep Learning with Trends and Challenges 
Pin-Yu Chen 2020 Jan 13rd
###### tags: `adversarial example` `robustness` `Deep learning`
```
This is a note for a speech in sinica
```

deepfake
what is the wrong with this model? Adversarial examples
Prediction -evasive attacks on an AI model
1. crisis in trust/black-box learning:inconsistent perception and decision making between humans and machines
2. Implications to security-critical tasks
3. Limitation in current machine
4. loss curve

Adverarial Robustness:
(1)Attack: 1stand 0th order(gradient-free) optimization
(2)Defense : Robust optimization
(3)Evaluation and Certification 
(4)Interpretability :Deep Learning 

Accuracy is not Adversarial Robustness
More accuracy model increasing get in trouble

Why we need? we need to trust AI
whenever a NN , there is a way to adversarial.

Adversarial examples: 
1.Image captioning 
2.speech recoginition
3.Data regression
4.phtsical world : real time traffic sign detector , 3D-printed adversarial turtle, Adversarial patch, Adversarial T-shirt

Holistic View of Adversarial Robustness
data -> Model ------------> Inference 

Evasion attack in Model and inferense
examples :+1: 
1. white box attack: standard white-box, adaptive white box(defense-aware) 已知模型
2. Black-box : 不知模型
3. Ｔransfer attack(black box)
4. Gray Box attack

Generate the box : Great BP
(1) Attack
Attack formulation:
Auto ZOOM(Query REdemptions)
minimize distance(看起來越像越好)
minimize{$x_0,x_o+\delta + \lambda * loss(x_0, \delta)$}
key technique : gradient estimation from system outputs

method : Zoo, ZOO+AE, AUTOZOO+BLI, AUTOZOO+AE

zeroth-order(ZO) optimization

Generating Contrastive Explanations
PP(pertinent positive):
PN(pertinent negative)
(2) defense (attack in some method : change pixels)
robustness evaluation : how close a refence input is to the closest decision boundary.(保證某個球內都是某一張圖的label)
對機器的adversarial examples

where we are and go 
1. Data augumentation with adversarial examples
2. standard training to robust training:minmax bad in DNN
3. input transformation, correction and anomaly detection: many are bypassed by advanced attacks
loss accurcy and give robustness
4. New learning model and training loss : slow progress
5. Model with diversity : model ensembles and model with randomness (complicated)
6. Domain and task-specific defenses : case-by-case, not automated
7. Combineation of all the effective methods : system design

Method of Our defense : 切割前半段，利用語音連續性去判斷
簡單的：可以在輸入的時候加入一定的誤差，能夠增加模型穩定性

SPROUT : self-preprogessing training with Dirichlet label smoothing 

How do we evalute adversarial robustness?
1. Game-based approach 
2. Verification-based approach 
CNN-cert

high-accuracy $\neq$ Robustness 


