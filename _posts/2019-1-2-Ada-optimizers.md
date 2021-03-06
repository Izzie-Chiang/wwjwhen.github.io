---
layout: post
title: Ada optimizers for Deep Learning
---

<p class="meta">2 January 2019 - Beijing</p>

今天主要记录的是ada相关算法在非凸优化中的一些套路和想法。

如果要一个一个点名的话，ada算法其实还是不少的，从tensorflow或者pytorch的一堆的优化器中就可以看出来，包括AdaGrad, RMSProp, AdaDelta, Adam等算法，前面列出名字的说明还比较经典或者常用。首先要说的是他们全部都属于梯度下降法的改进与衍生，之所以叫他们ada是因为他们可以自适应地改变步长，而改变步长的依据是历史的梯度信息。

其中AdaGrad主要是对梯度值的平方和进行累加，但是只是简单地累加，导致最后累加和太大，步长非常小，这算是一个缺点了。

RMSProp改进之处就在于利用了指数衰减对历史信息进行加权累加，使得历史信息的占比有所降低，一定程度上避免训练过程单调递减的情况。

AdaDelta根据历史信息对梯度大小进行了调整。

Adam则是依照梯度平方指数衰减累加值进行步长的调整，根据梯度累加值更新模型。具体的可以参看论文的公式，至于他们的部分实现可以参看下面的代码（该代码是theano版本的实现，主要来源于当时OCR项目，参考链接为https://github.com/JianshuZhang/WAP/blob/master/DenseNet/optimizers.py）：

~~~~
import theano
import theano.tensor as tensor

import numpy

profile = False

def itemlist(tparams):
    return [vv for kk, vv in tparams.iteritems()]

def itemlist_name(tparams):
    return [kk for kk, vv in tparams.iteritems()]

"""
General Optimizer Structure: (adadelta, adam, rmsprop, sgd)
Parameters
----------
    lr : theano shared variable
        learning rate, currently only necessaary for sgd
    tparams : OrderedDict()
        dictionary of shared variables {name: variable}
    grads : 
        dictionary of gradients
    inputs :
        inputs required to compute gradients
    cost : 
        objective of optimization
    hard_attn_up :
        additional updates required for hard attention mechanism learning 
Returns
-------
    f_grad_shared : compute cost, update optimizer shared variables
    f_update : update parameters
"""
# optimizers
# name(hyperp, tparams, grads, inputs (list), cost) = f_grad_shared, f_update
def adam(lr, tparams, bn_tparams, opt_ret, grads, inp, cost):
    gshared = [theano.shared(p.get_value() * 0.,
                             name='%s_grad' % k)
               for k, p in tparams.iteritems()]
    rule = []
    for idx in bn_tparams:
        rule += [(bn_tparams[idx], opt_ret[idx])]
    gsup = [(gs, g) for gs, g in zip(gshared, grads)]
    f_grad_shared = theano.function(inp, cost, updates=rule+gsup, profile=profile)

    #lr0 = 0.0002
    lr0 = lr
    b1 = 0.1
    b2 = 0.001
    e = 1e-8

    updates = []

    i = theano.shared(numpy.float32(0.))
    i_t = i + 1.
    fix1 = 1. - b1**(i_t)
    fix2 = 1. - b2**(i_t)
    lr_t = lr0 * (tensor.sqrt(fix2) / fix1)

    for p, g in zip(tparams.values(), gshared):
        m = theano.shared(p.get_value() * 0.)
        v = theano.shared(p.get_value() * 0.)
        m_t = (b1 * g) + ((1. - b1) * m)
        v_t = (b2 * tensor.sqr(g)) + ((1. - b2) * v)
        g_t = m_t / (tensor.sqrt(v_t) + e)
        p_t = p - (lr_t * g_t)
        updates.append((m, m_t))
        updates.append((v, v_t))
        updates.append((p, p_t))
    updates.append((i, i_t))

    f_update = theano.function([lr], [], updates=updates,
                               on_unused_input='ignore', profile=profile)

    return f_grad_shared, f_update

