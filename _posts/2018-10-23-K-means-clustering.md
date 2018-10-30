---
layout: post
title: K-means clustering
date: 2018-10-23 11:26:00
tags: algorithm
postPatterns: 'circuitBoard'
---

* Input: K, set of points $x_{1} ... x_{n}$
* Place centroids $c_{1} ... c_{k}$ at random locations
* Repeat until convergence:
    - for each point $x_{i}$:
        * find nearest centroid $c_{j}$
        * assign the point $x_{i}$ to cluster j
    - for each cluster j = 1 ... K:
        * new centroid $c_{j} = $ means of all points $x_{i}$ assigned to cluster j in previous step
* Stop when none of the cluster assignements change
