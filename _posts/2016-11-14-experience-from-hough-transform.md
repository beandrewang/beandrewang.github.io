---
layout: post
title: Some experiences from the hough transform problem sets
tags: ComputerVision
date: 2016-11-14 09:30:00
postPatterns: 'circuitBoard'
---

The problem sets is [here](), and the answers I did is [here](). 

1. smooth the image
	
    In order not to generate a black edge at the right side of the image after smoothing, we's better choose the method 'symmetric' instead of others to smooth the image.
    
    ```matlab
    gauss_f = fspectial('gaussian', 9, 3);
    img_smoothed = imfilter(img, gauss_f, 'symmetric');
    ```
2. find the edge

	I'd like to use canny edge detector for edge finding, and we also can control the up/down threshold to generated the best looking edge we'd like. 
    
    ```matlab
    img_edges = edge(img_smoothed, 'canny', [up, down]);
    ```
    
3. Hough lines implementation

	The lines in the image is transformed to a point in hough space, meanwhile the point in image is transformed as a line in hough space.
    
    * point -> line
    * line -> point

	for a point in the edge image, we transform the points to the corresponding lines in hough space at all possible theta. 

$$
\theta = -90 \cdots 89
\rho = i * cos(theta) + j * sin(theta);
$$

4. Hough circle fitting

	The circle is mapping to a circle in hough space. if we know the radius for the circle, we can get the center of the circle by plot the circles on the points with the same radius, the center would be the maximum with intersection. 
    
    If we don't know the radius, we'd better find the circles with all the possible radius and get the maximum of intersection. 
    
5. find the paralell lines

	The paralell lines have the same $\theta$ or little difference, try to sort the peaks with this feature, we can get the paralell lines. 
   
