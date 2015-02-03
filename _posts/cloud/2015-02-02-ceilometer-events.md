---
layout: post
title: openstack ceilometer events
category: cloud
description: 2015-02-02

---

Author:[Hyphen](http://weibo.com/344736086)http://weibo.com/344736086

本文是阅读官方介绍文档的个人笔记：
###Events(事件) vx. Samples（样本）
Samples 是一些具体时间点上的数据值的集合。而Events更多的是状态的变化，而不只只是数值上的。
比如用户创建或者销毁了某些资源。
Samples 可以随意处理，因为没多大问题。而Events经常是比较重要的变化，经常是有用的提示，处理就要谨慎了。

###Event 结构
event_type
message_id
generated
traits

Event信息主要是从openstack的notification系统获取，在openstack的notification系统中，数据的形式可以是多种的，
但经过events获取后，会按照event的结构进行处理，从而方便后面的处理。

从notification转换成events的结构的规定，通过配置文件ceilometer.conf来配置，字段“definitions_cfg_file”。
通过规定不同的类型的事件，怎样从notification映射到events.
如果某个事件类型没有定义，那就按照最少定义来进行转换。
当然，也可以通过设置“drop_unmatched_notifications=True”,来对没定义转换规则的事件类型进行抛弃处理。
如果notification有如下数据，无论是否定义规则，都会自动加到所有类型的事件events结构中：
service
tenant_id
request_id

###定义文件格式
YAML格式，包含事件定义的列表，处理的时候，是按从这个定义文件，从下到上处理，即是说最后定义的最先处理。
从下到上主要是出于这样的考虑：
  “compute.instance”
  "compute.instance.exites"
我们一般写配置文件时，都会将通用的写在前面--“compute.instance”,然后后面才是越来越具体的，所以在匹配时，就当从最具体的
开始配置，所以就从下到上了。
使用YAML格式，可以避免复制traits的定义。

###事件定义Events
事件定义必须包含两个keys
event_type:可以使用”！“来表示排除类型，
traits：一个dict,key是trait 的名字，value是trait的具体定义。

###Trait定义
type:数据类型
fields:field path specification.从notification要拿到的数据域的路径
plugin:又是一个dict
  name:插件名字
  parameters:（可选）插件的关键参数的dict
  
###Field Path Specifications
格式上支持“.”和“[]”,如果某个路径字符串包含“.”，则要加上引用符进行区分，如org.openstack__1__architecture是一个整体：
  payload.image_meta.’org.openstack__1__architecture’
  
###[YAML定义文件样例](http://docs.openstack.org/developer/ceilometer/events.html#example-definitions-file)


还可以通过trait plugins来处理这个转换过程，如分串，格式化等。
原文：[http://docs.openstack.org/developer/ceilometer/events.html](http://docs.openstack.org/developer/ceilometer/events.html)

