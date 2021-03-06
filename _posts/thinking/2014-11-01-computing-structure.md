---
layout: post	
title: 	云计算平台下的计算机架构漫想  
category: thinking  
description: 2014-11-01

---

Author:[海峰 http://weibo.com/344736086](http://weibo.com/344736086)

#### 起源
这几天在想公司云平台的计算与存储架构,现在计算与存储基本都是放在不同的地方,那这些资源之间通信都是走的基本网络通信协议,这些在多台普通计算机之间通信来说并不奇怪,但在一个云计算平台的数据中心来说,我就开始怀疑这种架构的效率.

#### 思路
在一个数据中心里面,如果大量的机器之间有大量数据流,然后形成一个整体来对外服务,其实就是我们现在说的云计算平台.站在平台之外来看,其实我们可以把一个云平台看成是一台很大很大的计算机.然后在这个数据中心里面,我的存储节点和计算节点之间的数据通道走的还是跟普通机器的一个完整的网络协议,这样让我觉得有点浪费资源.为什么我们这样一个对外面来说相对封闭的一个集群里面的机器也要跟外面其他机器一样走完这套网络协议流程呢?为什么不可以按我们一台普通个人电脑的架构一样,里面各组件数据流走总线这种模式呢?其实想到这里我又想起了我司之间另外一个团队分享的技术,就是我们数据中心,其中的数据通信,走的是经过优化的网络协议,他们声称可以将速度提升百分之十十左右(好像是这个数,记不太清楚了).

#### 分析
之前跟他们说时没太在意,以为他们有点吹的成分,现在想想还是有一定的道理的.很多时候,我们走的流程都是一种通用的流程,因为要关注所有使用者,所以可能有浪费的情况.举一个很简单的例子,手机长号11位,手机短号6位,在这里就不关注价格了,就关注结构.如果我们只在一个比较小的圈子通信,用6位的足够了,但假如在这种情况下,你用11位的一样可以,这样你可以跟更多的人通信.我觉得同事做的网络协议的优化就跟这个6位短号一样,我不需要跟全国人通信,我为什么一定要用11位号码,我用6位效率更高.

#### 类比
通过上面的分析,那这个例子也可以用到我们的云计算数据中心中来.如果我的不同节点之间数据流是不需要与全球互联网通信,它的终极目的就是只在内部通信(这里指集群内部的数据流,当然集群与外部通信有相应的出口,这种就不在现在讨论范围之内),为什么也要按互联网的普通机器的协议来使用呢,何不精简些呢,比较理想的是我们现在的PC机内部组件的这种总线通信.即使是做不到这种级别,但至少可以做得比现在按标准网络协议这套要高效吧.

#### 发散
有时我们都会局限在当前的状况里面,很多思考都被当前的情形所限制,我们现在很多人,包括我,在写文章前,都会在一味想怎样设计架构,怎么组织我的不同功能的节点,让它们运转得更好,但却很少有时间,也很少有这种童真去思考,可不可以不按照现在的框架来做呢,真正革它的命呢
以上想法纯粹是漫想,胡思乱想还是要有的,万一实现了呢?