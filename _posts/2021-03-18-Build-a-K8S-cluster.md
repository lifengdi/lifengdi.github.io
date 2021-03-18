---
layout: post
title: 搭建k8s集群
categories: [k8s]
description: 搭建k8s集群
keywords: k8s
topmost: true
---


## 硬件准备



## 初始化配置

### 配置免密钥登录

```bash
[root@localhost ~]# ssh-keygen -b 4096 -f ~/.ssh/id_rsa -N ""
[root@localhost ~]# cat ~/.ssh/id_rsa.pub | tee -a ~/.ssh/authorized_keys
[root@localhost ~]# ssh-copy-id -i ~/.ssh/id_rsa.pub <user>@<node_ip_address>
```



### Host配置

```bash
[root@localhost ~]# vim ~/k8s-ansible/customer/hosts
192.168.30.171 master-test-k8s.kd.cn master-test-k8s
192.168.30.170 node1-test-k8s.kd.cn node1-test-k8s
192.168.30.172 node2-test-k8s.kd.cn node2-test-k8s
192.168.30.173 node3-test-k8s.kd.cn node3-test-k8s
192.168.30.174 node4-test-k8s.kd.cn node4-test-k8s
192.168.30.175 infra1-test-k8s.kd.cn infra1-test-k8s
192.168.30.176 infra2-test-k8s.kd.cn infra2-test-k8s
```



## ansible-hosts

```bash
[root@master ~]# vim ~/k8s-ansible/customer/ansible-hosts
# build节点
[k8s-build]
master-test-k8s

# master节点
[k8s-masters]
master-test-k8s  node_name=master-test-k8s

# node 节点
[k8s-nodes]
node1-test-k8s node_name=node1-test-k8s
node2-test-k8s node_name=node2-test-k8s
node3-test-k8s node_name=node3-test-k8s
node4-test-k8s node_name=node4-test-k8s
infra1-test-k8s node_name=infra1-test-k8s
infra2-test-k8s node_name=infra2-test-k8s

[all:vars]
etcd_ip=192.168.30.171
#etcd_ip=127.0.0.1
service_cluster_ip_range=169.169.0.0/16
master_ip=192.168.30.171
api_server_ip=192.168.30.171
#node_name=k8s-master
dns_server_ip=169.169.0.2
dns_domain=cluster.local
```



- 将hosts复制到/etc/ansible/下`cp ~/k8s-ansible/customer/ansible-hosts /etc/ansible/hosts`
- 通过`ansible all -m ping`测试是否正常

如果出现以下异常：

