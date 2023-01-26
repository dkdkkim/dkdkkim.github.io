---
layout: post
title: Vision Transformer Training on Small Datasets
date:   2022-10-21 00:00:00
description: Proposed method serves as an effective weights initialization to successfully train ViTs from scratch, thus eliminating the need for large-scale pre-training.
tags: review
categories: paper-review
---

앞서 이야기했듯이 Image recognition의 여러가지 분야에서 Vision Transformer는 많은 성과를 이루어냈다. 하지만 여전히 가지고 있는 한계점은 large-scale의 데이터셋에서만 효과적이라는 점이다. small-scale의 데이터밖에 없는 분야에는 이런 문제로 대부분의 ViT 모델을 적용하는데 어려움을 겪고 있다. 이런 문제를 해결하고자 하는 접근중의 하나가 이번에 리뷰하는 논문이다.

---

## **Problem**
ViT가 small-scale dataset을 학습하는 데에 어려움을 겪고 있는 이유는 ViT 알고리즘 특성상 locality, inductive bias, hierarchical sturucture 가 부족하기 떄문이다. 비슷한 이유로 ViT는 transfer learning을 하기 위해서 large-scale dataset에 pretraining을 필요로 한다. 따라서 결과적으로 large-scale dataset이 없는 경우에는 ViT의 성능은 제한된다.

---

## **Approach**
본 논문에서는 small dataset에서 low-resolution view를 활용해서 sef-supervised learning을 하는 방법을 제안한다. 이 방법은 large dataset에서의 pretraining 없이 ViT를 효과적으로 학습할 수 있게 하는 효과적은 weight initialization을 제공한다. 이때 self-supervised learning 하는 방식은 [DINO](https://arxiv.org/pdf/2104.14294.pdf)에서 제안한 방식과 아주 유사한 것을 확인할 수 있다. 따라서 DINO를 이해하고 있다면 Method는 무리없이 이해할 수 있을 것이다.
<div>
    <center><img src="../../../assets/img/small01.gif" alt="rego_approach" width="60%" height="60%"></center>
</div>
<div class="caption">
     <center>Concept of REcurrent glimpse-based decoder (REGO)</center>
</div>

---

## **Method**
REGO model의 전체적인 workflow는 DETR의 출력물인 $$ O_{box} $$와 feature $$ H_{dec} $$을 입력으로 받아 $$N$$개의 step의 반복을 통해서 최종적인 prediction을 하게 되며, 각각의 step은 Glimpse-based Decoder로 구성되어있다.

<div>
    <center><img src="../../../assets/img/rego02.png" alt="rego_method" width="70%" height="70%"></center>
</div>
<div class="caption">
     <center>The overview of the REGO</center>
</div>

### Glimpse-based Decoder

DETR의 output 또는 이전 block의 출력값중 $$O_{box}(i-1)$$을 이용하여 해당영역에서 feature를 추출하게 된다. 이때에 box 값을 그대로 사용하는 것이 아니라 margin을 갖고 조금더 넓은 영역에서 feature를 추출하는데 Diagram에서 Enlarge라고 표시된 부분이다. 이때 enlarge 하는 비율을 나타내는 glimpse scale $$\alpha$$ 는 점점 감소하게 되는데 3step일 경우를 예로 들면 $$\alpha=3,2,1$$ 이된다.

<div>
    <center><img src="../../../assets/img/rego03.png" alt="rego_method" width="30%" height="30%"></center>
</div>
<div class="caption">
     <center>Enlarge step of Glimpse-based Decoder</center>
</div>

이렇게 해당 RoI에서 추출된 feature $$V(i)$$는 query 값이 되고 이전 block으로 부터의 feature $$Hec(i-1)$$ 은 Linear Projection으로 dimension 조정 후 key, value 값이 되어 Multi-head cross attention 후 $$H_{g}(i)$$ 가 된다. $$H_{g}(i)$$ 와 $$Hec(i-1)$$를 concatenation을 통해 하나의 feature로 만들고 MLP를 통해서 새로운 Detected Boxes $$O_{box}(i)$$, Classification Output $$O_{cls}(i)$$ 그리고 Decoding Output $$H_{dec}(i)$$ 를 출력하게 된다.

### Multi-stage Reccurent Processing
이런 glimpes-based Decoder를 $N$번 반복하여 ReGO model 이 완성되며 중간과정에서는 box와 feature 만 사용되지만 마지막 블럭에서는 classification 값까지 최종 예측값으로 사용된다. 본논문에서 일반적으로 step의 수 $N$은 3을 사용하였으며, 이는 실험적으로 선택된 것으로 보인다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/rego04.png" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/rego05.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
     Hyper-parameter study of the number of stages in REGO and  the glimpse scale in REGO
</div>

---

## **Experimental result**
MS COCO validation split에서 35, 50 epoch에서의 결과를 아래와 같이 여러가지 existing Object Detection 모델들과 비교했으며, 관심을 가지고 보아야할 부분은 일반적인 DETR과 앞서 Training 문제를 개선한 [Deformable DETR](https://arxiv.org/abs/2010.04159) 과의 비교이다.

<div>
    <center><img src="../../../assets/img/rego06.png" alt="rego_method" width="60%" height="60%"></center>
</div>
<div class="caption">
     <center>Results of different detectors on the MS COCO val split</center>
</div>

표에서 보면 알 수 있듯이 DETR 은 물론이도 Deformable DETR 보다도 동일 epoch을 training 했을 때 높은 성능을 보임을 알 수 있다. 심지어 동일한 ResNet 50 backbone 기준으로 보았을 때에 REGO로 36 epoch을 training 했을 때 성능(44.8)이 Deformable DETR 의 성능(43.8) 보다 높은 것까지도 확인할 수 있다.

---

## **Limitations**
본 논문에서 저자도 언급했지만, REGO를 통해서 DETR의 학습 효율은 많이 개선되었지만 여전히 training 하는데에 수 일이 소요되기 때문에 앞으로도 개선의 여지는 남아있다고 볼 수 있다.
그리고 개인적으로는 본 논문에서 성능 검증에 사용된 지표는 validation split의 결과여서 test split에서의 성능의 검증이 필요하고, 50 epoch이상 training 하여 최종적으로 optimized 된 성능의 비교도 확인할 필요가 있다고 생각한다.

---

## Reference

- [Chen, Zhe, Jing Zhang, and Dacheng Tao. "Recurrent glimpse-based decoder for detection with transformer." Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition. 2022.](https://openaccess.thecvf.com/content/CVPR2022/html/Chen_Recurrent_Glimpse-Based_Decoder_for_Detection_With_Transformer_CVPR_2022_paper.html)
- [Carion, Nicolas, et al. "End-to-end object detection with transformers." European conference on computer vision. Springer, Cham, 2020.](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwjMmPaL3tz5AhVIQt4KHf0tBMUQFnoECAkQAQ&url=https%3A%2F%2Farxiv.org%2Fabs%2F2005.12872&usg=AOvVaw1fNz92-EEj1d91Nfiv875y)
- [Zhu, Xizhou, et al. "Deformable detr: Deformable transformers for end-to-end object detection." arXiv preprint arXiv:2010.04159 (2020).](https://arxiv.org/abs/2010.04159)