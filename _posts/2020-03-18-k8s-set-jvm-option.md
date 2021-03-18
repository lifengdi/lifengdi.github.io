---
layout: post
title: K8S设置JVM参数
categories: [k8s, JVM]
description: K8S设置JVM参数
keywords: k8s, JVM
---

## TOMCAT：

设置`JAVA_OPTS`：

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  generation: 43
  labels:
    app: tomcat-app
  name: tomcat-app
  namespace: default
spec:
  progressDeadlineSeconds: 600
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: tomcat-app
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: tomcat-app
    spec:
      containers:
        - env:
            - name: JAVA_OPTS
              value: >-
                -server -Xms4G -Xmx4G -Xmn1536M -Xss256k -XX:PermSize=128m
                -XX:MaxPermSize=256m -XX:+UseParNewGC -XX:ParallelGCThreads=6
                -XX:+UseConcMarkSweepGC -XX:+CMSParallelRemarkEnabled
                -XX:SurvivorRatio=8 -XX:CMSInitiatingOccupancyFraction=70
                -XX:+CMSScavengeBeforeRemark -XX:CMSFullGCsBeforeCompaction=5
                -XX:+UseCMSCompactAtFullCollection -Xloggc:/opt/docker/gc-%t.log
                -XX:+PrintGCDetails -XX:+PrintGCDateStamps
```

## JAR包
设置`JAVA_TOOL_OPTIONS`
```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  generation: 43
  labels:
    app: jar-app
  name: jar-app
  namespace: default
spec:
  progressDeadlineSeconds: 600
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: jar-app
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: jar-app
    spec:
      containers:
        - env:
            - name: JAVA_TOOL_OPTIONS
              value: >-
                -Xms4G -Xmx4G -Xmn1536M -Xss256k -XX:PermSize=128m
                -XX:MaxPermSize=256m -XX:+UseParNewGC -XX:ParallelGCThreads=6
                -XX:+UseConcMarkSweepGC -XX:+CMSParallelRemarkEnabled
                -XX:SurvivorRatio=8 -XX:CMSInitiatingOccupancyFraction=70
                -XX:+CMSScavengeBeforeRemark -XX:CMSFullGCsBeforeCompaction=5
                -XX:+UseCMSCompactAtFullCollection -Xloggc:/opt/docker/gc-%t.log
                -XX:+PrintGCDetails -XX:+PrintGCDateStamps
```
