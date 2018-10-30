---
layout: post
title: A way to stitch images on stereo camera or 360 degree camera
tags: ComputerVision
date: 2016-11-23 16:33:00
postPatterns: 'circuitBoard'
---

# Abstract 

We know that in stereo camera geometry, there are epipolar lines in the left and right image because there are some overlapped scene in the left and right images. 

What about the 360 degree camera? This article is about to introduce a new way to do stitching on the scenario of stereo or 360 degree cameras.

# Fundamental Matrix

For the stereo or 360 degree cameras, both of them have the overlapped view. For the overlapped view, the points in the left(front) view must have a matched point int the right(back) view. 

If we marked some points in the view with a marked chart. We can calculate the fundamental matrix with the equation as below: 

$$
p'^{T}Fp = 0 \tag{1}
$$ 

$p'$ is the point in left(front) view, $p$ is the point in the right(back) view, $F$ is the fundamental matrix. 

# Epipolar line

If we get the fundamental matrix $F$, then the epipolar line is 
$$
l = FP \tag{2}
$$

# Find a feature from the left(front) overlapped view

We can find a feature by the Harris corner algorithm or other way. 

# Find the matched feature in the right(back) overlapped view

Since we have computed the epipolar line, then we can find the matched feature along with the epipolar line by a normal correlation function and then obtain the best matched point. 

# Calculate the phase of gradient for matched feature 

We already know the direction of the gradient for the feature from left(front) view, and also know the direction of the gradient for the feature from the right(back) view, there must be a difference between them. We can get the **rotation angle**.  Use the angle to rotate the right(back) view. 

# Calculate the offset for the feature

For the left view and right view, we can get the coordinate easily, then cut the right part from the feature in the left(front) view and also the left part in the right(back) view. 

# Stitch them into a panorama

Just add them. 

# Advantages

1. Less computing than the traditional SIFT. 
2. Easily implementation. 
3. Overcome the error from calibration for the cameras. 
4. Real time stitching. 

