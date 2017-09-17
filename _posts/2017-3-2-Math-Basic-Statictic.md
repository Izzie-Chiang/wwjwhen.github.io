---
layout: post
title: Math Basis of Statistic Learning
---

<p class="meta">2 March 2017 - Beijing</p>

好像很久没有写了呢，看了percy Liang的教学笔记，没有看完但是觉得很有收获的，教学内容被percy Liang分成了4个部分，
从asymptotics到uniform convergence到online learning外加一个kernel method。在看的时候其实是有非常多的东西是看不懂的，
尤其是统计学习理论有非常多的数学基础，所幸的是appendix中的结论简洁明了，不绕圈子，所以很想总结一下，估计以后一定会用到的。

In the following, assume the following:

Let $$X_{1}, X_{2}, ..., X_{n}$$ be real vectors drawn i.i.d. from some distribution with mean $$\mu$$
and covariance matrix $$\Sigma$$.

**Convergence in probability**

We say that a sequence of random variables ($$Y_{n}$$) converges in probability to a
random variable $$Y$$ (written $$Y_{n} \overset{P}{\rightarrow} Y$$)

if for all $$\epsilon > 0$$, we have $$lim_{n\rightarrow\infty}\mathbb{P}[|Y_{n}-Y| \geq \epsilon ] = 0$$
Example: 

$$\hat{\mu}\overset{p}{\rightarrow} \mu$$ (weak law of large numbers)


**Convergence in distribution**

We say that a sequence of distributions ($$P_{n}$$) converges in distribution (weak convergence) to a distribution $$P$$
(written $$Y_{n} \overset{D}{\rightarrow} Y$$) if for all bounded continuous functions 
$$f$$, $$lim_{n\rightarrow \infty}\int f(x)P_{n}(x)dx = \int f(x)P(x)dx$$

When we write &&Y_{n} \overset{D}{\rightarrow} Y&& for a sequence of random variables ($$Y_{n}$$), we mean that the sequence of distribution of those random variables converges in distribution.

Example: 

$$\sqrt[]{n}(\hat{\mu}-\mu) \overset{d}{\rightarrow}N(0, \Sigma )$$(central limit theorem)


**Continuous mapping theorem**

This theorem allows us to transfer convergence in probability and convergence in distribution results through continuous transformations.

If $$f:\mathbb{R}^{d}\overset{}{\rightarrow}\mathbb{R}^{k}$$ is continuous and $$Y_{n} \overset{P}{\rightarrow} Y$$, then

$$f(Y_{n}) \overset{P}{\rightarrow} f(Y)$$

If $$f:\mathbb{R}^{d}\overset{}{\rightarrow}\mathbb{R}^{k}$$ is continuous and &&Y_{n} \overset{D}{\rightarrow} Y&&, then

$$f(Y_{n}) \overset{D}{\rightarrow} f(Y)$$

Example: 

$$\left \| \hat{\mu} \right \| \overset{P}{\rightarrow}\left \|\mu  \right \|$$

**Delta method**

This theorem allows us to transfer asymptotic normality results through smooth transformations.

If $$\bigtriangledown f:\mathbb{R}^{d}\rightarrow (\mathbb{R}^{d}\times\mathbb{R}^{k})$$ is continous, and $$\sqrt[]{n}(\hat{\mu}-\mu) \overset{d}{\rightarrow}N(0, \Sigma )$$, then 

$$\sqrt[]{n}(f(\hat{\mu})-f(\mu)) \overset{d}{\rightarrow}N(0, \nabla f(\mu)^{T} \Sigma \nabla f(\mu))$$

The intuition is Talor approximation:

$$f(\hat{\mu}) - f(\mu) = \nabla f(\mu)^{T}(\hat{\mu} - \mu)$$