---
layout: post
title: Some Classical Projects on Object Tracking with VS 2013
---

<p class="meta">30 June 2017 - Beijing</p>

最近在看物体追踪的相关知识，是为了解决医疗影像中的主动脉追踪的问题，属于这个领域的起步阶段，所以从经典的方法开始调研起来，
果然，Object Tracking领域被引用最多的文章是Ming-Hsuan Yang的。于是手动跑了几个工程，看看效果，虽然在我们的医疗数据集上
效果不是很好，但还是十分有趣的。也是许久没有用VS写东西了，配置个工程用了一天，后来发现bug出在对exe文件的使用方法不对，也是醉了，
早在cmd里面试一下，可能就没有那么多的问题了。。。

[SemiBoostTracker](http://www.vision.ee.ethz.ch/boostingTrackers/download.htm)


[Fragtrack](http://www.cs.technion.ac.il/~amita/fragtrack/fragtrack.htm)


VS的工程还是那几个大块：*C/C++目录下的include位置， 连接器下的常规的lib库位置和输入*
只要跑出了exe文件就是好的，然后就是看看实在cmd里面调用还是直接调用！！

