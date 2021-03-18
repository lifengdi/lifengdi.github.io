---
layout: post
title: K8S + docker部署war包项目
categories: [k8s, docker]
description: K8S + docker部署war包项目
keywords: k8s, docker, war包
---

## 背景
以前使用K8S + docker部署Java项目的时候，大多都是jar包，不过这次公司使用了cas系统，只能使用war包的方式来部署，所以就研究了一下怎么部署的。

## 基础镜像的选择
使用K8S + docker部署war包大概流程和jar包差不多，唯一的区别就是基础镜像包不同。

区别在于部署jar包的时候基础镜像只需要选择Jdk就可以了，而war包则需要选择tomcat+jdk。

tomcat+jdk的基础镜像包可以自己制作，也可以使用tomcat官方制作的镜像。不过建议不要自己制作，还是使用官方的靠谱些。

可以去[https://hub.docker.com/_/tomcat](https://hub.docker.com/_/tomcat)上自行查找自己需要的tomcat版本和对应的jdk版本。

我这里选择的是`tomcat:8-jdk8-openjdk`这个版本的。

## Dockerfile

和部署jar包的脚本相似，只是多了tomcat的目录，另外需要注意的是虽然镜像中的tomcat运行脚本在`/bin`目录下，但是执行的时候并不需要指定脚本具体的路径。

其次多了个解压的步骤，我这里是直接解压到了`ROOT`目录下。

```bash
FROM tomcat:8-jdk8-openjdk

ENV PROJECT_DIR=/opt/docker
ENV TOMCAT_DIR=/usr/local/tomcat
WORKDIR $PROJECT_DIR

COPY target/ROOT.war  $TOMCAT_DIR/webapps/ROOT.war
RUN mkdir $TOMCAT_DIR/webapps/ROOT
RUN unzip -oq $TOMCAT_DIR/webapps/ROOT.war -d $TOMCAT_DIR/webapps/ROOT/

RUN chown -R daemon:daemon $TOMCAT_DIR
RUN chown -R daemon:daemon $PROJECT_DIR

ENV TZ=Asia/Shanghai


EXPOSE 8080 8443
USER daemon
CMD ["catalina.sh", "run"]
```

## 结束
总体来说还是挺简单的，祝大家运行愉快。
