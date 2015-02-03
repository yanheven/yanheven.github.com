---
layout: post
title: openstack ceilometer overview  
category: cloud
description: 2015-01-30

---

Author:[Hyphen](http://weibo.com/344736086)http://weibo.com/344736086

本文是阅读官方介绍文档的个人笔记：
###系统架构
####目标
主要目标是计量，其中提到在openstack中，多个项目都有计量的需求，所以ceilometer不只是我们平时所说的资源计量而去计量，
而是它把openstack所有项目中的计量工作接收了--“multi-publisher”.其中还有heat作自动拓展，也需要ceilometer的alarm功能来配合，


####计量Metering
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

#####在组件模块来看，有如下几个模块：   

######ceilometer-api  供外部调用使用ceilometer  
######ceilometer-agent-central  
  通过RESTful APIs来查询：    
    OpenStack Networking    
    OpenStack Object Storage    
    OpenStack Block Storage   
    Hardware resources via SNMP   
    Energy consumption metrics via Kwapi framework    
    
######ceilometer-agent-compute  运行在计算节点上，查询实例的性能信息，相关消息。通过AMQP消息队列发送这些数据给collector.  
######ceilometer-agent-notification 通过AMQP消息队列获取其他openstack服务的事件通知（nitification）.  
######ceilometer-agent-ipmi 通过IPMI接口，查询计算节点的物理信息。  
######ceilometer-collector  获取各种通过AMQP消息队列发送过来的消息，然后存储到DB中。    
######ceilometer-alarm-evaluator  警报触发评估，通过在一段时间内结合统计数据的趋势和之前设置的阀值来决定是否报警。    
######ceilometer-alarm-notifier   报警后的响应动作设置。    

 #####Telemetry middleware  
 ceilometer甚至还可以用来记录每个服务的API接受请求和响应次数，相应服务只需要暴露http.request和http.response   
 类型的事件notification.    

![数据收集流程](http://docs.openstack.org/developer/ceilometer/_images/1-agents.png)

ceilometer收集数据的三种方式：  
消息队列notification：使用OSLO库的项目，ceilometer-notification agent使用这种方法 
推送代理agent-compute：在某个项目内加多个推送数据的功能，不推荐，会使项目变大变复杂  
轮询代理Restful API：通过定时去调用各个服务的API去轮询相应 数据，最不推荐，
后两种方法被ceilometer-polling agent使用




![获取收集到的数据](http://docs.openstack.org/developer/ceilometer/_images/2-accessmodel.png)
用户在调用ceilometer相应api来获取数据时，可以不被openstack里面的tenant,user等概念限制 
用户还可以收集数据来为上层应用，比如PAAS，SAAS等使用  
用户可以通过RESTFUL API发送数据到ceilometer数据库保存     

    ID of the corresponding resource. (--resource-id)   
    Name of meter. (--meter-name)
  Type of meter. (--meter-type)

    Predefined meter types:

        Gauge

        Delta

        Cumulative

    Unit of meter. (--meter-unit)

    Volume of sample. (--sample-volume)


![多种发布功能](http://docs.openstack.org/developer/ceilometer/_images/3-Pipeline.png)
发布计量数据给不同的使用场景，主要有两个关注点：频率和发布方式  
“multi-publishe”现在可以为不同的使用场景，通过不同的频率和不同的方式去发布数据
现在实现的3种发布方式： 
notifier  
RPC 
UDP 


![cpu例子](http://docs.openstack.org/developer/ceilometer/_images/4-Transformer.png)
聚集多个CPU sample为一个CPU 比例sample


![sample send](http://docs.openstack.org/developer/ceilometer/_images/5-multi-publish.png)
sample 通过多种方式发送到不同的目的地


####警报Alarming
可以设置单个警报，也可以结合多个警报组合来做一个警报，普通用户只能对自己的资源进行设置警报
警报可以触发多种形式的动作，但现在只实现了两种：  
HTTP callback 
log 


####数据库选择
![database choose](http://docs.openstack.org/developer/ceilometer/_images/6-storagemodel.png)
ceilometer 数据存储模型

在Juno & Kilo版本中，ceilometer的数据分为alarm,event,meter,可以将这三种数据分别存储在不同类型的数据中，比如alarm 数据存储在SQL backend数据库中,event和meter存储在NOSQL backend数据库中

###细节描述
####插件Plugins
ceilometer 现在使用 Stevedore,用户可以自己增加需要的插件，通过设置来启用还是禁用插件。






