---
layout: post
title: Level Set Method for Main Aortic Segmentation
---

<p class="meta">17 Augest 2017 - Beijing</p>

嗯，搞了挺久的水平集方法终于有所进展了，需要感慨一下一代一代改进水平集方法的人，
都是利用数学方法表达语义的大触。不把这其中的演变路线讲出来真是对不起那么多的智慧闪耀。

**参数动态活动曲线**

这是水平集的前世，属于将曲线参数化进行表达，之后利用参数的演化控制曲线的演化。二维平面上的曲线表达如下：

$$C(s, t) = (x(s,t),y(s,t))$$

因为是动态曲线，所以除了曲线参数$$s$$之外还有对时间变量$$t$$的依赖。
将这个公式利用偏微分方程进行表达后就可以得到对曲线进行迭代的方程，数值上利用的是偏微分，但是实现上就是差分方程，有了差分方程迭代就不成问题了。给出的差分方程如下：
 $$\frac{\partial C(s,t)}{\partial t} = FN$$

 $$N \rightarrow N(s, t) = (\frac{-y'(s,t)}{\sqrt{ y'(s,t)^{2} + x'(s,t)^{2}}}, \frac{x'(s,t)}{\sqrt{y'(s,t)^{2} + x'(s,t)^{2}}})$$

 注意这里面的$$N$$是和梯度方向垂直的方向，上述的偏微分方程描述的是某一点的速度，但是我们注意到这一点的速度并不完全，而是分解之后的速度，完整的速度还需要包括切向的速度，即完整的偏微分方程要有如下的形式：

 $$\frac{\partial C(s,t)}{\partial t} = \alpha T+\beta N$$

 $$T \rightarrow T(s,t) = (\frac{x'(s,t)}{\sqrt{ x'(s,t)^{2} + y'(s,t)^{2} } }, \frac{ y'(s,t)}{\sqrt{x'(s,t)^{2} + y'(s,t)^{2}} })$$

但是我们不关心他的切向速度，主要是因为切向速度决定的是曲线的位移而不是曲线自身形状的变化，即表达的不是曲线的收缩或扩张，因此我们将切方向的速度忽略，这个是一开始理解这个公式的一个比较大的障碍，不知道为什么忽然出现这样的表达式，这其间省略掉不少的思想，一时间比较难以想到。

我们使用不同的$$F$$，会得到不同的曲线演化的效果：

![k and curvature](/wwjwhen.github.io/images/kandc.png)

如果我们想让曲线实现如下的演化：

![c1toc2](/wwjwhen.github.io/images/c12c2.png)

除了上述的参数曲线的演化，一个精妙的思想--水平集方法就横空出世了，具体的思想就是利用高维在低维上的零水平面的投影轮廓表示平面的轮廓，如此一来平面上的演化就变成了高维函数的演化问题，如下图所示：

![l12l2](/wwjwhen.github.io/images/level12level2.png)

**关于水平集函数和水平集曲线的有用的分析结论**

$$C(p)=(x(p), y(p))$$如果曲线是函数$$\phi (x,y)$$零水平集的话，那么其相应的$$N$$和曲率$$\kappa$$计算如下：

$$N=-\frac{\nabla \phi}{\left | \nabla\phi \right |}$$  

$$\kappa =div(\frac{\nabla \phi}{\left | \nabla\phi \right |})$$


$$p(x)$$在曲线上的曲线积分如下:

$$\int _\Re p(x)\delta (\phi)\left | \nabla\phi \right |dx$$

$$p(x)$$在曲线包裹曲面上的曲面积分如下:

$$\int _\Re p(x)H(-\phi)dx$$

于是水平集函数的演化函数就有如下的形式：

$$\frac{\partial \phi}{\partial t} = FN$$

我们希望在$$\phi$$演化的过程中不会太剧烈也不会太缓慢，所以希望整体的梯度模长保持为1，所以一开始的时候利用符号距离函数进行$$\phi$$的初始化：

