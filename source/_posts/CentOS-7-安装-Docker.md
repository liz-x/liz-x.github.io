---
title: CentOS 7 安装 Docker
date: 2019-02-13 18:27:53
categories:
    - Linux
tags:
    - Docker
---

```sh
# 安装 Docker
# 官方文档：https://docs.docker.com/install/linux/docker-ce/centos/
# 阿里文档：https://yq.aliyun.com/articles/110806/

cd /tmp/

curl -fsSL https://get.docker.com | sh -s docker --mirror Aliyun
sudo usermod -aG docker www-data

sudo systemctl start docker
sudo systemctl enable docker

# 配置镜像加速器(阿里)
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://fm9u2lxx.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker

# 安装 docker-compose
# 官方文档：https://docs.docker.com/compose/install/
#
# 超时解决方法：
# 方法1：
#    ping github-cloud.s3.amazonaws.com 获取 IP
#    修改 hosts，添加：ip github-cloud.s3.amazonaws.com
# 方法2：
#    使用 https://get.daocloud.io
#
# 这里使用方法 2
#
sudo curl -L "https://get.daocloud.io/docker/compose/releases/download/1.23.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose
```
