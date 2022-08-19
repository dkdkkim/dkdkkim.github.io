---

layout: page

title: Nodule Type classification for Lung CT

description: Lung nodule type classification model for Lung CT 3D image

img: assets/img/type_cls_01.png

importance: 1

category: work

---

git repository link: [Nodule type classification](https://github.com/dkdkkim/nodule_type_classification)
 * * *
## Project Outline

Lung CT 영상내에서 Nodule의 악성도를 판별하는 기준중의 하나인 Nodule Type(solid / part-solid / non-solid) 를 분류하는 딥러닝 모델 개발하는 것을 목표로 한다.

<!-- <p align="center"><img src="../../assets/img/image-20220806025535400.png" alt="image-20220806025535400" style="zoom:67%;" /> -->
<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/nodule_type_01.png" title="lung nodule type" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    3 types of lung nodule
</div>

프로젝의 목표는 아래와 같다.

1. 정확도(Accuracy) 90% 이상
2. Class 중 하나인 Part solid의 경우에는 실제로 판독의 사이에서 consensus가 낮은 편이므로 Solid / Non-solid 클래서의 성능을 우선으로 한다


* * *
## Role in Project

1. 모델 구현 및 실험
2. 성능 측정 및 검증
 * * *

## Language & Framework

1. Language: Python
2. Framework: Tensorflow
 * * *

## Approach

1. 기존에 검증된 모델들을 기반으로 하여 3D classification model을 구현
2. Attention module을 구현 및 적용하여 성능을 향상시킴
3. 여러개의 모델을 Training한 후 Ensemble 기법을 활용하여 약간의 성능향상

   <!-- <p align="center"><img src="../../assets/img/image-20220806030139034.png" alt="image-20220806030139034" style="zoom: 40%;" /> -->

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/nodule_type_02.png" title="Overview" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Overview of lung nodule type classification model
</div>


 * * *

## Achievement
1. CNN base의 검증된 여러가지 모델로 training & validation 한 결과 성능이 가장 좋았던 DenseNet-121-BC 을 최종 모델로 선정하였다.
2. Squeeze & Excitation 모델을 DenseNet에 적용하여 성능 향상시켰다.
3. Hyperparameter tuning을 통하여 여러가지 모델을 생성하였고 그중 상위 모델 3개를 Ensemble 기법을 이용하여 합쳐서 최종 모델로 사용하였다.
4. 성능평가 후 CAD(Computer aided detection) system에 integration 하였다.

<style>
table {
    border-top: 1px solid #444444;
    border-collapse: collapse;
}
th, td {
    border-bottom: 1px solid #444444;
    padding: 10px;
}
</style>
<table width ="400" height="100" align = "center"><thead>
<tr>
<th></th>
<th align = "center">Solid</th>
<th align = "center">Part solid</th>
<th align = "center">Non solid</th>
</tr>
</thead><tbody>
<tr>
<td >Sensitivity</td>
<td>94.3%</td>
<td align = "center">79.2%</td>
<td align = "center">64.5%</td>
</tr>
<tr>
<td>Specificity</td>
<td>89.1%</td>
<td align = "center">93.8%</td>
<td align = "center">94.2%</td>
</tr>
<tr>
<td>Accuracy</td>
<td>92.5%</td>
<td align = "center">91.6%</td>
<td align = "center">88.4%</td>
</tr>
</tbody></table>