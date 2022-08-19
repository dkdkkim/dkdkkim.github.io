---

layout: page

title: A Conservative Approach for Unbiased Learning on Unknown Biases

description: A novel unbiased learning method, UBNet, with hierarchical features and orthogonal regularization

img: assets/img/ulub_00.png

importance: 1

category: work

---

git repository link: [Unbias Network repository](https://github.com/dkdkkim/ubnet)

paper link : [A Conservative Approach for Unbiased Learning on Unknown Biases
, CVPR2022](https://openaccess.thecvf.com/content/CVPR2022/html/Jeon_A_Conservative_Approach_for_Unbiased_Learning_on_Unknown_Biases_CVPR_2022_paper.html)



* * *
### **Role in Project**

1. Modeling
2. Experiment & Validation
3. Writing paper
 * * *

### **Language & Framework**

1. Language: Python
2. Framework: Pytorch
 * * *

### **Approach**
- Training dataset이 특정 분포에 대해서 biased 되어 있는 경우 Machine learning model은 편향될 가능성이 높다. 이런 경우에 model은 새로운 데이터에 대해서 편향된 상태로 예측을 하게 된다. 따라서 ML 또는 DL을 학습할 때에 Generalization 또는 Unbias는 중요한 요소라고 할 수 있다.
- 예를들의 사람 얼굴의 성별을 구분하는 모델에서 대부분의 여성은 머리길이가 길기 때문에 머리긴 남성의 얼굴에 대해서 여성으로 잘못 예측하는 방향으로 모델이 학습될 수도 있다.
- 기존의 Unbias 연구들은 bias에 대한 추가적인 정보(data 단위의 labeling 또는 feature 정보)를 요구한다. 본 연구에서는 추가적인 정보없이 모델이 편향되지 않도록 학습하는 모델을 제안한다.
- [ImageNet-trained CNNs are biased towards texture](https://arxiv.org/abs/1811.12231)에서 CNN은 특정 feature에 의존적으로 학습되고 있음을 보여주었다. 여기서 의존하지 않고 있는 다른 feature들을 사용하면 unbias 할 수 있겠다는 아이디어를 얻게 되었다.

<div>
    <center><img src="../../assets/img/ulub_01.png" alt="mask branch network intro" width="60%" height="60%"></center>
</div>
<div class="caption">
     <center>Feature dependence of the CNNs</center>
</div>
- CNN 모델에서 layer depth에 따른 Degree of bias(DOB)를 측정해본 결과 high level에서 더욱 bias 된 것을 확인할 수 있었고, low level feature와 balance있게 사용한다면 모델의 편향성이 감소될 수도 있겠다는 결론에 도달하게 되었다.

<div>
    <center><img src="../../assets/img/ulub_02.png" alt="mask branch network intro" width="60%" height="60%"></center>
</div>
<div class="caption">
     <center>Degree of Bias</center>
</div>
___

### **Method**
- Bias 되어있는 데이터에서 효과적으로 학습하기 위한 **UBNet(Unbiasing Network)** 를 제안하며, 전체적인 구조는 아래와 같다.

<div>
    <center><img src="../../assets/img/ulub_03.jpg" alt="mask branch network intro" width="70%" height="70%"></center>
</div>
<div class="caption">
     <center>The architecture of the proposed model, UBNet</center>
</div>

1. **Hierarchical Features**
- CNN 모델에서 High level feature 뿐만 아니라 Low level feature 까지 균형있게 활용할 수 있도록 하기 위해서 Base model(feature extractor)에서 다른 depth level의 feature들을 추출하였다.
- 각각 다른 level에서 추출된 feature들은 $f_{trans}$ 동일한 dimension으로 치환된다

2. **Orthogonal Regularization**
- feature들이 fusion될 경우 기존의 CNN모델과 마찬가지로 High feature에 의존할 가능성 있기 때문에, **Group convolution** 을 적용하여 개별적으로 연산되도록 한다.
- 서로 다른 group의 feature들의 독립성을 보장하기 위해서 **Orthogonality regularization** 을 적용하였다. 각각의 feature 사이의 orthogonality를 loss term에 추가하여 학습하면서 orthogonality가 높은 방향으로 학습되도록 유도하였다.



 * * *

### **Experimental results**
- 3가지 biased dataset에 대해서 성능을 평가하였다
1. **CelebA-HQ**
-  30K의 유명인사들의 얼굴사진으로 이루어진 데이터셋. 머리길이를 bias로 하는 gender prediction 에 대한 성능을 평가 하였다.

<div>
    <center><img src="../../assets/img/ulub_04.jpg" alt="mask branch network intro" width="60%" height="60%"></center>
</div>
<div class="caption">
     <center>Extreme bias sets on CelebA-HQ</center>
</div>

- bias 되어있는 EB2에서 성능이 향상된 것을 확인할 수 있고, bias 되지 않은 EB setting에서도 성능이 떨어지지 않았음을 확인할 수 있다.

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
<th align = "center">Method</th>
<th align = "center">Base Model</th>
<th align = "center">HEX</th>
<th align = "center">Rebias</th>
<th align = "center">LfF</th>
<th align = "center">UBNet</th>
</tr>
</thead><tbody>
<tr>
<td >Sensitivity</td>
<td align = "center">99.38%</td>
<td align = "center">92.50%</td>
<td align = "center">99.05%</td>
<td align = "center">93.25%</td>
<td align = "center">99.18%</td>
</tr>
<tr>
<td>Specificity</td>
<td align = "center">51.22%</td>
<td align = "center">50.85%</td>
<td align = "center">55.57%</td>
<td align = "center">56.70%</td>
<td align = "center">58.22%</td>
</tr>
<tr>
<td>Accuracy</td>
<td align = "center">75.30%</td>
<td align = "center">71.68%</td>
<td align = "center">77.31%</td>
<td align = "center">74.98%</td>
<td align = "center">78.70%</td>
</tr>
</tbody></table>
1. **UTKface**
- 20K의 얼굴 이미지 데이터셋으로 연령, 성별, 피부색이 분류되어 있다. 

<div>
    <center><img src="../../assets/img/ulub_05.jpg" alt="mask branch network intro" width="60%" height="60%"></center>
</div>
<div class="caption">
     <center>Extreme bias sets on UTKFace</center>
</div>

- Gender가 편향된 Skin tone 예측과 Skin tone이 편향된 Gender 예측에서 모두 성능이 향상됨을 확인할 수 있었다.

<div>
    <center><img src="../../assets/img/ulub_utkface_table.png" alt="mask branch network intro" width="60%" height="60%"></center>
</div>
<div class="caption">
     <center>Results on UTKFace</center>
</div>

3. **9-Class ImageNet**
- ImageNet 데이터셋의 일부를 9class로 분류한 데이터 셋으로 texture에 대한 정보가 sub-label로 라벨링 되어 있다.
<div>
    <center><img src="../../assets/img/ulub_06.jpg" alt="mask branch network intro" width="60%" height="60%"></center>
</div>
<div class="caption">
     <center>Extreme bias sets on UTKFace</center>
</div>
  
- Biased 와 Unbiased ACC 에서 모두 높은 성능을 보였다. Biased에서 Rebias와 동일한 성능을 보였으나 Unbiased에서 더 높은 성능을 보였다.

<table width ="400" height="100" align = "center"><thead>
<tr>
<th align = "center">Metric</th>
<th align = "center">Base mode</th>
<th align = "center">SI</th>
<th align = "center">LM</th>
<th align = "center">RUBi</th>
<th align = "center">Rebias</th>
<th align = "center">LfF</th>
<th align = "center">UBNet</th>
</tr>
</thead><tbody>
<tr>
<td align = "center">Biased</td>
<td align = "center">90.8%</td>
<td align = "center">88.4%</td>
<td align = "center">64.1%</td>
<td align = "center">90.5%</td>
<td align = "center">91.9%</td>
<td align = "center">89.0%</td>
<td align = "center">91.9%</td>
</tr>
<tr>
<td align = "center">Unbiased</td>
<td align = "center">88.8%</td>
<td align = "center">86.6%</td>
<td align = "center">62.7%</td>
<td align = "center">88.6%</td>
<td align = "center">90.5%%</td>
<td align = "center">88.2%</td>
<td align = "center">91.5%</td>
</tr>
</tbody>

### **Conclusion**
Training dataset 의 편향된 분포는 머신러닝 모델의 학습을 불안정하게 만든다. 본 연구에서 제안하는 UBNet은 추가적인 정보없이 모델의 hierarchical feature를 효과적으로 활용하여 편향된 Trainig set에서도 더욱 안정적인 training을 가능하게 하였다. 3가지 편향된 데이터 셋에서 실험을 통하여 그 효과를 입증하였다.