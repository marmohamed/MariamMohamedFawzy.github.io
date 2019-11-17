---
layout: post
title:  "CNN vs Capsule Net - Model robustness"
date:   2018-3-24
categories: jekyll update
---

Capsule network is getting more and more attention after the dynamic routing paper was published in 2017. I beleived that capsule network will be much better than normal CNN in recognizing images with different transformations as it focuses on ratings, not single pixels, parts of objects existing in the image will give more weight to the correct object. Therefore, I have trained both CNN and capsule network on MNIST data set for 12 epochs. Ecah gives more than 0.99% accuracy on training set and test set. I then tested the two models on the test set after applying several operations.

The four variations of the noisy test set are:

1. Salt and pepper noise.
2. Image bluring.
3. Rotation.
4. Shifting the image to the right.

<br>

1. Salt and pepper noise:

Salt and pepper noise is added to the test set with different probabilities (0.2, 0.3, 0.4). Accuracy of both models decrease compared to the original test set, but in the three cases, the accuracy of capsule network decreases more as shown in figure[1].

![salt and peppr noise](/blog/assets/images/s_p.png)

Normal CNN is more robust to salt and pepper noise than capsule network. This may be because salt and pepper noise destroys the shape of the digit and capsule network depends on ratings from the different parts of the shape, while CNN depends on pixels not shape parts. Therefore, CNN can resist scattered noise.

2. Image bluring:

Kernels of different sizes are applied on test set. I have compared the effect of different kernel sizes on both models as in figure[2].

![salt and peppr noise](/blog/assets/images/blur.png)

Both models seem comparable to each other on blured images, although CNN seems slightly better. This may be because blured images look similar to the original images especially when the kernel size is small.

3. Rotation:

Images from test set are rotated counter clock wise 20, 30 and 45 degrees, Accuracyies of the two models are shown in figure[3].

![salt and peppr noise](/blog/assets/images/rotation.png)

Before testing the models on the rotated images, I expected that capsule network will perform better as it depends on parts of shapes not single pixels. This is what happened. The more the angle of rotation is, the better the capsule network is than normal CNN.

4. Shifting:

Images are shifted to the right 6, 8, 10 and 12 pixles and the two models are tested on the images after shifting. Accuracies of the two models are shown in figure[4].

![salt and peppr noise](/blog/assets/images/shift.png)


CNN seems more robust to shifting images to the right than capsule network that performs worse when the images are shifted more pixels. This seems logical as shifting the image can hide big parts of the image, the remaining parts may vote for the wrong digit as the missing part's vote will not be considered.

### Conclusion

what I noticed from testing the two models (trained on images with no transformation) on images after applying the four transformation operations is that capsule network is more robust to transformations that do not add any random pixels to the original images, but preserve the digit's shape. However, convolution network is better with images with scattered noise or images with big part of the digits inside.