```bash
[root@master .ssh]# ansible all -m ping
WARNING: yacc table file version is out of date
[DEPRECATION WARNING]: The TRANSFORM_INVALID_GROUP_CHARS settings is set to allow bad characters in group names by default, this will change, but still be user configurable on deprecation. This feature will be removed in version 2.10. 
Deprecation warnings can be disabled by setting deprecation_warnings=False in ansible.cfg.
[WARNING]: Invalid characters were found in group names but not replaced, use -vvvv to see details
[WARNING]: Skipping plugin (/usr/lib/python2.7/site-packages/ansible/plugins/callback/foreman.py) as it seems to be invalid: from_buffer() cannot return the address of the raw string within a str or unicode or bytearray object
[WARNING]: Skipping plugin (/usr/lib/python2.7/site-packages/ansible/plugins/callback/grafana_annotations.py) as it seems to be invalid: from_buffer() cannot return the address of the raw string within a str or unicode or bytearray object
[WARNING]: Skipping plugin (/usr/lib/python2.7/site-packages/ansible/plugins/callback/hipchat.py) as it seems to be invalid: from_buffer() cannot return the address of the raw string within a str or unicode or bytearray object
[WARNING]: Skipping plugin (/usr/lib/python2.7/site-packages/ansible/plugins/callback/nrdp.py) as it seems to be invalid: from_buffer() cannot return the address of the raw string within a str or unicode or bytearray object
[WARNING]: Skipping plugin (/usr/lib/python2.7/site-packages/ansible/plugins/callback/slack.py) as it seems to be invalid: from_buffer() cannot return the address of the raw string within a str or unicode or bytearray object
[WARNING]: Skipping plugin (/usr/lib/python2.7/site-packages/ansible/plugins/callback/splunk.py) as it seems to be invalid: from_buffer() cannot return the address of the raw string within a str or unicode or bytearray object
[WARNING]: Skipping plugin (/usr/lib/python2.7/site-packages/ansible/plugins/callback/sumologic.py) as it seems to be invalid: from_buffer() cannot return the address of the raw string within a str or unicode or bytearray object
The authenticity of host 'node3.test-k8s (192.168.30.173)' can't be established.
ECDSA key fingerprint is SHA256:vs8UaBvGyvZ79sPKjl0WASckxsWt/+nJsHfaUd2ayWo.
ECDSA key fingerprint is MD5:7a:64:5e:72:80:00:5a:65:59:71:cf:02:61:8a:d9:f4.
Are you sure you want to continue connecting (yes/no)? The authenticity of host 'node4.test-k8s (192.168.30.174)' can't be established.
ECDSA key fingerprint is SHA256:FnmzBan+7fO7HWvk6BM/h6Ow+/TZE07xOgEo4RBFaPo.
ECDSA key fingerprint is MD5:65:08:c3:38:14:36:e1:5a:9a:32:1a:13:62:73:50:62.
Are you sure you want to continue connecting (yes/no)? The authenticity of host 'node5.test-k8s (192.168.30.175)' can't be established.
ECDSA key fingerprint is SHA256:VBtBKNLNGKMaSwuMrw0HKlvIAQPfAZnpw6aW0nTWOVI.
ECDSA key fingerprint is MD5:28:34:8d:bf:0c:08:17:dd:31:18:f7:f1:07:ec:5b:01.
Are you sure you want to continue connecting (yes/no)? master.test-k8s | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": false, 
    "ping": "pong"
}
node1.test-k8s | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": false, 
    "ping": "pong"
}

node3.test-k8s | UNREACHABLE! => {
    "changed": false, 
    "msg": "Failed to connect to the host via ssh: Host key verification failed.", 
    "unreachable": true
}

node4.test-k8s | UNREACHABLE! => {
    "changed": false, 
    "msg": "Failed to connect to the host via ssh: Host key verification failed.", 
    "unreachable": true
}

node5.test-k8s | UNREACHABLE! => {
    "changed": false, 
    "msg": "Failed to connect to the host via ssh: Host key verification failed.", 
    "unreachable": true
}
```

解决方案如下：

方案一：删除~/.ssh/known_hosts文件，如果还是不行，则尝试方案二

方案二：修改~/.ssh/known_hosts

修改内容如下：

```bash
[root@master .ssh]# vim known_hosts 

node1.test-k8s,192.168.30.171 ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBFGx20i1bubXSCefB0yQQFhfGBgRv4eOq9S7X3C60xBKEqfTTlO4HhdKrmyJev+z4RoiUr+f9s9IQjGMY8cWpj4=
master.test-k8s,192.168.30.170 ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBEyOIj+H+FCKa/GDmJL1yWLmWR075tZwF8fjg/7IqMbj+oqrQ7UfoleBHgn1x7J4Iw2x/52kc7xMSHjky8cn7Yw=
node3.test-k8s,192.168.30.173 ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBLRtsT7h1jeGsy8eqhXASzD3fek1iDEc2Y6xQs9xc3iltGkQAclzrgptYj3DlCbmf7mSUE5tkc4xLEe4I/vQCiU=
node4.test-k8s,192.168.30.174 ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBM9yITcxresV0ceUqDdmIVFp6SaURYiUl/R6mF5lzvIfTneihG7Q5BU01nepl923hgDWxrQ2MqctbLGKJ/teCtg=
node5.test-k8s,192.168.30.175 ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBE52pSwQo3UhgrTbOKU/7ekJmo3GPEsqKpTw4B8V/D1YYnZJZtXIN+Vcnx8X027eMq71om8jCd1RRFUo6nakJg8=
```



## 安装k8s

```bash
# 执行脚本
~/k8s-ansible/bin/install-k8s.sh
```

## 验证

检查node是否就绪：kubectl get nodes

检查是否可正常创建deployment: kubectl create -f ~/k8s-ansible/deployment/nginx.yml

检查是否可正常创建deployment: kubectl create -f ~/k8s-ansible/deployment/busybox.yml

检查是否可正常生成service: kubectl expose deployment nginx

检查是否可正常进入容器: kubectl exec -it podname bash

查看dns及service负载是否正常: 服务中curl ping 或 wget service_ip 及 service_name

异常的pod在对应的节点上查看容器: docker ps
