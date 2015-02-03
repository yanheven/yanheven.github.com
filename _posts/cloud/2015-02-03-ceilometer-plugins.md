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
一个polling agent可以支持多个插件去获取不同类型的信息然后发送以collector。如果没有特殊指定，一个agent会
自动激活在本agent的所有插件。   

计算节点：ceilometer.poll.compute命名空间定义
管理节点：ceilometer.poll.central 命名空间    
  
1.怎样增加一个外部系统的Plugin,比如计算节点的相关代码：ceilometer/compute/pollsters,里面的cpu.CPUPollster模块
2.怎样增加一个通过现在的openstack的消息队列中的event notification来做的plugin,ceilometer/compute/notifications
  里面的instance.InstanceNotifications 模块
  
####Pollster查询者
所有的Pollster必须实现基于 ceilometer.compute.BaseComputePollster 的一个方法：get_samples(self, manager, context)，
定义在这里：ceilometer/compute/pollsters/__init__.py
这个方法返回一个Sample对像的列表，定义在这里：ceilometer/sample.py

在CPUPollster插件里面，get_samples 是一个循环，查询当前物理机上的所有实例的CPU使用情况。
“cpu”：cumulative类型，CPU使用时间，累计
"cpu_util"：gauge类型，某个时间点上的cpu使用占比，一个百分比。
这里的LOG方法只是记录信息来调试使用，跟计量活动无关。
你也可以通过命令行参数来启动polling agent,可以只指定pollster命名空间，或者具体的pollster列表，或者两者都使用。    
1.namespace:
  ceilometer-polling –polling-namespaces central compute
2.pollster-list:
  ceilometer-polling –pollster-list image image.size storage.*
  
如果两个参数都使用的话，则要两个参数都匹配的才启动。Agents coordination 不能和pollster-list同时使用，避免样sample重复。

####Notifications：通知
所有notification都要继承于ceilometer.plugin.NotificationBase，位于 ceilometer/plugin.py（icehouse中才有），
而且必须实现以下方法：    
event_types ：这个插件的事件类型列表
process_notification(self, message)：根据event_types定义，按Sample对象格式，获取事件消息列表返回。

在InstanceNotifications 插件里，它监听下面三种事件：    
  compute.instance.create.end
  compute.instance.exists
  compute.instance.delete.start
  
使用get_event_type 方法的话，每次相应的事件发生时，process_notification 方法接着也会被调用来产生相应的Sample对象来发给
collector.

####Test:测试
任何新的plugin,agent，都要经过unit tests.agent plugins在这里tests/compute ,插件在这里：test/agent.提交之前尽可能保证不会影响到其他项目。

#####[原文在这里](http://docs.openstack.org/developer/ceilometer/contributing/plugins.html)


