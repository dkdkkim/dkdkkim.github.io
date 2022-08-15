---

layout: page

title: Weakly supervised Branch Network with Template Mask for Classifying Masses in 3D Automated Breast Ultrasound

description: A novel branch network architecture incorporating segmentation information of masses in the training process

img: assets/img/mask_branch_network_00.png

importance: 1

category: work

---

git repository link: [Mask branch network repository](https://github.com/dkdkkim/maskbranchnetwork)

paper link : [Weakly supervised Branch Network with Template Mask for Classifying Masses in 3D Automated Breast Ultrasound,WACV2022](https://openaccess.thecvf.com/content/WACV2022/html/Kim_Weakly_Supervised_Branch_Network_With_Template_Mask_for_Classifying_Masses_WACV_2022_paper.html)


 * * *
### **Project Outline**

자동유방초음파영상(ABUS, Automated Breast Ultrasound)은 초음파영상의 특성상 artifact나 shadow등의 영향으로 상대적으로 영상의 quality가 떨어지고 전문가 사이에서도 consensus가 잘 되지 않는 domain이다. 그에따라 데이터 구축에도 어려움이 있어 충분한 데이터셋 구축에도 어려움이 있다.

이런 한계점들을 극복하기 위해서 본 연구에서는 ABUS내에서 병변을 구분하는 classification 모델을 효율적으로 training 하기위한 **Mask branch network**를 제안하며 이때 **Template mask** 를 활용한 Weakly-supervised sementation map을 동시에 제안한다.


<!-- <p align="center"><img src="../../assets/img/mask_branch_network_01.png" alt="mask branch network intro" style="zoom:67%;" /> -->
<div class="row justify-content-md-center">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/mask_branch_network_01.png" title="CAM" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
     Class activation maps (CAMs) for sample mass images.
</div>

* * *
### **Role in Project**

1. Modeling
2. Experiment & Validation
3. Wrting paper
 * * *

### **Language & Framework**

1. Language: Python
2. Framework: Pytorch
 * * *

### **Method**

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