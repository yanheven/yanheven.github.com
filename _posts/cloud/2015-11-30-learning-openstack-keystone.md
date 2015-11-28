---
layout: post
title: Learning OpenStack Keystone
category: cloud
description: 2015-11-30

---
Author: 

- [海峰 http://weibo.com/344736086](http://weibo.com/344736086)   
- [http://yanheven.github.io/ ](http://yanheven.github.io/ )
- [http://blog.csdn.net/yanheven1](http://blog.csdn.net/yanheven1/)

这周重新学习整理了OpenStack Keystone里面的知识, 成果就是这个思维导图了.主要通过阅读IBM几个哥们出的 "Identity, Authentication & Access Management in Openstack".
Keystone在OpenStack架构里面有如下几个功能作用: 

- Identity: 身份区分, 比如区分你, 我, 他是不同的人. 信息可以存在Keystone SQL DB 或者来自LDAP, MS AD.
- Authentication: 身份验证, 确定以你名义过来的请求是你, 主要是用户密码匹配验证, 这个同样可以存在Keystone SQL DB 或者通过LDAP, MS AD, 甚至第三方Google, Facebook来做. 
- Access Management: 权限管理, 即你有哪些权限可以做什么, 通过指定你在某个范围(Scope)里面拥有的角色(Role), 加上各个服务自己的策略(Policy)来判断你有哪些操作的权限.

之前发现看过的东西没做笔记,很快就没了,虽然画画思维导图也记录不了多少东西,但至少画的时候是有自我整理了一下,总比不画好.不过这个思维导图对读者来说可能没有很大的作用,也只能知道大概有哪些东西,通过什么来实现,但完全没有细节,所以想详细了解还是多看看书,文档吧.  
喜欢的同学可以”在新窗口打开图片链接”或”保存图片到本地”来查看清晰内容.

![Keystone](http://img.blog.csdn.net/20151122121849449)