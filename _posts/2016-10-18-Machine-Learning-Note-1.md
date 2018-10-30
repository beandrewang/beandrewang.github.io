---
layout: post
title: Machine Learning 学习笔记（一）
date: 2016-10-18 11:11:00
category: Machine Learning
tags: MachineLearning
postPatterns: 'circuitBoard'
---

这篇笔记是本人在学习Andrew Ng教授在Coursera上的Machine Learning课程所做的，是基于我对本课程的理解所做，其目的是提炼精华，供后期参考。其中本课程的课后作业详见[assignments](https://github.com/beandrewang/machine_learning). 

## 什么是机器学习？

按照本人的理解，就是用传统方式很难推算出的问题，很难用逻辑运算计算得到，比如传统的C语言编程。根据问题提供的数据，对数据进行拟合(regression), 分类(classification) 以及聚类（Clustering）等各种方法。 

机器学习很难得到准确答案，只能得到最大近似结果。 

## 机器学习的分类

* 监督学习（Supervised)，有的中文翻译为“有导师学习”， 就是供算法学习的样本包含了已知的结果，英文讲的 （labled data）.

* 无监督学习 (Unsupervised)，有的中文翻译为“无导师学习”，跟监督学习相反，供算法学习的样本没有答案，即人也不知道这些样本应该对应什么样的答案。

## 监督学习

* 拟合(regression), 这类问题就是根据已知的数据去逼近一个函数，使已知的数据到这个这个函数的距离最小，最好的情况就是所有数据都在这个函数上。然后可以根据这个函数去预测新的数据。关键点，**连续**。

* 分类(classification)，这类问题时给已知的数据分类，也是构造一个函数，使已知的数据带入这个函数得到的结果在0-1之间，根据算法定义的阈值，能够得到>阈值的属于这一类，<阈值的不属于这一类。也可以给新的数据分类。关键点，**离散**。

## 拟合

根据数据先猜一个函数h，然后计算得到使cost function最小的theta值。这样算法的精华部分就是**如何求函数的最小值的问题**。

在高等数学中，最小值的地方，二次导数为0。在几何上表达就是切线的斜率为0. 算法上讲：最简单的方法是**梯度下降法(Gradient Decent)**。

### 单一变量拟合

算法的**输入输出**都只有一个。

Hypothesis function: $h_{\theta}(x) = \theta_{0} + \theta_{1}x$

Cost function: $J(\theta) = \frac{1}{2m}\sum_{i = 1}{m}(h_{\theta}x^{i} - y^{i})^{2}$

### 梯度下降法

$$
\theta_{j} := \theta_{j} - \alpha \frac{d}{d\theta_{j}}J, \ \ \ \ j = 0, 1, 2\cdots m
$$

### 根据梯度下降法拟合

其思路就是对所有的数据进行梯度下降求得参数theta. 

算法的步骤：

```matlab
repeat until convergence
{
    theta0 = theta0 - alpha *（1/m)*sum(h(x(i) - y(i)))
    theta1 = theta1 - alpha * (1/m)*sum(h(x(i) - y(i))*x(i))
}
```
