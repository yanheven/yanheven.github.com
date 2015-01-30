---
layout: post
title: openstack ceilometer overview  
category: cloud
description: openstack ceilometer overview  

---

Author:[Hyphen](http://weibo.com/344736086)http://weibo.com/344736086

本文是阅读官方介绍文档的个人笔记：
###系统架构
####目标
主要目标是计量，其中提到在openstack中，多个项目都有计量的需求，所以ceilometer不只是我们平时所说的资源计量而去计量，
而是它把openstack所有项目中的计量工作接收了--“multi-publisher”.其中还有heat作自动拓展，也需要ceilometer的alarm功能来配合，


####计量
在实现顺序上：
计量：最原始的资源使用数据
计费：使用计量信息来算价格了
帐单：最终与用户算钱的步骤了

而ceilometer的设计尽可能停留在第一步，计量上，因为后面两步不同使用者，差异会很大，会导致项目过于庞大。
关于db,不建议直接访问它存放计量数据的DB，尽可能通过API，还有一点是计量数据在DB的保存时间一般是几个月，如果用来做计费数据，
应该再想办法保存这些数据，不然用户过一两年后，发现自己消费记录有问题，那你不保存这些原始数据，你就说不清了。

####架构
直接上图好了：
![架构图](http://docs.openstack.org/developer/ceilometer/_images/ceilo-arch.png)

共五个核心服务，可以根据自己的负载水平拓展布署

![数据收集流程](http://docs.openstack.org/developer/ceilometer/_images/1-agents.png)

ceilometer收集数据的三种方式：
消息总线：使用OSLO库的项目，ceilometer-notification agent使用这种方法
推送代理：在某个项目内加多个推送数据的功能，不推荐，会使项目变大变复杂
轮询代理：通过定时去调用各个服务的API去轮询相应数据，最不推荐，

后两种方法被ceilometer-polling agent使用

![获取收集到的数据](http://docs.openstack.org/developer/ceilometer/_images/2-accessmodel.png)