$$\left\{\begin{matrix}
dist(x,C) & if\ x\ is\ outside\ C\\ 
0 & if\ x\ is\ on\ C\\ 
-dist(x,c) & if\ x\ is\ inside\ C
\end{matrix}\right.$$

表示如下图所示：

![init](/wwjwhen.github.io/images/init.png)

但是这样的演化公式还是会有不小的问题，随着演化过程的继续，水平集函数$$\phi$$不再保持模长为1的良好性质了如下图所示：

![degrade](/wwjwhen.github.io/images/degrade.png)

所以有人提出了重新初始化的概念，重新初始化的公式如下，宗旨就是重新使得水平集函数恢复模长为1的性质：

$$\frac{\partial \psi }{\partial t} = sign(\phi )(1-\left | \nabla \psi \right |)$$

后来李纯明等人利用泛函方法表达我们对水平集函数的语义描述，具体来看整个的公式结构如下：

$$F(\phi) = \mu R(\phi)+ \varepsilon_{ext}(\phi)$$

公式的第一项表达的是对水平集函数的正则化约束，作用是使得水平集函数能够平稳演化，保持梯度模长为1的特征，公式的后一项表达的是促使水平集朝特定方向演化的外部能量，比如图像分割的话就是边缘信息。

分解来看：

$$R_p(\phi) = \int _\Omega p(\left | \nabla \phi \right |)dx$$

$$p = p_1(s) = \frac{1}{2}(s-1)^{2}$$

外部能量稍后再说。

一个重要的事实是：

$$\frac{\partial \phi}{\partial t} = -\frac{\partial F}{\partial \phi}$$

这样，利用泛函就可以进行水平集函数的迭代了

于是就有：

$$\frac{\partial \phi}{\partial t} = -\frac{\partial R_p}{\partial \phi} - \frac{\partial \varepsilon_{ext}}{\partial \phi}$$

$$\frac{\partial R_p}{\partial \phi} = -div(d_p(\left | \nabla\phi \right |)\nabla\phi)$$

$$d_p(s) = \frac{p'(s)}{s}$$

于是就有：

$$\frac{\partial \phi}{\partial t} = \mu div(d_p(\left | \nabla\phi \right |)\nabla\phi) - \frac{\partial \varepsilon_{ext}}{\partial \phi}$$

其中
$$D = \mu d_p(\left | \nabla\phi \right |)$$
可以看做是第一项的散度的扩散系数，当扩散系数大于零的时候作为扩散源，
$$\left | \nabla \phi \right |$$ 
将会逐渐减小，当扩散系数小于零的时候，作为吸收源，
$$\left | \nabla\phi \right |$$
将会逐渐增大。

根据前面的描述，结合下述三个式子：

$$p = p_1(s) = \frac{1}{2}(s-1)^{2}$$

$$d_p(s) = \frac{p'(s)}{s}$$

$$D = \mu d_p(\left | \nabla\phi \right |)$$

得到结论：

$$D = \mu (1 - \frac{1}{\left | \nabla \phi \right |})$$

如此可见，当我们的
$$\left | \nabla \phi \right |$$
大于1的时候，扩散率大于零，于是要向减小的方向进行，从而趋近于1，当
$$\left | \nabla \phi \right |$$ 
小于1的时候，扩散率小于零，要向增大的方向进行，从而趋近于1，所以性质就可以被比较完美地保存了，但是这个公式存在的一个问题是当
$$\left | \nabla \phi \right |$$ 
趋近于0的时候，整个的就变成了负无穷，这样在数值计算的时候就有不稳定的因素存在。

基于此，一个新的约束函数 $$p_2$$ 出现了

$$p_2=\left\{\begin{matrix}
\frac{1}{(2\pi)^{2}}(1-\cos (2\pi s))& if\ s\leq 1\\ 
\frac{1}{2}(s-1)^{2}& if\ s>  1
\end{matrix}\right.$$

该约束函数有一个重要的性质：

$$\left | d_p(s) \right | < 1\ \forall s\in (0,+\infty )$$

于是可以保证：

$$D = \mu\left | d_p(s) \right | < \mu$$

保证了扩散率的有界性，从而使得扩散保持了数值的平稳性。

进一步地，我们可以知道我们的$$d_p(s)$$函数如下图所示：

![newdp](/wwjwhen.github.io/images/newdp.png)

通过函数的曲线，我们可以看到扩散率的正负。

此时结合新的初始化函数：

$$\phi_0(x)=\left\{\begin{matrix}
-c_0 & if\ x\in R_0\\ 
c_0 & else
\end{matrix}\right.$$

这样的初始化函数带来的好处就是我们只需要非常简单地初始化我们的水平集函数，将感兴趣区域的水平集函数符号取反即可，在边缘处由于
$$\left | \nabla \phi \right |$$ 
无穷大所以可以向1收缩，其他地方都保持0不变，稍有变化就会又被迫式变为0，这样就保证了水平集函数的平稳演化。

到此为止，我们介绍了使得水平集函数平稳演化的方法，应用在主动脉分割中就是对外部能量项进行设计，从而使得水平集函数向待分割的区域边缘进行演化。

我们将整体的泛函写作如下的公式：

$$F(\phi) = \mu R_p(\phi) + \lambda L_g(\phi) + \alpha A_g(\phi)$$

其中：

$$L_g(\phi) =\int_\Omega g\delta (\phi)\left | \nabla \phi \right | dx$$

$$ A_g(\phi) = \int _\Omega gH(-\phi)dx$$

$$g = \frac{1}{1 + \left | \nabla G_\delta \ast I\right |}$$

