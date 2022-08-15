---

layout: page

title: A Conservative Approach for Unbiased Learning on Unknown Biases

description: A novel unbiased learning method, UBNet, with hierarchical features and orthogonal regularization

img: assets/img/ulub_00.png

importance: 1

category: work

---

git repository link: [Mask branch network repository](https://github.com/dkdkkim/maskbranchnetwork)

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
- 기존의 Unbias 연구들은 bias에 대한 추가적인 정보(data 단위의 labeling 또는 feature 정보)를 요구한다. 본 연구에서는 추가적인 정보없이 모델이 편향되지 않도록 학습하는 모델을 제안한다.
- [ImageNet-trained CNNs are biased towards texture](https://arxiv.org/abs/1811.12231)에서 CNN은 특정 feature에 의존적으로 학습되고 있음을 보여주었다. 여기서 의존하지 않고 있는 다른 feature들을 사용하면 unbias 할 수 있겠다는 아이디어를 얻게 되었다.

<div>
    <center><img src="ulub_01.png" alt="mask branch network intro" width="60%" height="60%"></center>
</div>
<div class="caption">
     <center>Feature dependence of the CNNs</center>
</div>
- CNN 모델에서 layer depth에 따른 Degree of bias(DOB)를 측정해본 결과 high level에서 더욱 bias 된 것을 확인할 수 있었고, low level feature와 balance있게 사용한다면 모델의 편향성이 감소될 수도 있겠다는 결론에 도달하게 되었다.

<div>
    <center><img src="ulub_02.png" alt="mask branch network intro" width="60%" height="60%"></center>
</div>
<div class="caption">
     <center>Degree of Bias</center>
</div>
___

### **Method**
- Bias 되어있는 데이터에서 효과적으로 학습하기 위한 **UBNet(Unbiasing Network)**를 제안하며, 전체적인 구조는 아래와 같다.

<div>
    <center><img src="ulub_03.jpg" alt="mask branch network intro" width="70%" height="70%"></center>
</div>
<div class="caption">
     <center>The architecture of the proposed model, UBNet</center>
</div>

1. **Mask branch network**
- branch netwokr는 이전의 연구들에서 multi-task learning을 위한 방법으로 제안되었다. 본 연구에서 branch network의 역할은 spatial information을 main network에 integration 하는 데에 있다.
- mask branch network는 training step에서만 사용되며 inference 시에는 사용하지 않으므로 추가적인 computational cost 가 필요하지 않다.

<!-- <p align="center"><img src="../../assets/img/mask_branch_network_02.png" alt="main_network" style="zoom:40%;"></img> -->
<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/mask_branch_network_02.png" title="Architecture" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
     The architecture of mask branch network
</div>

1. **Template mask**
- Object(mass)의 segmenation map을 만드는 데에는 전문가의 작업이 필요하기 때문에 시간과 비용이 많이 필요하기 때문에 어려움이 있다. 이를 극복하기 위해서 Text(판독문)의 정보를 활용한 Template mask를 제안하였다.

<!-- <p align="center"><img src="../../assets/img/mask_branch_network_03.png" width="80%" height="30%" title="overview" alt="overview"></img> -->
<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/mask_branch_network_03.png" title="Template mask" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
     How to make template mask by utilizing characteristics of target lesions
</div>

 * * *

### **Experimental results**
1. **Qualitative result**
- Backbone network를 ResNet/DenseNet으로 세팅하여 평가하였고 두가지 방법 모두 제한된 조건내에서 성능향상을 확인할 수 있었다.

<!-- <p align="center"><img src="../../assets/img/mask_branch_network_04.png" alt="template_mask" width="80%" height="30%" title="overview"></img> -->
<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/mask_branch_network_04.png" title="ROC curves" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
     ROC curves of mask branch network(MBN) with DenseNet and ResNet
</div>

2. **Quantitative result**
- Traning된 모델을 CAM(Class Activation Map)으로 비교해보았을 때에도 제안된 모델에서 병변에 더 focusing 하고 있음을 확인할 수 있다.

<!-- <p align="center"><img src="../../assets/img/mask_branch_network_05.png" alt="template_mask" width="80%" height="30%" title="overview"></img> -->
<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/mask_branch_network_05.png" title="CAM2" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
      Class activation maps (CAMs) to show the effect of the mask branch network (MBN)
</div>

### **Conclusion**
Breast mass의 특성을 활용한 Template mask와 Spatial information을 효과적으로 feature extractor에 integration 하도록 하는 Mask branch network는 ABUS 영상의 병변을 분류하는 모델의 학습에 적용되어 성능향상을 확인할 수 있었다.