---
title: DEFENSE-GAN - PROTECTING CLASSIFIERS AGAINST ADVERSARIAL ATTACKS USING GENERATIVE MODELS (ICLR 2018)
date: 2020-08-12 14:30:00
comments: true
author: Yi-Wei
categories:
- meeting
- read note
tags:
- Adversarial
- note
---

# DEFENSE-GAN: PROTECTING CLASSIFIERS AGAINST ADVERSARIAL ATTACKS USING GENERATIVE MODELS (ICLR 2018)
###### tags: `adversarial`
The new framework leveraging the expressive capability of generative models to defend deep neural networks against such attacks.
It can be used with any classification model and does not modify the classifier structure or training procedure.
https://github.com/kabkabm/defensegan.

## Introduction 
Propose a novel defense mechanism which is effective against both white-box and black-box attacks.
In GaN framework, generative model trained simultaneously in an adversarial setting, discriminator model predicts whether a certain input is real or fake. And we classify the image which we generated in which class.

## Related work and background information

White-box model :
- Fast Gradient Sign Method FGSM(Goodfellow et al., 2015) : Give image x and true label y and the perturbation $\delta = \epsilon*sign(\nabla_x J(x,y))$
- Randomized Fast Gradient Sign Method(RAND+FGSM)(Tramer et al., 2017), the increase of FGSM
    - First, apply a small random perturbation before using FGSM. For $\alpha < \epsilon$, $x' = x +\alpha*sign(N(0^n,1^n))$
    - Then computed on $x'$, $\tilde x=x'+(\epsilon-\alpha)*sign(\nabla_{x'}J(x',y))$
- Carlini-Wagner (CW) attack (Carlini & Wagner,2017), the most powerful algorithm : ![](https://i.imgur.com/STq5VQg.png)
- others : Iterative FGSM (Kurakin et al., 2017), Jacobian-based Saliency Map Attack (JSMA) (Papernot et al., 2016b), Deepfool (Moosavi-Dezfooli et al., 2016)

black-box model : 
- untargeted FGSM attacks computed on a substitute model.

Defense mechanisms : 
1. Adversarial training : popular approach to defend the adversarial noise is to augment the training dataset with adversarial examples. 
2. Defense distillation : Trains the classifier in two rounds using a variant of the distillation method.
3. MAGNET : Trains a reformer network with auto-encoder or a collection of auto-encoder. Use GAN instead of auto-encoders and use GD minimization to find the latent codes.

## Defense-GAN
We use W-GAN trained on legitimate(un-pertubed) training samples to denoise adverserial examples. 
In test time, feeding an image x to the classifier, we project it onto the range of the generator by minimizing the reconstruction error $||G(z)-x||^2_2$ by using L steps of GD.

In inference time, Give generator G and image x to be classified, $z^*$ is to minimize$\min_z||G(z)-x||^2_2$. $G(z^*)$ is to given to the classifier.


Test time : ![](https://i.imgur.com/37oa3MH.png)
Inference time with L steps: ![](https://i.imgur.com/y0yCKOV.png)
And the classifier wit two types:
1. training on the original image
2. training on the reconstruction image

Compared by the defense mechanisms, there are four differents:
1. Defense-GAN can be used in conjuction with any classifier and doesn't modify the classify structure.
2. Re-training the classifier should not be necessary.
3. Defense any attack.
4. White box attack will be difficult due to GD loop

## Experiment
Three attack threat levels:
1. Black-box attacks : trains a substitute network to find adversarial examples.
2. White-box attacks : Know all the details of the classifier and defense strategy.(Compute the gradients)
3. White-box attacks,revisited : In addition to the above, attackers know the random seed and random number generator.


Compared with the method to adversarial training and MAGNET under the FGSM, RAND+FGSM, and CW (with $l_2$ norm) white-box attacks, as well as the FGSM black-box attack.
And two kinds of classifier, one is training used for reconstructed images, one is original images.
Datasets : MNIST, F-MNIST. (both with 60,000 training images and 10,000 testing images.)
### Black box attack

In tables 1, dataset is MNIST. The black box attacks are successful at reducing the classifier accuracy by up to 70%. 
The defense accuracy is more than MAGNET and Adversarial training.

![](https://i.imgur.com/zmXTR9U.png)

In table 2, dataset is F-MNIST. The large $\epsilon = 0.3$ represents a high noise, which make the images difficult to classify, even by a human.

![](https://i.imgur.com/1ZgvZyi.png)

In Table 3, the more epsilon big than the accuracy drop.
Although this is the strategy for the attacker, but more higher $\epsilon$ is more easily to detected.
![](https://i.imgur.com/5LyGQi1.png)

In figure 4, for different setting in R(the number of the choosing range), L(the iteration step), and the epsilon in fixed R and L.

For more L, more R and more epsilon the AUC curve is more bigger.
![](https://i.imgur.com/Y2s4bNV.png)

### White box attack
FGSM, RAND+FGSM, CW.
CW attack for 100 iterations of projected GD, with learning rate 10.0, and
use c = 100.
![](https://i.imgur.com/BWjmSZO.png)
For the dataset CelebA, classify the gender.
![](https://i.imgur.com/VEnYh7t.png)






## Question : 
