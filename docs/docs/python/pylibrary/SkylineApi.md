# 前言

:100:

相信很多朋友都和我一样，对openstack原生的dashboard（horizon）性能低下而无奈，今天我为大家推荐一款可以用来替代horizon的社区产品skyline-apiserver。

# 什么是 Skyline

Skyline 是一个经过 UI 和 UE 优化过的 OpenStack 仪表盘，拥有现代化的技术栈和生态，更易于开发者维护和使用者操作，以及更高的并发性能。。


# 安装和使用

## 容器安装

> 详见官方指导文档:
> https://opendev.org/skyline/skyline-apiserver/src/branch/master/README-zh_CN.md

### 前置

由于skyline的部署是使用docker容器，所以我们要预先安装好Docker容器环境，配置好镜像源，才可以开始后续的工作。

### 安装

1. 拉取官方镜像

```
docker pull docker.io/99cloud/skyline
```

2. 创建并修改配置文件


创建配置文件
```
touch /etc/skyline/skyline.yaml
```

将以下内容填入skyline.yaml

```
default:
  access_token_expire: 3600
  access_token_renew: 1800
  cors_allow_origins: []
  # 数据库链接地址（注意:这里的路径指容器内的路径，host的路径为/tmp/skyline/skyline.db）
  database_url: sqlite:////tmp/skyline.db
  debug: true
  log_dir: ./log
  secret_key: aCtmgbcUqYUy_HNVg5BDXCaeJgJQzHJXwqbXr0Nmb2o
  session_name: session
developer:
  show_raw_sql: false
openstack:
  base_domains:
  - system_user_domain
  base_roles:
  - keystone_system_admin
  - keystone_system_reader
  - keystone_project_admin
  - keystone_project_member
  - keystone_project_reader
  - nova_system_admin
  - nova_system_reader
  - nova_project_admin
  - nova_project_member
  - nova_project_reader
  - cinder_system_admin
  - cinder_system_reader
  - cinder_project_admin
  - cinder_project_member
  - cinder_project_reader
  - glance_system_admin
  - glance_system_reader
  - glance_project_admin
  - glance_project_member
  - glance_project_reader
  - neutron_system_admin
  - neutron_system_reader
  - neutron_project_admin
  - neutron_project_member
  - neutron_project_reader
  - heat_system_admin
  - heat_system_reader
  - heat_project_admin
  - heat_project_member
  - heat_project_reader
  - placement_system_admin
  - placement_system_reader
  - panko_system_admin
  - panko_system_reader
  - panko_project_admin
  - panko_project_member
  - panko_project_reader
  - ironic_system_admin
  - ironic_system_reader
  - octavia_system_admin
  - octavia_system_reader
  - octavia_project_admin
  - octavia_project_member
  - octavia_project_reader
  default_region: RegionOne
  default_domain: Default
  extension_mapping:
    fwaas_v2: neutron_firewall
    vpnaas: neutron_vpn
  interface_type: public
  keystone_url: https://{openstack_ip}:5000/v3/
  nginx_prefix: /api/openstack
  reclaim_instance_interval: 604800
  service_mapping:
    baremetal: ironic
    compute: nova
    identity: keystone
    image: glance
    load-balancer: octavia
    network: neutron
    orchestration: heat
    placement: placement
    volumev3: cinder
  system_admin_roles:
  - admin
  - system_admin
  system_project: admin
  system_project_domain: Default
  system_reader_roles:
  - system_reader
  system_user_domain: Default
  system_user_name: {admin_user}
  system_user_password: {admin_passwd}
setting:
  base_settings:
  - flavor_families
  - gpu_models
  - usb_models
  flavor_families:
  - architecture: x86_architecture
    categories:
    - name: general_purpose
      properties: []
    - name: compute_optimized
      properties: []
    - name: memory_optimized
      properties: []
    - name: high_clock_speed
      properties: []
  - architecture: heterogeneous_computing
    categories:
    - name: compute_optimized_type_with_gpu
      properties: []
    - name: visualization_compute_optimized_type_with_gpu
      properties: []
  gpu_models:
  - nvidia_t4
  usb_models:
  - usb_c
```

3. 初始化

```
rm -rf /tmp/skyline && mkdir /tmp/skyline

docker run -d --name skyline_bootstrap -e KOLLA_BOOTSTRAP="" -v /etc/skyline/skyline.yaml:/etc/skyline/skyline.yaml -v /tmp/skyline:/tmp --net=host 99cloud/skyline:latest
```

> 使用命令：`docker logs skyline_bootstrap` 检查初始化结果，如果退出码为 `exit 0`，则证明初始化成功。

4. 开始启动skyline

```
# 删除初始化的容器，后续不需要它了
docker rm -f skyline_bootstrap

docker run -d --name skyline --restart=always -v /etc/skyline/skyline.yaml:/etc/skyline/skyline.yaml -v /tmp/skyline:/tmp --net=host 99cloud/skyline:latest
```

5. 检查部署结果

访问：`https://{容器host的IP}:8080`，能够访问skyline web界面则证明成功。

## Q&A

### Q1：T版本以下的openstack对接后，在skyline登录时选择不到domain！

> 方案：修改容器内：/skyline/libs/skyline-apiserver/skyline_apiserver/types/constants.py 文件中的 KEYSTONE_API_VERSION 为 3 即可。

### Q2：对接endpoint为https的openstack时，https告警导致无法启动skyline！

> 方案：向/root/skyline/skyline-apiserver/.venv/lib/python3.8/site-packages/keystoneauth1/下的session.py中添加https告警忽略即可。

```python
...省略...
from keystoneauth1 import exceptions

# 即如下两行
from requests.packages.urllib3.exceptions import InsecureRequestWarning

requests.packages.urllib3.disable_warnings(InsecureRequestWarning)

try:
...省略...
```

### Q3：登录skyline的账号密码是什么？

> 方案：即对接使用的账号密码

### Q4：登录skyline后，进行资源操作，提示api版本不正确！

> 方案：需要修改skyline-console的源码后重新编译打包才行。

```
# 需要修改的文件
/src/client/client/constants.js

# 修改如上文件中的api版本号即可
```