def sgd_momentum(lr, tparams, bn_tparams, opt_ret, grads, inp, cost):
    gshared = [theano.shared(p.get_value() * numpy.float32(0.), name='%s_grad' % k)
               for k, p in tparams.iteritems()]
    prev_update = [theano.shared(p.get_value() * numpy.float32(0.), name='%s_update' % k)
               for k, p in tparams.iteritems()]
    rule = []
    for idx in bn_tparams:
        rule += [(bn_tparams[idx], opt_ret[idx])]
    gsup = [(gs, g) for gs, g in zip(gshared, grads)]
    f_grad_shared = theano.function(inp, cost, updates=rule+gsup, profile=False)      # calculate the model

    # Save the update_delta[i] (this time) 
    pup1 = [(delta, 0.9*delta - lr * g) for delta, g in zip(prev_update, gshared)] # |0.9*delta| why the result can't converge
    pup2 = [(p, p + delta) for p, delta in zip(itemlist(tparams), prev_update)]
    f_update = theano.function([lr], [], updates=pup1+pup2, profile=False)
    return f_grad_shared, f_update

def adadelta(lr, tparams, bn_tparams, opt_ret, grads, inp, cost):
    zipped_grads = [theano.shared(p.get_value() * numpy.float32(0.),
                                  name='%s_grad' % k)
                    for k, p in tparams.iteritems()]
    running_up2 = [theano.shared(p.get_value() * numpy.float32(0.),
                                 name='%s_rup2' % k)
                   for k, p in tparams.iteritems()]
    running_grads2 = [theano.shared(p.get_value() * numpy.float32(0.),
                                    name='%s_rgrad2' % k)
                      for k, p in tparams.iteritems()]

    rule = []
    for idx in bn_tparams:
        rule += [(bn_tparams[idx], opt_ret[idx])]

    zgup = [(zg, g) for zg, g in zip(zipped_grads, grads)]
    rg2up = [(rg2, 0.95 * rg2 + 0.05 * (g ** 2))
             for rg2, g in zip(running_grads2, grads)]

    f_grad_shared = theano.function(inp, cost, updates=rule+zgup+rg2up,
                                    profile=profile)

    updir = [-tensor.sqrt(ru2 + lr) / tensor.sqrt(rg2 + lr) * zg
             for zg, ru2, rg2 in zip(zipped_grads, running_up2,
                                     running_grads2)]
    ru2up = [(ru2, 0.95 * ru2 + 0.05 * (ud ** 2))
             for ru2, ud in zip(running_up2, updir)]
    param_up = [(p, p + ud) for p, ud in zip(itemlist(tparams), updir)]

    f_update = theano.function([lr], [], updates=ru2up+param_up,
                               on_unused_input='ignore', profile=profile)

    return f_grad_shared, f_update

def rmsprop(lr, tparams, grads, inp, cost):
    zipped_grads = [theano.shared(p.get_value() * numpy.float32(0.),
                                  name='%s_grad' % k)
                    for k, p in tparams.iteritems()]
    running_grads = [theano.shared(p.get_value() * numpy.float32(0.),
                                   name='%s_rgrad' % k)
                     for k, p in tparams.iteritems()]
    running_grads2 = [theano.shared(p.get_value() * numpy.float32(0.),
                                    name='%s_rgrad2' % k)
                      for k, p in tparams.iteritems()]

    zgup = [(zg, g) for zg, g in zip(zipped_grads, grads)]
    rgup = [(rg, 0.95 * rg + 0.05 * g) for rg, g in zip(running_grads, grads)]
    rg2up = [(rg2, 0.95 * rg2 + 0.05 * (g ** 2))
             for rg2, g in zip(running_grads2, grads)]

    f_grad_shared = theano.function(inp, cost, updates=zgup+rgup+rg2up,
                                    profile=profile)

    updir = [theano.shared(p.get_value() * numpy.float32(0.),
                           name='%s_updir' % k)
             for k, p in tparams.iteritems()]
    updir_new = [(ud, 0.9 * ud - 1e-4 * zg / tensor.sqrt(rg2 - rg ** 2 + 1e-4))
                 for ud, zg, rg, rg2 in zip(updir, zipped_grads, running_grads,
                                            running_grads2)]
    param_up = [(p, p + udn[1])
                for p, udn in zip(itemlist(tparams), updir_new)]
    f_update = theano.function([lr], [], updates=updir_new+param_up,
                               on_unused_input='ignore', profile=profile)

    return f_grad_shared, f_update


def sgd(lr, tparams, grads, x, mask, y, cost):
    gshared = [theano.shared(p.get_value() * 0.,
                             name='%s_grad' % k)
               for k, p in tparams.iteritems()]
    gsup = [(gs, g) for gs, g in zip(gshared, grads)]

    f_grad_shared = theano.function([x, mask, y], cost, updates=gsup,
                                    profile=profile)

    pup = [(p, p - lr * g) for p, g in zip(itemlist(tparams), gshared)]
    f_update = theano.function([lr], [], updates=pup, profile=profile)

    return f_grad_shared, f_update

~~~~
{: .language-python}