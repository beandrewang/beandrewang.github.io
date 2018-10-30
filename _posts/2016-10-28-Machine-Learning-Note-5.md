---
layout: post
title: Machine Learning 学习笔记（五）
tags: MachineLearning
date: 2016-10-28 22:41:00
postPatterns: 'circuitBoard'
---

## 分类损失函数 

$$
J_{\theta} = -\frac{1}{m}\sum_{i = 1}^{m}\left[y^{(i)} \log{(h_{\theta}(x^{i}))} + (1 - y^{(i)})
              \log{(1 - h_{\theta}(x^{(i)}))}\right]
$$

*矩阵表达形式*

$$
h = g(X\theta)
$$

$$
J(\theta) = \frac{1}{m}\left(-y^{T}\log{(h)} - (1 - y)^{T}\log{(1 - h)}\right)
$$

## 梯度下降法

`Repeat {`

$$
\theta_{j} := \theta_{j} - \alpha\frac{\partial}{\partial \theta_{j}}J(\theta) 
$$

`}`

分解之后得到：

`repeat {`

$$
\theta_{j} := \theta_{j} - \frac{\alpha}{m}\sum_{i = 1}^{m}\left(h_{\theta}^{(i)} - y^{(i)} \right)x_{j}^{(i)}
$$

`}`

*矩阵表达式*

$$
\theta := \theta - \frac{\alpha}{m}X^{T}\left(g(X\theta) - \vec{y}\right)
$$

## 高级最优化

比梯度下降法更高效的求损失函数最小值的方法有

* Conjugate gradient
* BFGS
* L-BFGS

用这些方法，我们只需要提供损失函数 $J(\theta)$ , 初始化  $\theta$  和其梯度 $\frac{\partial}{\partial \theta_{j}}J(\theta)$  即可.

用matlab来表达

```matlab
  function [Jval, gradient] = costFunction(theta)
    jVal = [...code to computer J(theta)...];
    gradient = [... code to computer derivative of J(theta) ];
  end
```

```matlab
  options = optimset('GradObj', 'on', 'MaxIter', 100);
  initialTheta = zeros(2, 1);
  [optTheta, functionVal, exitFlag] = fminunc(@costFunction, initialTheta, options);
```

## 多类分类器（Multiclass classfication: One-Vs-All）

基本思路： 分别对每一类做二类分类，得到每一类对应的预测值，预测值哪一类最大，那么这个样本就属于那一类。

# 正规化(regularization)

*Underfitting*, or *High Bias*: 就是得到的曲线误差跟样本的误差太大，可能是模型太简单或者特征值
太少。比如真实的情况应该是一个二次曲线，那么我们用直线模型去预测的话就会导致误差巨大，这种问题
我们称之为Underfitting or High Bias. 

*Overfitting*, or *high variance*: 就是得到的曲线对样本拟合的非常好，误差非常小，但是如果有新样本
加入的话，模型的误差就会变得非常大。

那么怎么克服这两种情况呢？

* Under	fitting, 可以适当增加特征值，或者增加样本数目来克服
* Overfitting， 这种情况可以通过加少特征值或者减少某些特征值的权重来克服。

## 正规化拟合

线性拟合和分类方法都可以进行正规化操作。

### 损失函数

$$
J_{\theta} = -\frac{1}{m}\sum_{i = 1}^{m}\left[y^{(i)} \log{(h_{\theta}(x^{i}))} + (1 - y^{(i)})
              \log{(1 - h_{\theta}(x^{(i)}))}\right] + \frac{\lambda}{2m}\sum_{j = 1}^{n}\theta_{j}^{2}
$$

### 梯度下降法

我们可以通过修改梯度下降法的公式来加入正规化操作，如下边公式，

```
repeat{
```
$$
\theta_{0} := \theta_{0} - \alpha \frac{1}{m} \sum_{i = 1}^{m}\left(h_{\theta}(x^{(i)}) - y^{(i)}\right)x_{0}^{(i)}
$$

$$
\theta_{j} := \theta_{j} - \alpha\left[\left(\frac{1}{m}\sum_{i = 1}^{m}(h_{\theta}(x^{(i)}) - y^{(i)})x_{j}^{(i)}\right) + \frac{\lambda}{m}\theta_{j}\right]
$$
```
}
```

其中， $\frac{\lambda}{m}\theta_{j}$ 就是正规化项。

公式也可以表达成下边的形式:

$$
\theta_{j} := \theta_{j}(1 - \alpha \frac{\lambda}{m}) - \alpha \frac{1}{m} \sum_{i = 1}^{m}\left(h_{\theta}(x^{(i)}) - y^{(i)}\right)x_{j}^{(i)}
$$

$1 - \alpha \frac{\lambda}{m}$ 总是小于1，我们可以看出，每一次迭代，$\theta_{j}$ 都会减小影响力。

### 正规公式

$$
\theta = \left(X^{T}X + \lambda \cdot L\right)^{-1} X^{T}y \\\\\\
where \ \ L = 
\begin{bmatrix}
0\\
&1\\
&&1\\
&&&\ddots\\
&&&&1\\
\end{bmatrix}
$$

