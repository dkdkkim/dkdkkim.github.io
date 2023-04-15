---
layout: post
title: How to Train Vision Transformer Training on Small Datasets
date:   2022-10-21 00:00:00
description: Proposed method serves as an effective weights initialization to successfully train ViTs from scratch, thus eliminating the need for large-scale pre-training.
tags: review
categories: paper-review
---

앞서 이야기했듯이 Image recognition의 여러가지 분야에서 Vision Transformer는 많은 성과를 이루어냈다. 하지만 여전히 가지고 있는 한계점은 large-scale의 데이터셋에 한정적으로 효과가 있다는 점이다. 따라서 large-scale의 데이터를 확보하기 힘든 분야에서는 이런 문제로 다양한 ViT 모델이 존재함에도 불구하 적용하는데 어려움을 겪고 있다. 이번에 리뷰하는 논문에서는 이런 단점을 보완하고자 한가지 방법을 제안한다.

---

## **Problem**
ViT가 small-scale dataset을 학습하는 데에 어려움을 겪고 있는 이유는 ViT 알고리즘 특성상 locality, inductive bias, hierarchical sturucture 가 부족하기 때문이다. 비슷한 이유로 ViT는 transfer learning을 하기 위해서 large-scale dataset에 pretraining을 필요로 한다. 결과적으로 large-scale dataset이 없는 경우에는 ViT의 성능은 제한된다.

---

