---
layout: post
title: Something on Python OOP
---

<p class="meta">22 July 2017 - Beijing</p>

用了python很久，但是对于Python里面最精华的面向对象却没有很深的认识，参照着网站的学习，想要总结一下相应的知识点。

**1**、和普通的函数相比，在类中定义的函数只有一点不同，就是第一个参数永远是实例变量**self**，并且，调用时，不用传递该参数。
除此之外，类的方法和普通函数没有什么区别，所以，你仍然可以用默认参数、可变参数、关键字参数和命名关键字参数。

**2**、如果要让内部属性不被外部访问，可以把属性的名称前加上两个下划线__，在Python中，实例的变量名如果以__开头，
就变成了一个**私有变量**（private），只有内部可以访问，外部不能访问。

**3**、需要注意的是，在Python中，变量名类似__xxx__的，也就是以双下划线开头，并且以双下划线结尾的，是特殊变量，
特殊变量是可以直接访问的，不是private变量，所以，不能用__name__、__score__这样的变量名。

**4**、有些时候，你会看到以一个下划线开头的实例变量名，比如_name，这样的实例变量外部是可以访问的，但是，按照约定俗成的规定，
当你看到这样的变量时，意思就是，“虽然我可以被访问，但是，请把我视为私有变量，不要随意访问”。

**5**、双下划线开头的实例变量是不是一定不能从外部访问呢？其实也不是。不能直接访问__name是因为Python解释器对外把**__name**变量改成了**_Student__name**，
所以，仍然可以通过_Student__name来访问__name变量，但是不同版本的解释器会对私有变量做不同的命名，这里面只是一种命名方式。

**6**、不要试图外部修改私有变量 

~~~~
instance.__private = 'new value'
~~~~
{: .language-python}

这样做的后果将是添加一个新的类属性，而不是将私有变量进行外部修改，是达不到目的的，需要利用set方法才行。

**7**、动态语言的“**鸭子类型**”--它并不要求严格的继承体系，一个对象只要“看起来像鸭子，走起路来像鸭子”，那它就可以被看做是鸭子。 
Python的“file-like object“就是一种鸭子类型。对真正的文件对象，它有一个read()方法，返回其内容。但是，许多对象，只要有read()方法，
都被视为“file-like object“。许多函数接收的参数就是“file-like object“，你不一定要传入真正的文件对象，完全可以传入任何实现了read()方法的对象。

**8**、总结来看就是说相较于Java，c++这样的静态语言而言，Python这样的动态语言的**多态不一定要以继承作为基础**，只要有相应的方法就可以，而不一定要有血缘关系。

**9**、给**实例绑定属性**的方法是通过实例变量，或者通过self变量，但是，如果Student**类本身需要绑定一个属性**呢？可以直接在class中定义属性，这种属性是类属性，归Student类所有，
当我们定义了一个类属性后，这个属性虽然归类所有，但类的所有实例都可以访问到，相当于静态语言里面的static