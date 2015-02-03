---
layout: post
title: openstack ceilometer plugins
category: cloud
description: openstack ceilometer plugins

---

Author:[Hyphen](http://weibo.com/344736086)http://weibo.com/344736086

本文是阅读官方介绍文档的个人笔记：
####自己写agent plugin
本文档提供了帮助，让你可以自己写现在ceilometer还没有实现的agent plugin.

####Agents：代理
polling agent可以运行在中心管理节点或者计算节点（直接通过hypervisor来poll虚机相关信息）
在计算节点的agent,各个资源指标数据都和resource ID,tenant ID,user ID标记一起，通过消息队列发给collector.
在中心管理节点的abent,收集除了计算节点上收集的信息以外的资源信息，主要是通过openstack各个服务的API来查询。
polling abent在ceilometer/agent/manager.py实现，插件定义在ceilometer.poll.agent命名空间，然后定期调用
get_samples()方法。

####Plugins：插件

