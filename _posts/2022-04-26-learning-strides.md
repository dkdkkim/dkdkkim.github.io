---
layout: post
title: LEARNING STRIDES IN CONVOLUTIONAL NEURAL NETWORKS
date:   2022-04-26 00:00:00
description: DiffStride, the first downsampling layer with learnable strides.
tags: review
categories: paper-review
---

Model의 architecure를 결정하는 여러가지 hyperparameter들이 이전에는 search space등의 방법으로 최선의 조합을 찾았다면, 최근에는 AutoML 방식으로 hyperparameter들도 알고리즘을 통해 자동으로 최적화하는 트렌드이다. 하지만 일반적으로 AutoML은 큰 Resource를 요구하기 때문에 실질적으로 사용하지 못하는 개발자들이 많다. 이번에는 CNN 모델에서 stride를 learnable하게 하는 연구에 대한 논문인 'LEARNING STRIDES IN CONVOLUTIONAL NEURAL NETWORKS'을 리뷰하고자 한다. 본 연구는 ICLR 2022에 oral paper로 선정되었으며, stride만을 leanable 하게 하기 때문에 지나치게 많은 resource를 요구하지도 않는다.

---

## **Problem**
CNN base 모델에서 downsampling layer를 디자인 함에 있어서 stride는 flexable하면서 매주 중요한 요소이다. 하지만 최선의 조합을 찾기 위해서는 cross-validation이나 archtecuture search 방식을 사용해야 한다. CNN model의 hyperparameter는 다양하기 때문에 stride까지 hyperparameter로 탐색하게 된다면 search space는 매우 커지게된다. 이는 downsampling layer의 개수가 많아질수록 기하급수적으로 문제가 커진다.

<div>
    <center><img src="../../../stride01.png" alt="diffstride" width="60%" height="60%"></center>
</div>
<div class="caption">
     <center>Hyperparametes of CNN model</center>
</div>
---

## **Contribution**
본 논문에서는 처음으로 제안하는 learnable stride 방법론을 제안하며 이를 'DiffStride'라고 명명하였다. 실제로 audio와 image classification task에 적용하였고 실험적으로 그 효과성을 입증하였다.
<div>
    <center><img src="../../../stride02.png" alt="diffstride" width="50%" height="60%"></center>
</div>
<div class="caption">
     <center>Comparison of resiudal block with and without DiffStride</center>
</div>

---

## **Method**

### Spectral pooling

DiffStride를 소개하기에 앞서 먼저 Spectral pooling 에 대한 이해가 필요하다. Spectral은 frequency domain에서 power spectrum과 noise간의 특성을 이용해 pooling을 하는 방식을 말한다. 첫번째 단계로 Image $$y$$를 Discrete Fourier Transform(DFT)를 이용하여 frequency domain의 $$\hat y$$으로 변환한다.  그리고 주파수가 낮은 영역을 중심으로 crop한 후 다시 Inverse DFT를 통해서 spatiotemporal signal $$\hat x$$ 로 변환한다. 그러면 natural image의 power 기댓값이 통계적으로 저주파 영역에 대부분 밀집되어 있기 때문에 정보량 손실을 최소화하면서 영상의 크기를 감소시킬 수 있다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/stride03.png" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/stride04.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
     Examples and algorithm of spectral pooling
</div>

### DiffStride
DiffStride의 workflow는 아래의 figure와 같다. 

<div>
    <center><img src="../../../stride05.png" alt="diffstride" width="70%" height="60%"></center>
</div>
<div class="caption">
     <center>DiffStride forward and backward pass</center>
</div>

먼저 이미지 또는 feature map $$x$$ 는 Fourier Transform $$\it F$$ 에 의해 $$y$$ 로 변환된다. 그리고 $$y$$ 는 Stride parameter $$S$$로 부터 만들어진 $$mask$$ 와 Elementwise product를 통해서 저주파 영역 값만 남게 된다. 남은 영역만 crop 한 $$y_{cropped}$$ 를 다시 Inverse Fourier Transform 하여 $$\hat x$$ 가 되며 이것이 최종 출력물이 된다. 여기까지가 forward flow 였고 backward flow를 보면 빨간색 점선으로 표시되어 있는 순서로 진행된다고 보면 된다. CNN을 학습하기 위한 flow는 기존과 마찬가지로 input image $$x$$까지 흘러가고, stride parameter $$S$$를 학습하기 위한 backward flow는 $$Crop$$ 에서는 disconnect 되어 있으므로 element-wise product에서 분기하여 gradient가 계산된다.  

