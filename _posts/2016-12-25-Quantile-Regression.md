---
layout: post
title: Quantile Regression
---

<p class="meta">25 December 2016 - Beijing</p>

Merrey Christmas!!
圣诞节，考研日，在实验室看了Quantile Regression的相关的文献，希望能够搞明白这到底是个啥东西。
最近要用quantile regression是因为在一个基因表达分析的任务中需要使用，话不多说，直接把很高屋建瓴的设计思想post出来：

What the regression curve does is give a grand summary for the **averages** of the distributions corresponding 
to the set of x’s. We could go further and compute several different regression curves corresponding to the
various percentage points of the distributions and thus get a **more complete picture** of the set. Ordinarily 
this is not done, and so regression often gives a **rather incomplete picture**. Just as the **mean** gives an 
incomplete picture of a single distribution, so the regression curve gives a corresponding incomplete picture 
for a set of distributions.

看到了嘛，这是对我们传统的线性回归的不满啊。 不满在什么地方呢？不满在传统的线性回归就像mean一样不能描述一个分布的所有的全面的
特征，就像我们知道，方差和均值是描述一个分布的两个不同的特征数，其他常用的特征数还包括中位数，分位数，矩，众数等。那么我们传统
的线性回归怎么就和均值扯上关系了呢？请看Quantile Regression的提出者Roger的原话：

Quantiles seem inseparably linked to the operations of **ordering** and **sorting**
the sample observations that are usually used to define them. So it comes as a mild
surprise to observe that we can define the quantiles through a simple alternative
expedient as an **optimization problem**. Just as we can define the **sample mean** as the
solution to the problem of **minimizing a sum of squared residuals**, we can define
**the median** as the solution to the problem of **minimizing a sum of absolute
residuals**. The symmetry of the piecewise linear absolute value function implies that
the minimization of the sum of absolute residuals must equate the number of
positive and negative residuals, thus assuring that there are the same number of
observations above and below the median

上面的话就很厉害了，也是人类思维的智慧光芒闪耀的时刻。我们注意到Roger从均值和最小平方误差的对应关系，以及中位数和绝对值误差的
对应关系。自然推演出其他的分位数是否也可以通过一个优化的问题得到定义的想法。谈到优化的问题，损失函数的定义是非常重要的， 而我们的
分位数就是通过中位数的损失函数的略微变形形成的。在原著的论文中被称作tilted absolute value function,大约长成这样：

![tilted absolute](/wwjwhen_blog/images/tilted.png)

而我们的普通的绝对值函数长得是这样的：

![absolute](/wwjwhen_blog/images/absolute.png)

由此来看，解决如下的问题的最优解（其中 $$ y_i $$ 是样本）：

$$  \min \sum \rho_\tau(y_i - \xi) $$

就变成了求解样本的 $$ \tau $$ 分位数。 就如同下面问题的结果将导致样本的均值一样

$$ \min \sum (y_i - \mu)^2 $$

但是这里面所求的只是非条件化的随机变量 Y 的中位数，在这个过程中我们并不能看到回归的成分。那么我们从普通线性回归开始看：

$$ \min \sum (y_i - \mu(x_i, \beta)) $$

显然，这是一个给出 X 求解 Y 的条件均值（conditional mean）问题。（因为这个优化结果将会解出Y的样本均值，所以我们可以这样说).
于是再一次利用类比的思想，我们可以把 conditional quantile 定义为如下的优化问题的解：

$$ \min \sum \rho_\tau(y_i - \xi(x_i, \beta)) $$

真的只是微小的变形，就有了不同的结果，可见损失函数还真是大有可为啊。其实吧也是因为中位数本来就是分位数的一种，0.5的分位数就是普通
绝对值和函数的最优化结果。值得一提的是，我们的分位数损失函数的最优值的求解方法可以只用线性规划的单纯型方法求解。 相应的函数包目前我用的是
matlab的[quantreg.m](http://cn.mathworks.com/matlabcentral/fileexchange/32115-quantreg-x-y-tau-order-nboot-).但是注意一点,
这里的基函数用的是多项式，我们可以把响应多项式的基函数改成我们需要的基函数，比如RBF,或者是其他的自定义的函数，本人在此次任务中用的就是从
Weibull分布演化而来的一个自定义的函数。

~~~~
function [p,stats]=quantreg(x,y,tau,order,Nboot)
~~~~
{: .language-matlab}

上面是matlab形式的函数，用于多项式拟合分位数回归，我们完全可以不用多项式，而采用自己的函数。

~~~~
rho=@(r)sum(abs(r-(r<0).*r/tau));  % tilted 函数的巧妙定义，也是整个函数中最传神的一笔，
% 要注意在这里算的 rho 和论文中的相比差了个 tau

options = optimset('MaxFunEvals',1000);

p = fminsearch(@(v)rho(y - f(x, v)), x0, options); % fminsearch函数用于自动寻求优化解， 
% 但要注意的是初始值x0的设置要尽可能合理，否则会在优化过程中出错。
~~~~
{: .language-matlab}

总结：
如果用我的话来总结的话，我感觉Quantile Regression的亮点在于两个类比，一个是分位数定义方式的类比，一个是基于这种定义方式的回归思想的类比。
也就是说分位数回归其实是另外一种**回归的方式**。不同于我们的传统印象，但是绝对是从传统中来的。 传统的回归方式会得到一套参数 $$\beta$$ , 而我们的分位数
回归也会得到一套参数 $$\beta$$，不管哪一套参数，都是为了使新预测的值尽可能减少损失函数的大小。可见通损失函数我们得到了另外一种**回归的方式**。

*references*

[Quantile Regresssion](http://www.econ.uiuc.edu/~roger/research/rq/QRJEP.pdf)