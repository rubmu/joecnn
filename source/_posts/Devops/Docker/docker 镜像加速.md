---
title: Docker 镜像仓库加速
date: 2018/08/05 14:00:00
tags: [docker]
---

docker 在默认安装后，当需要下载镜像时，通过命令`docker pull user/image` 拉取镜像都是访问默认的 docker hub 上的镜像，在国内网络环境下，下载一个镜像需要很长的时间，可以考虑使用 Registry Mirror 配置国内的镜像仓库。

> 使用由阿里云提供的 Docker 镜像仓库进行加速。

### 1. 登录阿里云
> [阿里云 Docker 镜像仓库](https://cr.console.aliyun.com/?spm=5176.100239.blogcont29941.13.haQkR4#/accelerator)

开启 Docker Hub 镜像站点
![docker_ali](../../../../images/docker_ali.png)

### 2. Windows 使用 Docker 加速
1. 创建一台 docker machine 同时配置 docker 加速器

```bash
docker-machine create --engine-registry-mirror=https://6bybmq21.mirror.aliyuncs.com -d virtualbox default
```

2. 对于已经创建的 docker machine 实例，更换镜像源方法如下  
i. 在 window 命令执行 `docker-machine ssh [machine-name]` 进入 VM bash  
ii. `sudo vi /var/lib/boot2docker/profile`  
iii. 在`--label provider=virtualbox`的添加一行 `--registry-mirror https://xxx.mirror.aliyuncs.com`  
iiii. 重启 docker 服务：`sudo /etc/init.d/docker restart` 或重启 VM ： `docker-machine restart`

3. docker for windows
> 设置 Daemon Registry mirrors
![docker_setting](../../../../images/docker_setting.png)

4. 查看 mirror 配置

```
docker-machine env default
eval "$(docker-machine env default)"
docker info
```

### 3. CentOs 使用镜像加速
1. 安装/升级 Docker 客户端

```
curl -sSL http://acs-public-mirror.oss-cn-hangzhou.aliyuncs.com/docker-engine/internet | sh -
```

2. 修改 daemon 配置文件 `/etc/docker/daemon.json` 来使用加速

```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://6bybmq21.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```
