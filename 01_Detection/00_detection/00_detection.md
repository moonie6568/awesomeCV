# a review of object detection

## 基本概念

1. **Intersection over union/IoU**

   IoU ≥ Ω，threshold一般设为0.5.

   对bounding box的evaluation,$b_{gt}$ refers to the ground truth or mask。

   ![1582728380429](C:\Users\moonie\AppData\Roaming\Typora\typora-user-images\1582728380429.png)

2. **positive and negative**

![1582728272037](C:\Users\moonie\AppData\Roaming\Typora\typora-user-images\1582728272037.png)

3.  **loss function**

   ![1582728574018](C:\Users\moonie\AppData\Roaming\Typora\typora-user-images\1582728574018.png)

sigmoid loss、softmax loss、focal loss

4. **Major milestone in object detection research**

![1582728811429](C:\Users\moonie\AppData\Roaming\Typora\typora-user-images\1582728811429.png)

![1582728824382](C:\Users\moonie\AppData\Roaming\Typora\typora-user-images\1582728824382.png)

目标检测可以认为是一个多region分类问题

5. **浅谈评价指标**

- mAP

- inference speed :images/FPS

  (e.g  a detector can achieve an inference speed of 20FPS is a real-time detector )

6. **the receptive field(感受野)**

特征图里的一个像素pixel和前一特征图之间相对应的区域就叫感受野

pooling是为了扩大感受野减小计算花费

## different components of object detection

### **two settings in object detection**

- bbox-level localization(detection)

- pixel-level or mask-level localization(segmentation)

  分割对空间信息的处理有更高的要求，但是有些方法两种setting都可以实现

### **两种检测范式**

- two-stage detectors(more accuracy,more time)

  first stage:提取一系列的预选框（proposals）

  second stage:对proposals进行特征提取（DNN），并进行分类预测

- one-stage detectors(less accuracy,less time)

  直接对所有position的region of interest进行分类预测

### **详解two-stage**

1. #### R-CNN

![1582730159360](C:\Users\moonie\AppData\Roaming\Typora\typora-user-images\1582730159360.png)

在训练前，RCNN已经reject了大量的easy positive,从而减少了FP的产生

缺点是time-consuming,三个部分（生成框、特征提取、分类预测）花费大量时间，并且不是end-to-end过程，优化也不是全局化的，不能充分利用GPU的加速

2. #### SPPnet

创新点：spatial pyramid pooling

![1582730796653](C:\Users\moonie\AppData\Roaming\Typora\typora-user-images\1582730796653.png)

3. #### Fast R-CNN

创新点：解决了SPP-Net的缺点，用ROI Pooling layer to extract region features替代SPP，

4. #### Faster R-CNN

创新点：Region Proposal Network(RPN)，实现了数据驱动的RP选取

深度网络的特征具有语义信息强，但空间信息弱的缺点，所以最后用一个特征层来进行预测，使得准确性较弱

5. #### R-FCN（Region-based Fully Convolutional Networks）

创新点：Position Sensitive ROI Pooling layer (PSROI Pooling)![1582731425904](C:\Users\moonie\AppData\Roaming\Typora\typora-user-images\1582731425904.png)

被提取的特征向量具有更丰富的空间位置信息

6. #### FPN（Feature Pyramid Networks）

创新点：特征金字塔，将低层和高层的特征进行融合，对于多尺度的物体检测效果好，解决了Faster R-CNN的这个缺点

7. #### Mask R-CNN

   .....................

#### overview

![1582732099730](C:\Users\moonie\AppData\Roaming\Typora\typora-user-images\1582732099730.png)

### 详解one-stage

#### 1. overFeat

arbitrary input，它认为目标检测就是一个multi region classification

#### 2.YOLO

它认为目标检测就是一个regression问题，把整张图片分割成grid,并且认为每个cell至多包含两个物体，

速度非常快，155FPS

缺点：因为认为每个cell至多包含两个物体，所以不能检测出较小或者拥挤的物体；只用最后一层 feature map来做预测，不适合预测multi scales and aspect ratios的物体

#### 3.Single-Shot Mulibox Detector (SSD)

为了解决YOLO的缺点，

SSD对划分的每个cell生成多个尺度的anchors，

并且为了检测 large objects,多加了几个feature map,同时预测出结果

hard nega	tive mining

intensive data augmentation

最终实现了Faster R-CNN的精度，和real-time的速度

#### 4.Retinanet

解决了one-stage所存在的class imbalance 问题，引入focal loss而不是简单的舍去忽略easy negative,比 hard negative mining更有效

金字塔结构用于multi 问题，极其有效

##### 5.YOLOv2

用k-means cluster的方式来进行anchor的生成，一定程度上解决hard negative的问题

使用在high resolution上进行训练过的backbone，weights更能注意到精细的信息

Batch Normalization layers

multi-scale training techniques

#### 6.cornernet(anchor-free)

.....没太理解

#### overview

![1582817907367](C:\Users\moonie\AppData\Roaming\Typora\typora-user-images\1582817907367.png)

### backbone

​	VGG16, ResNet, ResNeXt and Hourglass., efficientnet......