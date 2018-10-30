---
layout: post
title: Canny Edge Detector
tags: ComputerVision
date: 2016-10-26 10:54:00
postPatterns: 'circuitBoard'
---

边缘检测的方法有很多，其基本思想如下:

1.  必须检测出图像中尽可能多的边缘
2.  检测出的边缘点应该准确的位于边缘的中心
3.  图像中的一个边缘只能被检出一次，不能多次被检出


# Canny edge detector

Canny edge detector 现在广泛应用于机器视觉领域的边缘检测任务，其优点是简单高效。

步骤： 

1. Apply Gaussian filter to smooth the image in order to remove the noise
2. Find the intensity gradients of the image
3. Apply non-maximum suppression to get rid of spurious response to edge detection
4. Apply double threshold to determine potential edges
5. Track edge by hysteresis: Finalize the detection of edges by suppressing all the other edges that are weak and not connected to strong edges.

## 高斯滤波

为什么要滤波呢？是因为图像中的噪声会对边缘检测造成干扰。可以想象一下，边缘检测的一个重要步骤就是计算图像的梯度值，比如一个像素的相邻像素正好是一个噪点，那么计算出的这个梯度可能就会非常大，被误认为是边缘，因此滤除噪声是首要步骤。

给图像做高斯滤波的过程就是，拿一个高斯滤波器跟原始图像做卷积。

举例，一个(2k+1)x(2k+1)的高斯滤波器

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/4a36d7f727beeaff58352d671bb41a3aca9f44d6)

用这个公式可以计算出一个滤波器的矩阵。

图像A, 通过 5x5，![](https://wikimedia.org/api/rest_v1/media/math/render/svg/59f59b7c3e6fdb1d0365a494b81fb9a696138c36) = 1.4 的高斯滤波器，得到的图像B

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/efce20969e243d1ba3f34c2f7126041095bd4656)

**高斯滤波器的size越大，滤波效果越好，但是同时边缘检测的误差也就越大， 一般情况下，建议用5x5.**

## 计算梯度图像

Canny 通过计算4个方向的梯度：水平，垂直，2个对角线。

## 非极大值抑制(Non-maximum suppression)

用于对边缘进行瘦身, 因为得到的梯度图像还是模糊的（高斯造成的），所以找出边缘处的极大值，其他的值变为0. 

步骤：

1. Compare the edge strength of the current pixel with the edge strength of the pixel in the positive and negative gradient directions.
2. If the edge strength of the current pixel is the largest compared to the other pixels in the mask with the same direction (i.e., the pixel that is pointing in the y direction, it will be compared to the pixel above and below it in the vertical axis), the value will be preserved. Otherwise, the value will be suppressed.

## 双阈值

经过非极大值抑制之后，虽然边界会更清晰，但是可能还存在由于噪声的影响出现的假的边缘，这样就用双阈值的方法来做。

步骤：

1. 定义两个阈值，分别对应弱梯度值和强梯度值。
2. 如果边缘像素的梯度比上阈值大，那么这是强梯度值。
3. 如果边缘像素的梯度比上阈值小，但是比下阈值大，这是弱梯度值
4. 如果边缘像素的梯度比下阈值小，去掉他，将此像素的值变为0

## Edge tracking by hysteresis

其思想就是除掉那些跟强梯度值不连续的弱梯度值所在的像素。

## Canny的缺点

1. Gaussian filter is applied to smooth out the noise, but it will also smooth the edge, which is considered as the high frequency feature. This will increase the possibility to miss weak edges, and the appearance of isolated edges in the result.
2. For the gradient amplitude calculation, the old canny edge detection algorithm uses center in a small 2×2 neighborhoods window to calculate the finite difference mean value to represent the gradient amplitude. This method is sensitive to noise and can easily detect fake edges and lose real edges.
3. In traditional canny edge detection algorithm, there will be two fixed global threshold values to filter out the false edges. However, as the image gets complex, different local areas will need very different threshold values to accurately find the real edges. In addition, the global threshold values are determined manually through experiments in the traditional method, which leads to complexity of calculation when large number of different images needs to be dealt with.
4. The result of the traditional detection cannot reach a satisfactory high accuracy of single response for each edge- multi-point responses will appear

## 改进

详情参考 [Improvement on Canny edge detection](https://en.wikipedia.org/wiki/Canny_edge_detector#Improvement_on_Canny_edge_detection)


# References

* [https://en.wikipedia.org/wiki/Canny_edge_detector](https://en.wikipedia.org/wiki/Canny_edge_detector)



