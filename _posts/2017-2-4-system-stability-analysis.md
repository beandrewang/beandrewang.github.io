---
layout: post
title: 系统的稳定性分析
date: 2017-2-4 13:34:00
tags: control theory
postPatterns: 'circuitBoard'
---

我们可以通过求解系统的特征方程得到系统的特征值，以及他们在S平面上的分布。但是如果系统比较复杂，求解高阶特征方程非常困难，但是现在有计算机估计也不是太难了。但是我们分析系统的稳定性，根本就不需要得到特征方程的根，只需要知道特征值在S平面上的分布就可以判定系统是否稳定。

根据特征方程的在S平面的分布判定系统稳定性，见下图：
![](https://github.com/beandrewang/beandrewang.github.io/blob/master/images/system_stable_analysis/3_16.jpg?raw=true)

系统的特征方程可以用下式表示

$$
D(s) = a_{n}s^{n} + a_{n - 1}s^{n-1} + \cdots + a_{1}s + a_{0} = 0
$$

$a_{n}, a_{n - 1}, \cdots, a_{0}$ 都为实数，并且都大于0，系统才能是稳定的。不过注意这个条件只是必要条件，并不是充分条件。也就是说不符合这个条件的系统肯定是不稳定的，但是符合这个条件的系统不一定是稳定的。

# Routh判据

* 系统的特征方程的各个系数都为正数
* 按照特征方程的系数写Routh矩阵

$$
\begin{bmatrix} \
s^{n} & a_{n} & a_{n - 2} & a_{n - 4} & \cdots \\
s^{n - 1} & a_{n - 1} & a_{n - 3} & a_{n - 5} & \cdots \\
s^{n - 2} & b_{1} & b_{2} & b_{3} & \cdots \\
s^{n - 3} & c_{1} & c_{2} & c_{3} & \cdots \\
s^{n - 4} & d_{1} & d_{2} & d_{3} & \cdots \\
\vdots & \vdots & \vdots & \vdots & \vdots \\
s^{1} & f_{1} & f_{2} & f_{3} & \cdots \\
s^{0} & g_{1} & g_{2} & g_{3} & \cdots \\
\end{bmatrix}
$$

其中，

$$
b_{1} = -\frac{1}{a_{n - 1}} \
\begin{vmatrix} \
a_{n} & a_{n - 2} \\
a_{n - 1} & a_{n - 3} \\
\end{vmatrix} 
$$

$$
b_{2} = -\frac{1}{a_{n - 1}} \
\begin{vmatrix} \
a_{n} & a_{n - 4} \\
a_{n - 1} & a_{n - 5} \\
\end{vmatrix} 
$$

$$
b_{3} = -\frac{1}{a_{n - 1}} \
\begin{vmatrix} \
a_{n} & a_{n - 6} \\
a_{n - 1} & a_{n - 7} \\
\end{vmatrix} 
$$

按照这个规律进行，直至剩余的$b_{i}$ 都为0.

$$
c_{1} = -\frac{1}{b_{1}} \
\begin{vmatrix} \
a_{n - 1} & a_{n - 3} \\
b_{1} & b{2} \\
\end{vmatrix}
$$

$$
c_{2} = -\frac{1}{b_{1}} \
\begin{vmatrix} \
a_{n - 1} & a_{n - 5} \\
b_{1} & b{3} \\
\end{vmatrix}
$$

$$
c_{1} = -\frac{1}{b_{1}} \
\begin{vmatrix} \
a_{n - 1} & a_{n - 7} \\
b_{1} & b{4} \\
\end{vmatrix}
$$

按照这个规律进行计算，最后得到Routh矩阵，在运算过程中，为了简化运算，对某一行都乘以一个正数，不会影响稳定性判断。

* 查看Routh矩阵的第一列，如果第一列元素的符号，如果第一列都是正数，则该系统是稳定的。即特征方程所有的根均位于S平面的左半平面。假若第一列元数有负数，则第一列元素的符号的变化次数等于系统在S平面右半平面上的根的个数。

我们可以通过Routh判据来调整系统的参数，使其达到稳定性要求。

# 减小系统稳态误差的方法

* 增大系统开环增益K；
* 在系统前向通道增加积分环节；
* 采取复合控制, 这种控制系统，顺馈既能减小系统稳态误差，又不影响反馈控制的稳定性。

前两种方法都有可能造成系统不稳定。但是可以在一定限制条件下应用。

> Written with [StackEdit](https://stackedit.io/).
