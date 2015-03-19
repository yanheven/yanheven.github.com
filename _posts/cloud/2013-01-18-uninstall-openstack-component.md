---
layout: post
title: openstack 删除某个组件
category: cloud
description: 2013-01-18

---

Author:[海峰 http://weibo.com/344736086](http://weibo.com/344736086)


####删除glance
	apt-get remove -y glance glance-api glance-client glance-common glance-registry python-glance
	dpkg -l |grep glance|awk '{print $2}'|xargs dpkg -P
	mysql -uroot -p$MYSQL_PASSWD -e "DROP DATABASE IF EXISTS glance;"

####删除nova
	apt-get remove -y nova-api nova-cert nova-common nova-compute nova-compute-kvm nova-doc nova-network nova-objectstore nova-scheduler  nova-volume python-nova python-novaclient  nova-consoleauth python-novnc novnc
	dpkg -l |grep nova|awk '{print $2}'|xargs dpkg -P
	mysql -uroot -pmygreatsecret -e "DROP DATABASE IF EXISTS nova;"

#### 删除dashboard
	apt-get remove -y libapache2-mod-wsgi openstack-dashboard
	dpkg -l |grep libapache2-mod-wsgi|awk '{print $2}'|xargs dpkg -P

####删除mysql数据库
	sudo apt-get autoremove --purge mysql-server
	sudo apt-get remove mysql-server
	sudo apt-get autoremove mysql-server
	sudo apt-get remove mysql-common //这个很重要
	dpkg -l |grep mysql|awk '{print $2}'|xargs dpkg -P


####删除keystone
	apt-get remove -y keystone python-keystone python-keystoneclient
	dpkg -l |grep keystone|awk '{print $2}'|xargs dpkg -P
	mysql -uroot -p$MYSQL_PASSWD -e "DROP DATABASE IF EXISTS keystone;"
