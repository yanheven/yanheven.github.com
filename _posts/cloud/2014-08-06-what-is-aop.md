---
layout: post
title: what is "AOP"
category: cloud
description: brief introduction of "AOP"

---

Author:[Hyphen](http://weibo.com/344736086)

#### What is AOP?

今天又接触到一个新名词--“AOP”，当我在电话听到这个词时，一头雾水，因为在学校里面学过OOA,OOD,当然还有OOP,就是没听过AOP.

AOP:Aspect Oriented Programming,用中文表达就是：面向切面编程。

在做开发设计时，你有你的核心关注点，就是你要实现的主要功能，好比某项数据操作，这些具体的功能是相对独立的，但在实现这些核心功能的同时，你又想增加一些额外的操作，比如对操作写日志，这些额外的操作就是横切关注点。一开始我们会简单把这些操作直接加到我们的核心关注点代码上，但这样一来就有很多重复而臃肿的代码结构，我们可以把这些横切关注点统一出来处理。大概这就是面向横切编程。

下面的资料介绍得不错：  
1、[JBoss官方文档关于AOP介绍](http://docs.jboss.org/jbossaop/docs/2.0.0.GA/docs/aspect-framework/userguide/en/html/what.html)  	
2、[张逸：AOP技术基础](http://www.cnblogs.com/wayfarer/articles/241024.html)