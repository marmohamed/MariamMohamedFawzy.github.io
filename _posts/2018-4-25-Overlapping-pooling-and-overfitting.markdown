---
layout: post
title:  "Overlapping pooling and overfitting"
date:   2018-4-25
categories: jekyll update
---

<link href="https://fonts.googleapis.com/css?family=Prompt&display=swap" rel="stylesheet">
<link rel="stylesheet" href="{{'/assets/css/style_font.css'}}" />


*** The content may be outdated and inaccurate

---

<br>
Since I found talks of Geoffrey Hinton speaking about the pooling layer and how it was not that useful as we think, I started reading about this component but found few papers talking about it deeply.

He differentiated between the overlapping and the non-overlapping pooling. As in [ImageNet Classification with Deep Convolutional Neural Networks](https://www.nvidia.cn/content/tesla/pdf/machine-learning/imagenet-classification-with-deep-convolutional-nn.pdf), the overlapping pooling is more difficult to overfit. In this post, I will try both the overlapping and the non-overlapping pooling and see how each one performs on the same dataset in terms of overfitting and accuracy.

The pooling layer is defined by the window size (F), stride (S) (steps/pixels between the windows) and the function it does on the pixels in the window. It is an overlapping pooling if the windows overlap (F < S).

I trained CIFAR10 from Keras datasets for only 50 epochs.

All models have the following structure

<img src="/assets/images/model.png" alt="model" style="width: 400px; height=600px;"/>

### Model 1:

Both max-pooling components contain one pooling layer, each has F = 2, S = 2 (**nonoverlapping**). The image size before the fully connected layer is (8, 8).


<img src="/assets/images/model_1_acc.png" alt="model" style="width: 300px; height=400px;"/>  <img src="/assets/images/model_1_loss.png" alt="model" style="width: 300px; height=400px;"/>


***Accuracy on test set: 0.8218***


### Model 2:

Both max-pooling components contain one pooling layer, each has F = 2, S = 1 (**overlapping**) . The image size before the fully connected layer is (30, 30).

 
<img src="/assets/images/model_2_acc.png" alt="model" style="width: 300px; height=400px;"/>  <img src="/assets/images/model_2_loss.png" alt="model" style="width: 300px; height=400px;"/>


***Accuracy on test set: 0.7795***

**The gap between the validation accuracy and the training accuracy is bigger in the overlapping pooling than in the non-overlapping pooling. Moreover, the test set accuracy is lower in the case of overlapping pooling. This is different from what is in [https://www.nvidia.cn/content/tesla/pdf/machine-learning/imagenet-classification-with-deep-convolutional-nn.pdf](ImageNet Classification with Deep Convolutional Neural Networks)**

### Model 3:

Both max-pooling components contain one pooling layer, each has F = 4,S = 2 (**overlapping**). The image size before the fully connected layer is (6, 6).

 
<img src="/assets/images/model_3_acc.png" alt="model" style="width: 300px; height=400px;"/>  <img src="/assets/images/model_3_loss.png" alt="model" style="width: 300px; height=400px;"/>


***Accuracy on test set: 0.843***

**The pooling window is larger and the accuracy is better. The gap between the validation and training accuracy is smaller.**


### Model 4:

Both max-pooling components contain one pooling layer, each has F = 4,S = 4 (**nonoverlapping**). The image size before the fully connected layer is (2, 2).

 
<img src="/assets/images/model_4_acc.png" alt="model" style="width: 300px; height=400px;"/>  <img src="/assets/images/model_4_loss.png" alt="model" style="width: 300px; height=400px;"/>


***Accuracy on test set: 0.8134***


### Model 5:

No pooling layers in this model. The image size before the fully connected layer is (32, 32).

 
<img src="/assets/images/model_5_acc.png" alt="model" style="width: 300px; height=400px;"/>  <img src="/assets/images/model_5_loss.png" alt="model" style="width: 300px; height=400px;"/>


***Accuracy on test set: 0.7249***

**What I noticed is that there may be a relationship between the image size between the convolution layers and before the fully connected layer (how the features are selected and compressed) and the accuracy.**

### Model 6:

The first max-pooling component has one max-pooling layer, the second one has two, each has F = 4, S = 2 (**overlapping**). I added two pooling layers in the second component to reduce the image size before the fully connected layer to be (2, 2), as in the non-overlapping version (Model 4).

 
<img src="/assets/images/model_6_acc.png" alt="model" style="width: 300px; height=400px;"/> <img src="/assets/images/model_6_loss.png" alt="model" style="width: 300px; height=400px;"/>


***Accuracy on test set: 0.8139***

**The accuracy is similar to the non-overlapping version (Model 4).**

### Model 7:

The first max-pooling component has one max-pooling layer, the second one has two, each has F = 3, S = 4 (**nonoverlapping**).

 
<img src="/assets/images/model_7_acc.png" alt="model" style="width: 300px; height=400px;"/> <img src="/assets/images/model_7_loss.png" alt="model" style="width: 300px; height=400px;"/>


***Accuracy on test set: 0.7961***

**Although it is non-overlapping, the gap between the training and validation accuracy is very small, but the accuracy is a bit smaller.**


## Conclusion

There is a relationship between how the features are selected and compressed (indicated by the image size after each layer) and the accuracy of the model on the test set. There are factors other than having overlapping pooling layers such as the window size and how it is comparable to the stride.