---
title: "note"
draft: true
categories:
    - unarchives
tags:
    - draft
---

## DOCKER 实操入门篇

### some runtime command
#### run in front
```shell
docker run -it ubuntu /bin/bash
# -i 保证容器的STDIN开启
# -t 分配一个伪tty
# ubuntu，基础镜像，会先检查本地是否存在，不存在则向docker hub registry拉取
# /bin/bash 容器启动后执行什么内容
```
启动后，容器会生成一个uuid，这个也可以通过`docker ps`指令来看到，我们可以通过uuid（长/短）或是docker名称来指定唯一id（可以通过na--name指定，或是随机生成）
```shell
docker start/stop/restart/rm uuid/name
# 其中stop本质是向容器的进程组发送sigterm信号
```

#### run as daemon
```shell
sudo docker run [--restart=always/on-failure[:5]] --name daemon_dave -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"
# -d 选项代表后台运行
# restart flag可以设定容器自动重启,可以设置始终重启、失败重启（可以指定重启次数），需要注意的时这是2.0版本才引入的选项
sudo docker logs ["--tail n"] [-f] daemon_dave
# 也就是后台运行的镜像都会被重定向到logs中
# -f 持续跟踪, 可以使用ctrl+c退出
# --tail 从尾部读，n=0时持续跟踪最新(只看信新产生的，需要配合-f)
```

#### run in a running docker
```shell
sudo docker exec [-d] daemon_dave touch /etc/new_config_file
```

#### some detail
```shell
sudo docker inspect [--format='{{.State.Running}}'] daemon_dave [...]
# 可以去获取容器信息，包括配置、名称、网络等一系列信息
# -f/--format 选项可以指定要查看哪些字段
# inspect 指令可以同时指定多个容器，相当于可以批量查看容器的各种字段

sudo docker rm `docker ps -a -q`
# -a 列出所有容器
# -q 表示只返回容器id
# 这样就可以一次删除所有列表
```

### docker image and repo

#### what is docker image
an image is composed of multiple filesystem which stacked together.

最底层时一个bootfs，当容器启动后，会被移到内存中，而bootfs则是被卸载以提供更多内存给initrd磁盘镜像使用。
再往上则是rootfs，可以根据具体os而变化。这里和传统linux不太一样。传统上rootfs启动时ro，但引导结束并通过完整性检查后切换为rw。而docker中永远是ro，并且利用`union mount`在这上面加载更多fs。
这样就构成了image，一个image可以叠在另一个上面，下层称为`parent image`，底层称为`base image`。
docker中下方的每一层都是只读的，当需要修改的时候，则会把只读内容往上拷贝到读写层，这样只读版本依然存在但是被上方读写层隐藏。（"`copy on write`")

> q:禅道
    1. arch of cpu
    2. storage driver
    3. cgroup and namespace
    4. uname -a

```shell
sudo docker images
# 一般来说，images都存在宿主机的/var/lib/docker下面

sudo docker pull ubuntu
# 这个会产生去docker repo拉下ubuntu仓库的所有镜像，为了区分仓库中的不同镜像，docker提供了一种叫tag的功能，每个tag都是对特定镜像的一些镜像层进行标记

# docker hub中有两种类型仓库：user repository && top-level repository.一个由用户自己管理，一个由docker内部工作人员管理。一般用户仓库由两部分组成（命名规范） user/repo。顶层则只需要有仓库名。如 hzj/custom_image, ubuntu。
```
