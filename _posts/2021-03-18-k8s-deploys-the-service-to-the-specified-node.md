---
layout: post
title: k8s部署服务到集群中的指定节点
categories: [k8s]
description: k8s部署服务到集群中的指定节点
keywords: k8s
---

## 查看标签
```bash
kubectl get node --show-labels
```

## 给node添加标签
```bash
kubectl label nodes k8s-slave2 slave=184

# 语法
kubectl label nodes <node-name> <label-key>=<label-value> 
```
修改标签的值：
```bash
#语法: 需要加上--overwrite参数：
kubectl label nodes <node-name> <label-key>=<label-value> --overwrite
```

如果需要移除标签，则使用：
```bash
kubectl label nodes k8s-slave2 slave-

# 语法
kubectl label nodes <node-name> <label-key>-
```
需要注意的是，移除标签并不会重新部署服务，因此所有服务还是在原节点。

## k8s编排文件中指定NodeSelector

此例中即增加以下配置：
```yaml
    nodeSelector:
        slave: "184"
```

较完整的yaml文件：
```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: centos-master
  labels:
    name: centos-master
spec:
  replicas: 1
  selector:
    name: centos-master
  template:
    metadata:
      labels:
        name: centos-master
    spec:
      containers:
      - name: centos
        image: 10.10.30.180/library/centos7:v1
      nodeSelector:
        slave: "184"
```

最后，重新部署服务即可。
