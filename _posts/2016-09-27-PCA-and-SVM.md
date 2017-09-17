---
layout: post
title: PCA and SVM
---

<p class="meta">27 Semptember 2016 - Beijing</p>

Today we would like to introduce some concepts about **PCA** and **SVM**, which correspond to 
**"Continuous Latent Variables"** and **"Sparse Kernel Method"** respectively in [Pattern Recognition And Machine Learning](https://book.douban.com/subject/2061116/).

There are two commonly used definitions of PCA that give rise to the same algorithm. PCA
 can be defined as the **orthogonal projection** of the data onto a lowerdimensional 
 linear space, known as the principal subspace, such that the **variance** ofthe projected
data is **maximized** *(Hotelling, 1933)*. Equivalently, it can be defined as the linear 
projection that **minimizes** the average projection cost, defined as the mean **squared
 distance** between the data points and their projections *(Pearson, 1901)*.

That is to say we have encountered with two contrary aspects to the definition of PCA, max and min.
To maximize the variance is equavelent to minimize the mean-square distance between the original data 
and its projection. Intuitively, if a set of two-dimension points (with x dimension and y dimension) lay 
in a straight line in Cartesian coordinate, maybe we can identify all the points within just a one-dimension
axis. Thus, the two-dimension indication of the points is redundant, we can demenstrate them in one rather
than two dimensions, which is called dimension reduction. The idea can be discribed in a way of **matrix
decomposition**, in which the **eigen values** indicate the degree of dispersion and **eigen vectors** indicate the directions
of the projection.
PCA can be applied to **moderate or relatively small** data sets, otherwise the computing complexity will explode.

![PCA](/wwjwhen_blog/images/pca.png)

SVM, as it was originally come up with, is mainly used as a method of **classifying**(*maximum margin classifier*). 
It has been popular for a long time for solving problems in classification, regression, and novelty detection. An 
important property of support vector machines is that the determination of the model parameters corresponds to a 
**convex optimization** problem, and so any local solution is also a global optimum. The convinence in optimazation is
one of the reasons that why SVM has been widely welcomed in both industry and labs.

![svm](/wwjwhen_blog/images/svm.png)

*references*

1. [Bishop. Pattern Recognition and Machine Learning (Information Science and Statistics)[M]. Springer-Verlag New York, Inc. 2006.](http://www.springer.com/gb/book/9780387310732)