---
layout: post
title:  "Deep Learning Projects - Tips & Tricks"
date:   2020-10-1
categories: 
# tags: papers
# permalink: /:title:output_ext
readmin: 3
description: 
comments: true
---

In this post, I will talk about the tricks I learnt throught my masters thesis. So, I will keep updating this post.


### 1. Project updates
If you have a big model that you will change alot of stuff like the architecture, the augmentation or the batch, keep in mind to do the foloowing.
1. Use a version control system such as git, to be able to keep all changes that you may need later.
2. Make a google sheet of the versions and their results. This would help a lot to know what works well and what does not.

### 2. Batch Normalization
When the batch size is small (mine was 1,2,4), the performance drops drastically. According to [this paper](https://arxiv.org/pdf/1803.08494.pdf), the batch norm performance drops when the batch size decreases. Therefore, if you have to use small batch sizes (about less than 16 ), think of changing batch normalization to other normalization techniques such as [group norm](https://arxiv.org/pdf/1803.08494.pdf) which is not affected by batch size.

### 3. Data augmentation
We go to data augmenattion to aboid overfitting especially if our dataset is small. But, not all data augmentation techniques are suitable for all tasks. We choose the augmentations that we expect that our model sees in the validation and that have features that are not very different from those in the data. \\
However, if you are not sure if some augmenattion technique is good, you have to try it. Sometimes it works unexpectedly.\\
In my thesis, I have RGB data and LiDAR BEV data on two different branches. I expected that making different augmentations on the two branches (such as translating BEV and keeping RGB fixed) would make the model not able to work or match the features. But this did not happen. The model was big enough to match the features from both branches and getting the same mAP (for an 3d object detection task) as using BEV alone.

