---
layout: post
title: Newton Methods
---

<p class="meta">1 January 2019 - Beijing</p>

不知不觉的这已经是第三个年头了，今天是19年的第一天，就写一下最近的关于优化的一些东西吧，这篇主要介绍牛顿法。

作为一个二阶优化算法，牛顿法的思想和其他的优化算法是类似的，都是通过对函数的局部逼近，通过寻找局部最小值从而一点一点实现全局最小的目标，对于梯度算法而言，牛顿法对局部函数实现了二阶的近似，因此，可以通过求导直接得到目前的二阶近似的最优值点（一阶近似的时候求完导数就只剩梯度了，因此需要步长），我们可以看一下一个二阶可导的函数在$$w_t$$点处的展开情况：

$$f(x) \approx f(w_t) + \triangledown^T f(w_t)(w - w_t) + \frac{1}{2}(w-w_t)\triangledown^2f(w_t)(w - w_t)  $$

对这个二阶近似求导，可以得到导数的表达式是

$$\triangledown^T f(w) \approx  \triangledown^T f(w_t) + \triangledown^2f(w_t)(w - w_t) $$

于是令左边的式子为零可以得到迭代公式如下：

$$w_{t+1} = w_t -  [\triangledown^2f(w_t)]^{-1} \triangledown f(w_t) $$

但是因为涉及到二阶矩阵不一定正定以及需要的计算量太多和存储量比较多的情况，所以进一步地又提出了拟牛顿法，主要思想史利用迭代的方式不断更新二阶海森矩阵

需要让近似矩阵满足近似条件：

$$\triangledown^T f(w) \approx  \triangledown^T f(w_t) + B_t(w - w_t) $$

于是我们引进了$$\triangledown^T f(w) -  \triangledown^T f(w_t)$$导数更新量和$$(w - w_t)$$参数更新量的概念用于海森矩阵的迭代更新。具体公式可以参见相关参考书，但是思路大致到此为止。
