---
layout: post
title: openstack horizon 开发入门
category: cloud
description: 2014-03-16

---

Author:[Hyphen](http://weibo.com/344736086)


#### 这段时间进行openstack控制台界面的开发有2个多月，感觉学到了不少东西。

## 1、前端

首先是前端的技术，之前只知道简单的html语法，对CSS，JS，JQUERY等一窍不通。年后，小组开发人手不是很够，果断转行去开发，记得刚开始，搞个练手的界面，搞了两天才实现。而且前提是别人写好的结构，拿过来修改使用。熟读唐诗三百首，不会做诗也会吟的境界。但是一旦碰到没有样例的界面，那就头疼了，因为很多标签都是不认识的。但只能问人，搜索用法，记得有几天，写几个界面，搞得有点喘不过气来。但事实证明一句老话，当你感觉到难受的时候，是你学东西最多的时候。因为不懂，所以觉得很难，因为一直坚持，所以特别难受，中途多次想不做前端了，让其他人做，只写后端好了，但还是一直坚持。截止本稿时间，终于学到了一些基本前端，现在做OPensack的控制台界面开发勉强够用。

##  2、后端

开发后端直接使用各个服务的client，因为Horizon就是这么干的。

通过实例化一个通过授权认证的客户端对象，然后可以进行后续的操作

### 2.1、nova

nova操作

	import novaclient.v1_1.client as nvclient
	nova = nvclient.Client(auth_url=auth_dict["--os-auth-url"],\
							username=auth_dict["--os-username"],\
							api_key=auth_dict["--os-password"],\
							project_id=auth_dict["--os-tenant-name"])


### 2.2 keystone

	import keystoneclient.v2_0.client as ksclient                     
	keystone = ksclient.Client(auth_url = auth_dict["--os-auth-url"], \
                                        username = auth_dict["--os-username"], \
                                        password = auth_dict["--os-password"], \
                                        tenant_name = auth_dict["--os-tenant-name"])


### 2.3 neutron
	import keystoneclient.v2_0.client as ksclient
	keystone = ksclient.Client(auth_url = auth_dict["--os-auth-url"], \
                                        username = auth_dict["--os-username"], \
                                        password = auth_dict["--os-password"], \
                                        tenant_name = auth_dict["--os-tenant-name"])
                                        
   
或者这样：
   
	import keystoneclient.v2_0.client as ksclient  
	keystone = ksclient.Client(auth_url = auth_dict["--os-auth-url"], \  
                                        username = auth_dict["--os-username"], \  
                                        password = auth_dict["--os-password"], \  
                                        tenant_name = auth_dict["--os-tenant-name"])  
  
	endpoint_url = keystone.service_catalog.url_for(service_type='network')  
	token = keystone.auth_token  
	neutron2 = neutronclient.Client(endpoint_url=endpoint_url, token=token) 
	neutron = neutronclient.Client(auth_url=auth_dict["--os-auth-url"],
	username=auth_dict["--os-username"],
	password=auth_dict["--os-password"],
	tenant_name=auth_dict["--os-tenant-name"])                                   


	
#### 这段时间进行openstack控制台界面的开发有2个多月，感觉学到了不少东西。

## 1、前端

首先是前端的技术，之前只知道简单的html语法，对CSS，JS，JQUERY等一窍不通。年后，小组开发人手不是很够，果断转行去开发，记得刚开始，搞个练手的界面，搞了两天才实现。而且前提是别人写好的结构，拿过来修改使用。熟读唐诗三百首，不会做诗也会吟的境界。但是一旦碰到没有样例的界面，那就头疼了，因为很多标签都是不认识的。但只能问人，搜索用法，记得有几天，写几个界面，搞得有点喘不过气来。但事实证明一句老话，当你感觉到难受的时候，是你学东西最多的时候。因为不懂，所以觉得很难，因为一直坚持，所以特别难受，中途多次想不做前端了，让其他人做，只写后端好了，但还是一直坚持。截止本稿时间，终于学到了一些基本前端，现在做OPensack的控制台界面开发勉强够用。

##  2、后端

开发后端直接使用各个服务的client，因为Horizon就是这么干的。

通过实例化一个通过授权认证的客户端对象，然后可以进行后续的操作

### 2.1、nova

nova操作

	import novaclient.v1_1.client as nvclient
	nova = nvclient.Client(auth_url=auth_dict["--os-auth-url"],\
							username=auth_dict["--os-username"],\
							api_key=auth_dict["--os-password"],\
							project_id=auth_dict["--os-tenant-name"])


### 2.2 keystone

	import keystoneclient.v2_0.client as ksclient                     
	keystone = ksclient.Client(auth_url = auth_dict["--os-auth-url"], \
                                        username = auth_dict["--os-username"], \
                                        password = auth_dict["--os-password"], \
                                        tenant_name = auth_dict["--os-tenant-name"])


### 2.3 neutron
	import keystoneclient.v2_0.client as ksclient
	keystone = ksclient.Client(auth_url = auth_dict["--os-auth-url"], \
                                        username = auth_dict["--os-username"], \
                                        password = auth_dict["--os-password"], \
                                        tenant_name = auth_dict["--os-tenant-name"])
                                        
   
或者这样：
   
	import keystoneclient.v2_0.client as ksclient  
	keystone = ksclient.Client(auth_url = auth_dict["--os-auth-url"], \  
                                        username = auth_dict["--os-username"], \  
                                        password = auth_dict["--os-password"], \  
                                        tenant_name = auth_dict["--os-tenant-name"])  
  
	endpoint_url = keystone.service_catalog.url_for(service_type='network')  
	token = keystone.auth_token  
	neutron2 = neutronclient.Client(endpoint_url=endpoint_url, token=token) 
	neutron = neutronclient.Client(auth_url=auth_dict["--os-auth-url"],
	username=auth_dict["--os-username"],
	password=auth_dict["--os-password"],
	tenant_name=auth_dict["--os-tenant-name"])                                   


	
 