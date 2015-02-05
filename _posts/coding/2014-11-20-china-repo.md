---
layout: post
title: use CSDN CODE to pull openstack codes
category: coding
description: 2014-11-20

---

Author:[海峰 http://weibo.com/344736086](http://weibo.com/344736086)

###问题
直接从GITHUB上获取代码，经常是没保障，特别是用DEVSTACK的时候，经常超时，所以我想到了每天把GITHUB上的代码更新到本地的想法。但本地还要搞一套GIT服务环境，而且也只是自己用，为何不使用公开的软件库呢，之前记得OSCHINA和CSDN 都搞GIT，相比之下，使用了CSDN CODE,使用起来就跟GITHUB一样，但有个问题是容量现在默认只给我一个G，昨天把OPENSTACK,OPENSTACK-DEV,STACKFORGE这三个目录下，DEVSTACK安装时要用到的库都同步了下，大半个G。

###解决方案
我就是在我本地一个实例上，为每个软件库建立一个目录，然后PULL GITHUB上的代码，切换远程URL为CSDN CODE我的软件库，PUSH代码。这些都写了个循环的脚本来做，初定每天更新一次，定时。
CSDN CODE我的软件库 [https://code.csdn.net/yanheven1/](https://code.csdn.net/yanheven1/)

####sync_git.sh

    #!/bin/sh -x
    PROJECT=("ceilometer" "cinder" "glance" "heat" "horizon" "ironic" "keystone" "neutron" "nova" "sahara" "swift" "trove" "requirements" "tempest" "tempest-lib" "python-ceilometerclient" "python-cinderclient" "python-glanceclient" "python-heatclient" "python-ironicclient" "python-keystoneclient" "python-neutronclient" "python-novaclient" "python-saharaclient" "python-swiftclient" "python-troveclient" "python-openstackclient" "cliff"  "pycadf" "stevedore" "taskflow" "glance_store" "heat-cfntools" "heat-templates" "django_openstack_auth" "keystonemiddleware" "diskimage-builder" "os-apply-config" "os-collect-config" "os-refresh-config" "tripleo-image-elements" "ironic-python-agent")

    PROJECT_STACKFORGE=("swift3" "wsme" "pecan" "sqlalchemy-migrate")
    PROJECT_OPENSTACK_DEV=("pbr" "devstack")
    PROJECT_OSLO=("oslo.concurrency" "oslo.config" "oslo.context" "oslo.db" "oslo.i18n" "oslo.log" "oslo.messaging" "oslo.middleware" "oslo.rootwrap" "oslo.serialization" "oslo.utils" "oslo.vmware")
    PROJECT_OSLO_NAME=("oslo-concurrency" "oslo-config" "oslo-context" "oslo-db" "oslo-i18n" "oslo-log" "oslo-messaging" "oslo-middleware" "oslo-rootwrap" "oslo-serialization" "oslo-utils" "oslo-vmware")

    for pro in "${PROJECT[@]}";do mkdir /home/$pro;done
    for pro in "${PROJECT_STACKFORGE[@]}";do mkdir /home/$pro;done
    for pro in "${PROJECT_OPENSTACK_DEV[@]}";do mkdir /home/$pro;done
    for pro in "${PROJECT_OSLO[@]}";do mkdir /home/$pro;done


    GIT_OPENSTACK=https://github.com/openstack
    GIT_STACKFORGE=https://github.com/stackforge
    GIT_OPENSTACK_DEV=https://github.com/openstack-dev
    GIT_CSDN=git@code.csdn.net:yanheven1


    for pro in "${PROJECT[@]}"
        do 
            cd /home/$pro
            git init
            git remote add origin $GIT_OPENSTACK/$pro.git
            git pull origin master
            git remote rm origin
    
            git remote add origin $GIT_CSDN/$pro.git
            git push origin master
            git remote rm origin
        done
    
    for pro in "${PROJECT_STACKFORGE[@]}"
        do 
            cd /home/$pro
            git init
            git remote add origin $GIT_STACKFORGE/$pro.git
            git pull origin master
            git remote rm origin
    
            git remote add origin $GIT_CSDN/$pro.git
            git push origin master
            git remote rm origin
        done
    
    for pro in "${PROJECT_OPENSTACK_DEV[@]}"
        do 
            cd /home/$pro
            git init
            git remote add origin $GIT_OPENSTACK_DEV/$pro.git
            git pull origin master
            git remote rm origin
    
            git remote add origin $GIT_CSDN/$pro.git
            git push origin master
            git remote rm origin
        done
    
    for ((i=0;i<12;i++))
        do 
            cd /home/${PROJECT_OSLO[$i]}
            git init
            git remote add origin $GIT_OPENSTACK/${PROJECT_OSLO[$i]}.git
            git pull origin master
            git remote rm origin
    
            git remote add origin $GIT_CSDN/${PROJECT_OSLO_NAME[$i]}.git
            git push origin master
            git remote rm origin
        done
        

然后定时执行：

    0 * * * * sh /home/sync_git.sh

最后修改下DEVSTACK下面的stackrc文件中的软件库URL即可使用：

    sed -i "s/{GIT_BASE}\/openstack\/oslo./{GIT_BASE}\/oslo-/g" /home/stack/devstack/stackrc
    sed -i "s/{GIT_BASE:-git:\/\/git.openstack.org}/{GIT_BASE:-https:\/\/code.csdn.net\/yanheven1}/g" /home/stack/devstack/stackrc
    sed -i "s/{GIT_BASE}\/stackforge/{GIT_BASE}/g" /home/stack/devstack/stackrc
    sed -i "s/{GIT_BASE}\/openstack/{GIT_BASE}/g" /home/stack/devstack/stackrc
    sed -i "s/{GIT_BASE}\/openstack-dev/{GIT_BASE}/g" /home/stack/devstack/stackrc