## **Approach**
본 논문에서는 small dataset에서 low-resolution view를 활용해서 sef-supervised learning을 하는 방법을 제안한다. 이 방법은 large dataset에서의 pretraining 없이 ViT를 효과적으로 학습할 수 있게 하는 효과적인 initial weights를 제공한다. 이때 self-supervised learning 하는 방식은 [DINO](https://arxiv.org/pdf/2104.14294.pdf)에서 제안한 방식과 아주 유사한 것을 확인할 수 있다. 따라서 DINO를 먼저 숙지한다면 Proposed method는 쉽게 이해할 수 있다.

<div>
    <center><img src="../../../assets/img/small01.gif" alt="small_dino" width="60%" height="60%"></center>
</div>
<div class="caption">
     <center>Concept of DINO</center>
</div>

---

## **Method**
앞서 말했듯이 본 논문의 핵심은 Self-supervised learning을 통해 효과적인 initial weights를 찾는 것이다. 따라서 제안하는 방법은 크게 Self-supervised learning 하는 과정과 이때의 weight 값을 활용하여 supervised learning 하는 두가지로 나누어 볼 수 있다.

### Self-supervised View Prediction as Weight Initialization Scheme
Self-supervised learning 과정을 보면 아래와 같다. DINO와 마찬가지로 동일한 두개의 모델로 구성되어 있으며 각각 student model과 teacher model 이라고 한다.

<div>
    <center><img src="../../../assets/img/small02.png" alt="small_self" width="50%" height="50%"></center>
</div>
<div class="caption">
     <center>Self-supervised View Prediction as Weight Initialization Scheme</center>
</div>

학습을 위해 Input image 에서 일종의 augmentation을 통해 local view와 global view 두가지의 image를 생성한다. 두가지의 차이점은 local view $$ x_l $$는 원본이미지의 20~50%의 크기의 crop 된 이미지이고 global view $$ x_g $$는 원본이미지의 50%크기 이상인 이미지이라는 점이다. $$ x_l $$과 $$x_g$$의 크기의 비율은 1:4가 되도록 한다. 모든 이미지는 color jittering, gray scaling, solarization, random hrizontal flip and gausian blur를 포함한 standar augmentation을 적용한다.

Student model $$ F_s $$에는 모든 image가 DPE(Dynamic Position Embedding)를 통해 position embeding 처리가 된 후 입력된다. 반면에 Teacher model $$ F_g $$에는 global view 만 입력되게 된다. 각각의 모델은 모두 ViT이고 ViT 뒤에 MLP head를 통과 하게 된다. MLP head는 3개의 linear layer로 구성되어 있고, single layer 보다 multi layer가 더 좋은 성능을 보인다고 한다.

입력된 이미지와 사용된 모델에 따라서 각각 $$F_s(x_l)$$, $$F_s(x_l)$$, $$F_t(x_g)$$ 이 생성된다. 예를 들면, $$F_s(x_l)$$ 의 경우는 local view 가 student model을 통해 생성된 출력값이다. student model은 이 출력값들을 활용하여 아래의 cost function을 통해서 update 된다.

$$
\mathfrak{L}=-\widetilde{F}_{g_t}\cdot \log(\widetilde{F}_{g_s})+\sum_{i=1}^{n}-\widetilde{F}_{g_t}\cdot \log(\widetilde{F}^{(i)}_{l_s})
$$

teacher model $$F_t$$는  일반적인 teacher-student 학습처럼 아래의 수식대로 두모델의 weight $${\theta}_{s}$$, $${\theta}_{t}$$ 의 exponential moving average를 이용하여 업데이트 된다.

$$
{\theta}_{t} \leftarrow \lambda{\theta}_{t}+(1-\lambda{\theta}_{s})
$$

### Self-supervised to Supervised Label Prediction
앞서 Self-supervised learning을 통해서 획득한 weight 값을 활용하여 supervised learningdmf 진행하게 된다. 두가지 모델중 teacher 모델의 weight를 초기값으로 하여 목표가 되는 supervised learning을 진행하며 이 과정은 일반적인 ViT의 학습과정과 유사하게 CE loss로 학습을 진행한다.

<div>
    <center><img src="../../../assets/img/small03.png" alt="small_supervised" width="50%" height="50%"></center>
</div>
<div class="caption">
     <center>Self-supervised to Supervised Label Prediction</center>
</div>

---

## **Experimental result**
CIFAR나 Tiny-ImageNet 과 같은 small-scale dataset은 32 또는 64 와 같이 낮은 해상도를 가지고 있다. 따라서 실험에 사용되는 patch size와 이에 따른 모델의 구조를 아래와 같이 수정하였다고 한다.

<div>
    <center><img src="../../../assets/img/small04.png" alt="small_exp_set" width="60%" height="60%"></center>
</div>
<div class="caption">
     <center>Details of
ViT encoders used in
our proposed training
approach</center>
</div>

실험에는 아래와 같이 7개의 small-scale dataset이 사용되었다.

<div>
    <center><img src="../../../assets/img/small05.png" alt="small_datasets" width="60%" height="60%"></center>
</div>
<div class="caption">
     <center>Details of datasets</center>
</div>

실험결과는 아래와 같이 대부분의 실험에 사용된 데이터셋에 대해서 다른 모델보다 높은 성능을 보이는 것을 알 수 있다. Comparison model로는 Dense relative localization task를 활용한 [ViT-Drloc](https://arxiv.org/pdf/2106.03746.pdf)과 Shifted Patch Tokenization과 Locality Self-Attention을 활용한 [SL-ViT](https://arxiv.org/pdf/2112.13492.pdf)가 있다. 자세한 내용은 원문을 참고하면 좋을 것 같다.


<div>
    <center><img src="../../../assets/img/small06.png" alt="small_exp" width="60%" height="60%"></center>
</div>
<div class="caption">
     <center>Comparison Performance</center>
</div>

특히 salient object에 대해서 높은 성능을 보이는데 feature map을 시각화한 아래 figure를 보면 본 연구에서 제안한 모델에서 주변부대비 salient object가 더 잘 활성화 되어 있는 것을 확인할 수 있다.

<div>
    <center><img src="../../../assets/img/small07.png" alt="small_salient" width="60%" height="60%"></center>
</div>
<div class="caption">
     <center>Comparison with salient objects</center>
</div>

더 적은 데이터를 활용한 실험결과도 확인할 수 있는데 CIFAR10, CIFAR100의 25%,50%,75%의 데이터로 학습한 결과를 비교해보아도 더 좋은 성능을 보인 것을 확인할 수 있다.

<div>
    <center><img src="../../../assets/img/small08.png" alt="small_part" width="60%" height="60%"></center>
</div>
<div class="caption">
     <center>Training with part of small dataset</center>
</div>

---

## **Comments**
ViT가 처음으로 제안된지 오랜 시간이 지났음에도 불구하고 지금까지도 이에 대한 다양한 연구가 진행되고 있다. 다양한 image recognition task들에서 SOTA model의 자리는 ViT가 차지 한지 오래되었지만, 여전히 해결되지 않고 있는 몇가지 한계점도 있었다. 그 중 하나가 small dataset에 대한 문제였고 앞서 리뷰한 논문뿐만 아니라 여러가지 연구에서 해결하기 위해 다양한 방법을 제안하고 있다. 개인적인 생각으로는 아직도 완전히 해결된 것 처럼보이지는 않지만, 그 단점이 시간이 지나고 연구가 진행됨에 따라서 조금씩 보완되고 있기 때문에 또 어떤 혁신적인 알고리즘이 나오기 전까지는 앞으로도 한동안은 ViT가 대세일 것으로 보인다.

---

## Reference
- [Caron, Mathilde, et al. "Emerging properties in self-supervised vision transformers." Proceedings of the IEEE/CVF international conference on computer vision. 2021.](https://arxiv.org/pdf/2104.14294.pdf)
- [Gani, Hanan, Muzammal Naseer, and Mohammad Yaqub. "How to Train Vision Transformer on Small-scale Datasets?." arXiv preprint arXiv:2210.07240 (2022).](https://arxiv.org/pdf/2210.07240.pdf)
- [Lee, Seung Hoon, Seunghyun Lee, and Byung Cheol Song. "Vision transformer for small-size datasets." arXiv preprint arXiv:2112.13492 (2021).](https://arxiv.org/pdf/2112.13492.pdf)
- [Liu, Yahui, et al. "Efficient training of visual transformers with small datasets." Advances in Neural Information Processing Systems 34 (2021): 23818-23830.](https://arxiv.org/pdf/2106.03746.pdf)