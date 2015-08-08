---
layout: post
title: write neturon ml2 mechanism driver
category: coding
description: 2015-08-08

---

Author:[海峰 http://weibo.com/344736086](http://weibo.com/344736086)

###1. 基本概念:
	core feature: network, subnet, port
	plugins feature: loadbalance, firewall, vpn, etc.
	ML2 core plugin: type driver,  mechanism driver.
	ML2 type driver: vlan, vxlan, gre,  etc.
	ML2 mechanism driver: linux bridge, openvSwitch, etc.
	
###2. 环境准备:
	devstack 开发环境搭建, local.conf 参考:
	
```
[[local|localrc]]
FORCE=yes
ADMIN_PASSWORD=password
DATABASE_PASSWORD=$ADMIN_PASSWORD
RABBIT_PASSWORD=$ADMIN_PASSWORD
SERVICE_PASSWORD=$ADMIN_PASSWORD
SERVICE_TOKEN=$ADMIN_PASSWORD
HOST_IP=192.168.56.102

LIBVIRT_TYPE=qemu
VIRT_DRIVER=libvirt
MULTI_HOST=False

DEST=/opt/stack
LOGFILE=$DEST/logs/stack.sh.log
SCREEN_LOGDIR=$DEST/logs/screen
LOG_COLOR=False
RECLONE=no
VERBOSE=False

disable_service n-net
disable_service tempest
disable_service cinder c-sch c-api c-vol
disable_service heat h-api h-api-cfn h-api-cw h-eng

enable_service q-agt
enable_service q-dhcp
enable_service q-l3
enable_service q-meta
enable_service q-svc

# Neutron related config
Q_PLUGIN=ml2
Q_AGENT=openvswitch
Q_ML2_PLUGIN_MECHANISM_DRIVERS=openvswitch,cookbook

# VLAN Related Config
ENABLE_TENANT_VLANS=TRUE
TENANT_VLAN_RANGE=1000:1100
PHYSICAL_NETWORK=physnet1
FLAT_INTERFACE=eth0
OVS_PHYSICAL_BRIDGE=br-eth0
Q_ML2_TENANT_NETWORK_TYPE=vlan
```

###3. 创建简单的ML2 Mechanism driver, 名字叫"cookbook":
####3.1. 在devstack安装目录下的neutron目录下:
/opt/stack/neutron/neutron/plugins/ml2/drivers
创建文件 ml2_mech_driver.py 如下:
```
# Import Neutron Database API
from neutron.db import api as db
try:
    from neutron.openstack.common import log as logger
except ImportError:
    from oslo_log import log as logger
from neutron.plugins.ml2 import driver_api as api

driver_logger = logger.getLogger(__name__)


class CookbookMechanismDriver(api.MechanismDriver):

    def initialize(self):
        driver_logger.info("Inside Mech Driver Initialize")
```

####3.2.  配置neutron server 使用上面这个ML2 mechanism driver,
编辑文件: 	/etc/neutron/plugins/ml2/ml2_conf.ini
```
[ml2]
tenant_network_types = vlan
type_drivers = local,flat,vlan,gre,vxlan
mechanism_drivers = openvswitch,cookbook
```
编辑入口配置文件: /opt/stack/neutron/neutron.egg-info/entry_points.txt
在 [neutron.ml2.mechanism_drivers] 配置部分, 增加一行指定cookbook 入口:
```
[neutron.ml2.mechanism_drivers]
...
neutron.plugins.ml2.drivers.ml2_mech_driver.CookbookMechanismDriver
```
重启neutron server, 从日志 /opt/stack/logs/q-svc.log 中可以看到我们的改动.

###4.  完善cookbook mechanism driver, 增加网络处理模块:
增加文件 /opt/stack/neutron/neutron/plugins/ml2/drivers/ml2_mech_driver_network.py 如下:
```
try:
    from neutron.openstack.common import log as logger
except ImportError:
    from oslo_log import log as logger
from neutron.plugins.ml2 import driver_api as api

driver_logger = logger.getLogger(__name__)


class CookbookNetworkMechanismDriver(api.MechanismDriver):

    def _log_network_information(self, method_name, current_context, prev_context):
        driver_logger.info("**** %s ****" % (method_name))
	# Print the Network Name using the context
        driver_logger.info("Current Network Name: %s" % (current_context['name']))
	# For create operation prev_context will be None.
        if prev_context is not None:
            driver_logger.info("Previous Network Name: %s" % (prev_context['name']))
	# Print the Network Type
        driver_logger.info("Current Network Type: %s" % current_context['provider:network_type'])
        driver_logger.info("**** %s ****" % (method_name))

    def create_network_postcommit(self, context):
	# Extract the current and the previous network context
        current_network_context = context.current
        previous_network_context = context.original
	self._log_network_information("Create Network PostCommit", current_network_context, previous_network_context)

    def update_network_postcommit(self, context):
	# Extract the current and the previous network context
        current_network_context = context.current
        previous_network_context = context.original
	self._log_network_information("Update Network PostCommit", current_network_context, previous_network_context)
```
	
编辑/opt/stack/neutron/neutron/plugins/ml2/drivers/ml2_mech_driver.py 如下:

```
# Import Neutron Database API
from neutron.db import api as db
try:
    from neutron.openstack.common import log as logger
except ImportError:
    from oslo_log import log as logger
from neutron.plugins.ml2 import driver_api as api
import ml2_mech_driver_network as cookbook_network_driver

driver_logger = logger.getLogger(__name__)


class CookbookMechanismDriver(api.MechanismDriver, ml2_mech_driver_network.CookbookNetworkMechanismDriver):

    def initialize(self):
        driver_logger.info("Inside Mech Driver Initialize")
```
	
重启neutron 服务, 创建网络: 
```
$neutron net-create CookbookNetwork1
```
可以从日志 /opt/stack/log/q-svc.log 看到打印出来的网络信息.

###5.  完善cookbook mechanism driver, 增加子网处理模块:
增加文件 /opt/stack/neutron/neutron/plugins/ml2/drivers/ml2_mech_driver_subnet.py 如下:
```
# Import Neutron Database API
from neutron.db import api as db
try:
    from neutron.openstack.common import log as logger
except ImportError:
    from oslo_log import log as logger
from neutron.plugins.ml2 import driver_api as api

# Import ML2 Database API
from neutron.plugins.ml2 import db as ml2_db


driver_logger = logger.getLogger(__name__)


class CookbookSubnetMechanismDriver(api.MechanismDriver):

    def _log_subnet_information(self, method_name, current_context, prev_context):
        driver_logger.info("**** %s ****" % (method_name))
        driver_logger.info("Current Subnet Name: %s" % (current_context['name']))
        driver_logger.info("Current Subnet CIDR: %s" % (current_context['cidr']))
        # Extract the Network ID from the Subnet Context
        network_id = current_context['network_id']
        # Get the Neutron DB Session Handle
        session = db.get_session()
        # Using ML2 DB API, fetch the Network that matches the Network ID
        networks = ml2_db.get_network_segments(session, network_id)
        driver_logger.info("Network associated to the Subnet: %s" % (networks))
        driver_logger.info("**** %s ****" % (method_name))

    def create_subnet_postcommit(self, context):
        # Extract the current and the previous Subnet context
        current_subnet_context = context.current
        previous_subnet_context = context.original
        self._log_subnet_information("Create Subnet PostCommit", current_subnet_context, previous_subnet_context)
```

编辑/opt/stack/neutron/neutron/plugins/ml2/drivers/ml2_mech_driver.py 如下:
```
# Import Neutron Database API
from neutron.db import api as db
try:
    from neutron.openstack.common import log as logger
except ImportError:
    from oslo_log import log as logger
from neutron.plugins.ml2 import driver_api as api
import ml2_mech_driver_network as cookbook_network_driver
import ml2_mech_driver_subnet as cookbook_subnet_driver


driver_logger = logger.getLogger(__name__)


class CookbookMechanismDriver(api.MechanismDriver, ml2_mech_driver_network.CookbookNetworkMechanismDriver, cookbook_subnet_driver.CookbookSubnetMechanismDriver):

    def initialize(self):
        driver_logger.info("Inside Mech Driver Initialize")
```
重启neutron 服务, 创建子网: 
```
$eutron subnet-create --name CookbookSubnet2 CookbookNetwork2 10.0.0.0/24
```
可以从日志 /opt/stack/log/q-svc.log 看到打印出来的网络信息.

###6.  完善cookbook mechanism driver, 增加网络接口port处理模块:
增加文件 /opt/stack/neutron/neutron/plugins/ml2/drivers/ml2_mech_driver_port.py 如下:
```

try:
    from neutron.openstack.common import log as logger
except ImportError:
    from oslo_log import log as logger
from neutron.plugins.ml2 import driver_api as api

driver_logger = logger.getLogger(__name__)


class CookbookPortMechanismDriver(api.MechanismDriver):

    def _log_port_information(self, method_name, context):
        driver_logger.info("**** %s ****" % (method_name))
        # Extract the current Port context
        current_port_context = context.current
        # Extract the associated Network Context
        network_context = context.network
        driver_logger.info("Port Type: %s" % (current_port_context['device_owner']))
        driver_logger.info("IP Address of the Port: %s" % ((current_port_context['fixed_ips'][0])['ip_address']))
        driver_logger.info("Network name for the Port: %s" % (network_context.current['name']))
        driver_logger.info("Network type for the Port: %s" % (network_context.current['provider:network_type']))
        driver_logger.info("Segmentation ID for the Port: %s" % (network_context.current['provider:segmentation_id']))
        driver_logger.info("**** %s ****" % (method_name))

    def create_port_postcommit(self, context):
        self._log_port_information("Create Port PostCommit", context)
```

编辑/opt/stack/neutron/neutron/plugins/ml2/drivers/ml2_mech_driver.py 如下:
```
# Import Neutron Database API
from neutron.db import api as db
try:
    from neutron.openstack.common import log as logger
except ImportError:
    from oslo_log import log as logger
from neutron.plugins.ml2 import driver_api as api
import ml2_mech_driver_network as cookbook_network_driver
import ml2_mech_driver_port as cookbook_port_driver
import ml2_mech_driver_subnet as cookbook_subnet_driver


driver_logger = logger.getLogger(__name__)


class CookbookMechanismDriver(api.MechanismDriver, ml2_mech_driver_network.CookbookNetworkMechanismDriver, cookbook_subnet_driver.CookbookSubnetMechanismDriver, cookbook_port_driver.CookbookPortMechanismDriver):

    def initialize(self):
        driver_logger.info("Inside Mech Driver Initialize")
```
重启neutron 服务, 创建一个路由, 然后连接一个子网到路由, 就会触发创建port的方法: 
```
$neutron router-create CookbookRouter
$neutron router-interface-add CookbookRouter CookbookSubnet2
```
可以从日志 /opt/stack/log/q-svc.log 看到打印出来的网络信息. 可以看到port type 是 network:router_interface.
