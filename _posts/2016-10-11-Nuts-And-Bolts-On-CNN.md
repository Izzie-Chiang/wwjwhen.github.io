---
layout: post
title: Nuts and Bolts on CNN
---

<p class="meta">11 October 2016 - Beijing</p>

In order to explore an innovative architecture of neural networks, I have to rescan the details of CNN(**Convolutional Neural Networks**).
Previously, I worked on the details of CNN, getting familliar with the terminologies. Yet I'm still amazed to see that there remain so many
nuts and bolts I have never noticed, rather than  comprehend the motivation or essence of the CNN. So here I would like to put down some notes
about my comprehension of the details that I have retrieved.

*Convolution*

I have to concede that convolution is such a subtle mathematical method used for engineering. To my knowledge before, it was first utilized to calculate the reaction functions of a linear time-invariant systems. As a result, the first time I heared of "CONVOLUTIONAL" neural networks, I was somewhat at a loss.
I comed clear to me that the convolution operation of CNN was in form of first mutiplication and then addition, thus the convolution operation
in CNN context only corresponded to mathematical definition not to physical meaning, showing as follows(*K means kernel, I means input*):

$$ S(i, j) = (I * K)(i, j) = \sum_n\sum_mI(i - n, j - m)K(n, m) $$

However, in CNN, the substraction was converted to addition as well, showing as follows:

$$ S(i, j) = (I * K)(i, j) = \sum_n\sum_mI(i + n, j + m)K(n, m) $$

Due to the slight change in the formula, the operation is not strictly "*convolution*", but correlation.

*Sparse Connection and Weight Sharing*
The activation of sparse connection takes advantage of Bionics and neuroscience. It elumates structures among neros in brains and eyes. And the
design gains simplification in computation and capacity in generalizing. Compared with fully-connected models in primitive stages of nerual networks,
spare connection forms hierarchy feature representations of an entity.


*pooling*
pooling helps to make the representation become approximately **invariant to small translations** of the input. Invariance to translation means 
that if we translate the input by a small amount, the values of most of the pooled outputs do not change. **Invariance to
local translation can be a very useful property if we care more about whether some feature is present than exactly where it is.**

The picture shows that though all the points in the former layer have changed, only one half of the points in the upper layer change their values.


![PCA](/wwjwhen.github.io/images/maxpool.png)

Another perspective of convolution and pooling is the **Bayesian view**. The two operations can be treated as **infinitely strong prior**(beliefs on 
the model before we see any data) of the model. This infinitely strong prior says that the weights for one hidden unit must be identical to the weights
of its neighbor, but shifted in space. The prior also says that the weights must be zero, except for in the small, spatially contiguous receptive field
assigned to that hidden unit. Overall, we can think of the use of convolution as introducing an infinitely strong prior probability distribution over 
the parameters of a layer. This prior says that the function the layer should learn contains only local interactions and is equivariant to translation.
Likewise, the use of pooling is an infinitely strong prior that each unit should be invariant to small translations.

Due to the infinitely strong prior, if the data does not rationally fit our strong belief, the error rate will rise. For example, if a task relies on 
**preserving precise spatial information**, then using pooling on all features can increase the training error. When a task involves incorporating 
**information from very distant locations** in the input, then the prior imposed by convolution may be inappropriate.
