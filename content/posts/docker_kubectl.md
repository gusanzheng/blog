---
title: "Container"
subtitle: ""
date: 2025-03-30T16:09:01+08:00
lastmod: 2025-03-30T16:09:01+08:00
draft: false
author: "Steven"
authorLink: "https://github.com/gusanzheng"
tags: ["docker", "kubectl"]
categories: ["tutorial"]
---
**摘要**
简单使用容器技术。

**关键词**
`docker`, `kubectl`, `minikube`
<!--more-->
## 1. `docker`

### 基本用法
```bash
docker run -itd --name=steven-test \ 
    -v /data1/steven/models:/models \
    -p 6666:8080 \
    -e LD_LIBRARY_PATH=$LD_LIBRARY_PATH:./lib \
    my-image:0.0.1 \
    /bin/bash
 
--restart always # 崩溃后自动重启
--net=host # 与宿主机共享网络
--privileged=true # 相当于宿主机的root用户，不开这个，只是宿主机的普通用户
--cpus=16 --memory=64g # 限定cpu16核，内存64g
```

### 导入导出
```bash
# commit: 从容器创建镜像
docker commit -a "author" -m "description" my-container my-image

#### 容器 ####
# export: 从容器导出tar包, 这种文件只包含文件系统的内容，不包含任何元数据
docker export -o ubuntu.tar my-container
# import: 从tar包导入镜像
docker import ubuntu.tar ubuntu:v1

#### 镜像 ####
# save: 将镜像 runoob/ubuntu:v3 生成 my_ubuntu_v3.tar，包含了镜像的所有层和版本信息。
docker save -o my_ubuntu_v3.tar runoob/ubuntu:v3 
# load: 将my_ubuntu_v3.tar加载进来生成镜像
docker load -i my_ubuntu_v3.tar
```

### nvidia-docker
```bash
docker 1.13.1 版本
--runtime=nvidia
--env CUDA_VISIBLE_DEVICES=1 # 允许你指定具体的 GPU 用于你的容器

# 在 Docker 19.03 及更高版本中，
--runtime=nvidia 参数已被废弃
Docker容器的驱动版本不能高于宿主机的驱动版本，可以通过 --runtime=runc 来避免无法启动的问题

官方建议使用更简单的 --gpus all 参数
--gpus 1 不指定到底哪个 GPU 被分配给容器，只是指定1个gpu给容器
```
如果你在 `/etc/docker/daemon.json` 中配置了 nvidia 运行时，可以保留配置，但不再需要显式指定 `--runtime=nvidia`

看 runtime 是否是 nvidia
```bash
dokcer info | grep -i runtime

# 如果正确配置了 nvidia 运行时
Runtimes: nvidia runc io.containerd.runc.v2
Default Runtime: runc
```

## 2. `minikube`

```bash
minikube start
minikube start --kubernetes-version=v1.28.2 指定版本
minikube stop/delete 停止/删除本地集群
minikube delete --all 删除所有本地集群和配置文件
minikube status 查看状态
minikube node list 查看minikube节点信息
minikube ip 查看 minikube ip
minikube ssh 连接上集群
```

## 3. `kubectl`

### 基础
```bash
# 构建/升级资源对象，如果资源已存在，不会报错
kubectl apply -f xxx.yaml
# 查看资源对象详细信息
kubectl describe <resource-type> <resource-name> 
# 获取配置
kubectl get <resource-type> [resource-name] -o yaml # 用 yaml / json 格式显示
# 删除
kubectl delete <resource-type> <resource-name>

kubectl get nodes # 查看节点
kubectl config view # 检查当前上下文和集群信息
kubectl cluster-info # 查看集群信息
kubectl api-resources # 显示出资源对象相应的 API 版本和类型
```

命令参数
- `-l app=nginx` // 过滤标签是app=nginx
- `-o wide` 以更宽格式显示
- `-w` // watch  实时过程
- `-c <container_name>` 指定容器名字
- `-n <namespace>` 指定命名空间
- `--all-namespaces` 指定全部命名空间

### pod
```bash
kubectl exec -it <pod-name> -c <container_name> -- <command> # 在pod中运行命令
kubectl logs <pod-name> # 查看pod日志
kubectl cp <pod_name>:/log /home/log -c <container_name> # 拷贝文件
```
### deployment
```bash
# 直接编辑
kubectl edit deployment/nginx-deployment
# 重启部署
kubectl rollout restart deployment <deployment_name>
# 查看滚动更新状态
kubectl rollout status deployment/nginx-deployment
# 回滚到上一版本
kubectl rollout undo deployment/nginx-deployment
    # --to-revision=2 指定版本
# 查看部署历史
kubectl rollout history deployment/nginx-deployment
# 暂停部署
kubectl rollout pause deployment/nginx-deployment
# 恢复部署，暂停期间的所有操作，最后只会触发一次“滚动更新”
kubectl rollout resume deploy/nginx-deployment
```