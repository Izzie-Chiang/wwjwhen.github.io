---
layout: post
title: Bootstrap and Rank-sum Test
---

<p class="meta">11 December 2016 - Beijing</p>

特征选择是特征工程里面的比较重要的部分，特征选择从大的方面来讲有两种，一种是前向特征选择，从操作方法上来看是从一个特征开始逐个开始累加后续的；
另外一种是后项特征选择，从操作方法上来看是从全局的特征开始逐个删减。我们在这里面主要用前向特征选择。


*Bootstrap*

其实就是一个有放回的抽样，于是抽样结果中会有重复的样本，这个其实在机器学习的bagging方法中应该是非常常用的，在对多个模型的训练集进行构造的时候，
这种抽样方法显得非常重要。

~~~~
import numpy as np
import numpy.random as npr

def bootstrap(data, num_samples, statistic, alpha):
    """

    Returns bootstrap estimate of 100.0*(1-alpha) CI for statistic.
    data: 将要从其中抽样的原始数据，可以是一维的向量，也可以是二位的矩阵
    num_samples：抽样的次数
    statistic:一个函数，方差函数或者均值函数之类的
    alpha:返回 100% - alpha的置信区间

    """

    n = len(data) #原始数据的样本个数
    idx = npr.randint(0, n, (num_samples, n)) # 得到num_samples次对整体样本的“n元素抽样”，
                                              #也可以是 “m元素抽样”，视情况而定
    samples = data[idx]  # 通过index直接从源数据得到相应的抽样数据，这里多亏了numpy的优良性质
    stat = np.sort(statistic(samples, 1))
    return (stat[int((alpha/2.0)*num_samples)],
            stat[int((1-alpha/2.0)*num_samples)])

~~~~
{: .language-python}

*Rank-Sum Test*

假设检验当初在学概率的时候就觉得不知所云，但是假设检验真的是一个非常重要的以概率的方式验证命题真伪的
重要方法，是很体现统计思想的精华所在。

秩和检验是假设检验里的一个比较重要的方法，同时t-test也是一个非常重要的假设检验手段，我们这里面主要介绍下秩和检验，
因为这个在scipy.stats 里面有ranksums的函数，目前只是介绍一下比较重要的衡量指标，其他的具体的原理暂时不介绍了。
秩和检验比较重要的统计量是p值，p值代表的是"做出拒绝命题为真这个决定的犯错的概率"，这个概率越大就越应该接受假设，
一般假设的都是某些统计量存在相等关系，所以我们可以用这个假设检验来验证我们的特征选择的过程中某个特征的增减是否会
引起整体的表示能力的下降，如果表达能力下降则剔除胡，如果表达能力没有变化则说明冗余，如果表达能力上升了则加入该特征。


*refernece*
[bootstrap with python random moudle](http://people.duke.edu/~ccc14/pcfb/analysis.html)