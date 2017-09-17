---
layout: post
title: Sampling Methods
---

<p class="meta">29 December 2016 - Beijing</p>

来到了抽样方法的专题，这里面首先还是要明白基本概念和基本方法。首先很多人对抽样会有一定的认识，顾名思义，所谓抽样就是从总体里**抽**出部分的**样**本。
但是很多人对抽样的认识可能只到这里了。从根本目标上来讲：我们要用抽样的方法估计总体的分布，也就是说抽样不但要抽出样本，而且还要让样本能够代表整体的分布情况，
也就是说要在**概率值大的区域抽出更多的样本，在概率值小的区域抽出较少的样本**，或者反过来讲，在我们的抽样方法之下，样本多的区域代表高的概率，少的代表小的概率
这是非常重要的一个认识，不是说只是从分布中取出来就算完事的。尤其是在高维变量空间的情况下， 如何使得**样本的分布符合总体的概率分布**这是一件 non-trival 的事。

*1、 利用均匀分布抽样其他分布：*

这里面只要记住两点，第一：我们的**源分布**($$z$$)是均匀分布，第二：**目标分布**($$y$$)的**累积分布函数的逆函数**作用在源分布的随机变量上就可以得到目标分布了。
即实现：

$$ y = h^{-1}(z) $$

其中$$h(.)$$是目标分布的累积分布函数

这里面非常著名的一个例子就是Box-Muller通过二维均匀分布抽样二维高斯分布的[例子](https://en.wikipedia.org/wiki/Box%E2%80%93Muller_transform)。

*2、 Rejection Sampling*

Rejection sampling主要用于对付比较复杂的单变量分布，对多变量不是很方便，但是也是很重要的一类方法。

$$p(z) = \frac{1}{Z_p}\widetilde{p}(z)$$

我们容易获得$$\widetilde{p}(z)$$， 但是不容易求得normalization的积分$$Z_p$$, 据此，我们可以选用一个抽样比较容易的分布$$q(z)$$，比如上述Box-Muller抽出的正态分布.
通过两步操作，首先从$$q(z)$$中得到抽样$$z_0$$，计算$$kq(z_0)$$，其中k是能使 $$kq(z) \geq  \widetilde{p}(z)$$的常数值。然后在$$[0, kq(z_0)]$$的区间上均匀抽样出$$u_0$$,
如果$$u_0 \leq  \widetilde{p}(z_0)$$则接受这个抽样$$z_0$$否则拒绝，我们需要注意的是通过$$u_0$$来决定是否接受$$z_0$$的方式使得抽得的样本可以很好地描述出总体的概率分布，
可以理解为我们是**依概率**来决定是否接受$$z_0$$，而这种间接的方式就能使得我们从$$q(z)$$的抽样得到$$\widetilde{p}(z)$$的抽样，又因为$$Z_p$$只是常数，所以我们的抽样就可以表征
$$p(z)$$的分布。

![rejection](/wwjwhen_blog/images/rejection.png)

很显然，在高维空间下不仅有envelope函数难以寻找的缺点，还有因为维度上升引起的接受率骤减的情况，由此来看rejection sampling确实不适用于多变量的抽样。

*3、 Importance Sampling*

在这个抽样的过程中我们没有拒绝，但是要对保留的样本依照重要程度进行保留。也就是说要进行加权处理。要注意importance sampling的的定位**是一种间接近似期望的手段，
不是从对应的概率分布中采样**。 要注意，从目的分布中抽样一定都是困难的，否则无需研究这么多，所以要从容易采样的源分布中采样，将样本通过一定的手段转化成目的分布
的样本。这个手段就是要研究的算法。

*4、 Sampling-Importance-Resampling*

顾名思义，经历三个步骤，从源分布中抽样L个样本，计算 importance sampling 过程中的weights,根据weights, 依概率地从L个样本中重新抽样L个。然后得到的样本就可以大致逼近
目的分布了。 在样本量大的时候效果尤其显著。

*5、Sampling from Markov Chain*

马尔科夫链是非常重要的一类随机过程，首先言简意赅地来讲随机过程就是一个二元函数，$$X(e, t)$$,这个二元函数的$$e$$取值自随机实验的结果的集合，同时$$t$$取值自参数的集合，可以看作是
时间。 也就是说固定$$e$$我们可以得到一个样本随时间变化的函数，固定$$t$$可以得到某一时刻样本在状态空间里依概率取值的各种情况。于是根据两个变量的取值的连续和离散我们可以得到各种随机过程，

马尔科夫链是无记忆的随机过程。但是满足可抽样的马尔科夫链不能只有无记忆这么简单，我们的每一次抽样都是要让抽样的结果更好地表征样本的分布，我们需要待抽样的分布随着状态转移函数的作用不改变其原来的分布。用专业术语来讲叫做'distribution invariant'。 我们需要注意的是一个马尔科夫链可以用一个状态转移函数来表征，而待抽样的分布需要在这个马尔科夫链下保持分布的不变。这样我们就可以通
过若干次的抽样使得样本能表征真实的概率分布。

比较著名的算法有Metropolis-Hasting Algorithm 以及 Gibbs Sampling。

*6、Gibbs Sampling*

这是非常重要的一种抽样的方法，应用比较广。

![gibbs1](/wwjwhen_blog/images/gibbs1.png)

从算法上来看这是一个非常straightforward的算法，但是就像我们之前对MC抽样算法的认识，他是需要满足distribuion invariant的。当我们在采样的时候条件条件部分的联合概率分布是不变的，
同时采样得到的条件概率也是原分布的，于是新的变量和旧的变量的联合分布都是源分布的，所以分布是不变的。

同时其实Gibbs Sampling是Metropolis-Hastings算法的一种特例，这里面接受样本的概率是1，总是会接受的（详细的证明可以参见Pattern Recognition and Machine Learning 的544页的证明，在这里我们不再详细展开），因为上面的解释我们知道，每一步得到的新样本都是源分布的，自然是没有理由拒绝的。因此Gibbs抽样一般会有很好的效果。

但是当待抽样分布的变量间的相关性比较强的时候Gibbs方法的效果就会有所降低，下图是二元高斯分布的Gibbs抽样结果，（a）表示的两个变量间相关性比较弱，（b）表示的是两个变量之间的相关性比较强

![gibbs](/wwjwhen_blog/images/gibbs.png)

*references*

1. [Murphy K P. Machine Learning: A Probabilistic Perspective[M]. MIT Press, 2012.](http://dl.acm.org/citation.cfm?id=2380985&preflayout=flat)
2. [Bishop. Pattern Recognition and Machine Learning (Information Science and Statistics)[M]. Springer-Verlag New York, Inc. 2006.](http://www.springer.com/gb/book/9780387310732)
3. [Barber D. Bayesian Reasoning and Machine Learning[J]. Cambridge University Press, 2012.](https://www.amazon.com/Bayesian-Reasoning-Machine-Learning-Barber/dp/0521518148)