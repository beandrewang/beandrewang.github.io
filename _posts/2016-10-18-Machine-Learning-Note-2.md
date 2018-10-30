---
layout: post
title: Machine Learning 学习笔记（二）
date: 2016-10-18 11:41:00 
category: Machine Learning
tags: MachineLearning
postPatterns: 'circuitBoard'
---

## 多变量线性拟合(Linear Regression with Multiple Variables)

跟单变量线性拟合不同，多变量拟合是**多输入单输出**的系统。

Hypothesis function: 

$$
h_{\theta}(x) = \theta_{0} + \theta_{1}x_{1} + \theta_{2}x_{2} + \cdots + \theta_{n}x_{n}
$$

```matlab
h = theta0 + theta1x1 + theta2x2 + ... + thetanxn # 有n个特征。
```

```matlab
h = [theta0, theta1, ... thetan] * [x1, x2, x3, ... xn]' # 这里'代表转置
```

Cost function:

$$
J(\theta) = \frac{1}{2m}\sum_{i = 1}^{m}\left(h_{\theta}(x^{i}) - y^{i}\right)^{2}
$$

```matlab
J(theta) = 1 / 2m *(sum(h(x(i)) - y(i)) ^ 2), i = 1 ... m # 公式其实跟单变量线性拟合一样 
```

```matlab
J(theta) = 1 / 2m * (X*theta - Y)' * (X*theta - Y) # 矩阵表达式
``` 

Gradient decent:

也就是对每一个特征值求偏导数

$$
\theta_{j} := \theta_{j} - \alpha\frac{1}{m}\sum_{i = 1}^{m}\left(h_{\theta}(x^{i}) - y^{i}\right)x_{j}^{i}
$$

```matlab
repeat to converence
{
  theta0 = theta0 - alpha / m * sum((h(x(i)) - y(i)) * x0(i)))
  theta1 = theta1 - alpha / m * sum((h(x(i)) - y(i)) * x1(i)))
  theta2 = theta2 - alpha / m * sum((h(x(i)) - y(i)) * x2(i)))  
  ...
}
```

in other word

```matlab
repeat to converence
{
  thetaj = thetaj - alpha / m * sum((h(x(i)) - y(i)) * xj(i)), for j = 1 ... n
}
```

in other word

```matlab
theta = theta - alpha * X' * (X * theta - Y)
```