<div>
    <center><img src="../../../stride06.png" alt="diffstride" width="60%" height="60%"></center>
</div>
<div class="caption">
     <center>Algorithm of DiffStride</center>
</div>


---

## **Experimental result**
논문에서는 image와 audio classification에서 실험을 진행했지만, image classification 에 대한 내용만 정리했다. Backbone은 resnet18로 setting 하였으며 CIFAR 10, CIFAR 100, ImageNet 에 대해서 training 및 evaluation을 진행하였다.

Diffstride의 성능은 Vanilla resnet, Spectral pooling이 적용된 resnet과 initial stride 값에 따라 비교 하였다. 아래 표에서 보면 알 수 있듯이 정보 손실량이 적은 spectral pooling의 경우 일반적인 resnet보다 높은 성능을 보였으며, 대부분의 inital stride에 대해서 DiffStride가 적용된 경우가 Spectral pooling이 적용된 경우보다 더 높은 성능을 보이는 것을 알 수 있다.

<div>
    <center><img src="../../../stride07.png" alt="diffstride" width="60%" height="60%"></center>
</div>
<div class="caption">
     <center>Accuracies on CIFAR10 and CIFAR100</center>
</div>

<div>
    <center><img src="../../../stride08.png" alt="diffstride" width="60%" height="60%"></center>
</div>
<div class="caption">
     <center> Top-1 and top-5 accuracies (% ± sd over 3 runs) on Imagenet</center>
</div>

Learnable Stride의 training 경향은 아래 그래프 (a) 에서 처럼 epoch 이 진행됨에 따라 특정 값으로 수렴하는 것을 알 수 있다. 그래프 (b)를 보면 layer의 depth에 따른 Stride의 수렴 값을 알 수 있는데 깊은 level의 layer 일수록 stride의 수렴값이 커지는 것을 알 수 있다. 그리고 그래프 (c) 에서는 width의 stride 값이 height의 stride 값보다 더 큰 값으로 수렴하는 것을 알 수 있다.

---

## **Limitations**
Spectral pooling의 경우 정보손실량을 줄인다는 점에서 이점이 있지만 그 과정에서 DFT와 Inverse-DFT의 연산과정이 추가되므로 Computational cost 가 증가한다는 한계점이 존재한다. DiffStride는 이에 더해서 Stride parameter를 learnable 하게 하는데에 더 많은 연산량을 요구하기 때문에, Peak Memory 관점에서는 최대 2배 가까운 메모리를 요구할 수도 있다. 이는 저자도 논문에서 언급한 바이다.

<div>
    <center><img src="../../../stride09.png" alt="diffstride" width="60%" height="60%"></center>
</div>
<div class="caption">
     <center>Per-step time and peak memory usage of Spectral Pooling and DiffStride</center>
</div>

그리고 실험결과를 보면 일부 stride의 initial의 경우에는 DiffStride의 성능이 더 떨어지는 경우가 발생하는데 이는 layer depth가 더 많아지고 initial stride 의 경우의 수가 많아질때에는 intial stride 자체가 새로운 hyperparameter 가 되는 문제가 발생할 수도 있다고 개인적으로 생각한다.

---

## Reference

- [Riad, Rachid, et al. "Learning strides in convolutional neural networks." arXiv preprint arXiv:2202.01653 (2022).](https://arxiv.org/abs/2202.01653)
- [Rippel, Oren, Jasper Snoek, and Ryan P. Adams. "Spectral representations for convolutional neural networks." Advances in neural information processing systems 28 (2015).](https://proceedings.neurips.cc/paper/2015/hash/536a76f94cf7535158f66cfbd4b113b6-Abstract.html)