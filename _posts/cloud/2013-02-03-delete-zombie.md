---
layout: post
title: openstack folsom删除僵尸实例
category: cloud
description: 2013-02-03

---

Author:[Hyphen](http://weibo.com/344736086)


	#!/bin/bash
	for i in "$@"
	do
	mysql -uroot -pmygreatsecret << EOF
	use nova;
	DELETE FROM nova.virtual_interfaces where 	instance_uuid='$i';
	DELETE FROM nova.fixed_ips where instance_uuid='$i';
	DELETE FROM nova.block_device_mapping where instance_uuid='$i';
	DELETE FROM nova.instance_system_metadata where instance_uuid='$i';
	DELETE FROM nova.security_group_instance_association where instance_id='$i';
	DELETE FROM nova.instance_info_caches WHERE instance_uuid='$i';
	DELETE FROM nova.instances WHERE uuid='$i';
	EOF
	done
	#echo "ok!,$# vm was deleted successfully!!"
	#exit 0

运行脚本时后面加参数 ，参数为你要删除的实例的id，例如：
22f8d28d-d6cd-461e-911f-627e6cb8d4fe

在实例所在的计算结点上删除/var/lib/nova/instance/instance00000[这个是要删除的实例的号]