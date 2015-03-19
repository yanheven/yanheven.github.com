---
layout: post
title: openstack 常用命令
category: cloud
description: 2013-12-11

---

Author:[海峰 http://weibo.com/344736086](http://weibo.com/344736086)


#### . Nova
I.1. Management

Services status:

    $ sudo nova-manage service list

Enable/disable a service:

    $ sudo nova-manage service enable|disable --host=host --service=nova-compute

Add a new network:

    $ sudo nova-manage network create --label vlan1 --fixed_range_v4 10.0.1.0/24 --num_networks 1 --network_size 256 --vlan 1

Remove a network, first disassociate it to a project:

    $ sudo nova-manage project scrub projectname
$ sudo nova-manage network delete [cidr]

List networks:

    $ sudo nova-manage network list

Add a floating IPs address range:

    $ sudo nova-manage floating create --pool [my-pool] --ip_range 172.17.1.32/27

Add a floating to your tenant (you will get a floating IP address, but unused):

    $ nova floating-ip-create [my-pool]

Associate IP to an instance (specific tenant, according to your credentials)

    $ nova add-floating-ip [my-instance] [ip]

Check the status of the floating IPs (tenant related):

    $ nova floating-ip-list

List all instances running on every compute node:

    $ sudo nova-manage vm list | column -t


I.2. Common

Add a new security group:

    $ nova secgroup-create web-server "Web server running"

Add rule to this group:

    $ nova secgroup-add-rule web-server tcp 80 80 0.0.0.0/0

Add a security rules, allow ping and ssh:

    $ nova secgroup-add-rule web-server icmp -1 -1 0.0.0.0/0
$ nova secgroup-add-rule web-server tcp 22 22 0.0.0.0/0

Create credential:

    $ nova keypair-add my_key > mey_key.pem
$ chmod 600 *.pem

List instances from the tenant in your credential

    $ nova list

Boot a new instance:

    $ nova boot --flavor [flavor-id] --image [image-id] --key_name [key1] --security_groups [default] [instance-name]

Delete an instance:

    $ nova delete [INSTANCE_ID]

Take a snapshot from an instance but first commit the buffer cache to disk:

my-instance:~$ sync
my-instance:~$ sudo echo 3 | sudo tee /proc/sys/vm/drop_caches
$ nova image-create [instance-id] [snapshot-name]

Get precise information about a specific instance:

    $ nova show [instance-name]

Perform a block_migration:

    $ nova live-migration --block_migrate [INSTANCE_ID] [TARGET_SERVER]

#### II. Glance

Add an image to glance (public):

    $ glance add name="my-image" is_public=True|False disk_format=qcow2 container_format=ovf architecture=x86_64 < my-image.img

Check the glance backend:

    $ glance index

Same with nova-common:

    $ nova image-list

Set an image to public:

    $ glance update [image-id] is_public=true

#### III. Keystone

List all the tenants:

    $ keystone tenant-list

List all users:

    $ keystone user-list

Create a new user:

    $ keystone user-create --name [username] --tenant_id [tenant-id] --pass [password] --email [email] --enabled true 
