---
title: Mac + Vagrant + Docker - LNMP
date: 2019-03-20 19:53:50
categories:
    - Mac
tags:
    - Mac
    - Linux
    - Docker
    - 工具
---

## 准备工作

1. VirtualBox
2. Vagrant
3. CentOS 镜像



[VirtualBox 下载地址](https://www.virtualbox.org/wiki/Downloads)

![](https://ws4.sinaimg.cn/large/006tKfTcgy1g17wq1rcvaj30jy04o0sm.jpg)



[Vagrant 下载地址](https://www.vagrantup.com/downloads.html)

安装完成后，终端输入：`vagrant -v`

![](https://ws1.sinaimg.cn/large/006tKfTcgy1g17wstjxb2j30d404a0sk.jpg)



[官方镜像](https://app.vagrantup.com/boxes/search)

![image-20190319112851082](https://ws2.sinaimg.cn/large/006tKfTcgy1g17y0nk47lj31kq0u0tjv.jpg)



## Vagrant

### 安装 CentOS

```shell
# 新建 vagrant 目录
mkdir ~/vagrant
cd ~/vagrant

# 官方镜像：在线安装
vagrant init centos/7

# 下载镜像：自定义镜像名、指定镜像路径(推荐)
# 官方：https://vagrantcloud.com/centos/boxes/7/versions/1902.01/providers/virtualbox.box
# vagrant box add <boxName> <boxFilePath>
vagrant box add centos7 ~/Downloads/centos-7.0-x86_64.box
```

**备注：**目录下会生成配置文件 —— Vagrantfile



### 安装插件

1. vagrant-hostmanage —— 用主机名访问（虚拟机之间访问、宿主机访问虚机）
2. vagrant-vbguest —— 自定义共享目录（必须，解决异常：mount: unknown filesystem type 'vboxsf'）

```shell
vagrant plugin install vagrant-hostmanage
vagrant plugin install vagrant-vbguest
```



### 自定义配置

修改 Vagrantfile(在 end 之前)

```yaml
config.vm.network "private_network", ip: "192.168.33.10"    # 虚拟机 IP
config.vm.synced_folder "~/websvr", "/websvr"               # 共享目录

# 可选
config.vm.provision "shell", path: "bootstrap.sh"           # 启动时运行脚本
```



### Vagrantfile 参数说明

**config.vm.define 多台主机**

```shell
config.vm.define "node1" do |node1|
end
config.vm.define "node1" do |node2|
end
config.vm.define "node1" do |node3|
end
```



**\*.vm.network 虚拟机网络**

```shell
nat             # 默认方式
forwarded_port  # 端口转发
private network # 私有网络（仅主机模式）
public network  # 公有网络（桥接模式）

config.vm.network "forwarded_port", guest: 80, host: 8080
config.vm.network "private_network", ip: "192.168.33.10"
config.vm.network "public_network"
```



**\*.vm.hostname 定义虚机主机名**

```shell
config.vm.define "node1" do |node1|
    node1.vm.network "private_network", ip: "192.168.33.11"
    node1.vm.network "public_network"
    node1.vm.hostname = "node1"
end
```



### 访问 CentOS

```shell
# 新建共享目录
mkdir ~/websvr
```

```shell
vagrant up      # 启动虚拟机(默认用户：vagrant)
vagrant ssh     # 访问虚拟机
```

宿主机：ping 192.168.33.10

**备注：**

1. 每次更改 Vagrantfile 后，需要重启虚拟机 `vagrant reload`。
2. **提高安全性**，root 账户建议通过密钥登录。



如果出现一下报错：

```
Permission denied - /Users/xxx/.vagrant.d/data/machine-index/index (Errno::EACCES)
```

1. rm -rf ~/.vagrant.d
2. 启动：vagrant up



### 打包镜像

1. 关闭虚拟机：`vagrant halt `
2. `mkdir -p backup/centos7`
3. 打包：`vagrant package --output backup/centos7/centos7.box --vagrantfile Vagrantfile`



### 常用命令

```shell
vagrant -v          # 查看版本
vagrant status      # 查看状态
vagrant up          # 启动虚拟机
vagrant ssh         # 访问虚拟机
vagrant reload      # 重启虚拟机
vagrant suspend     # 挂起虚拟机（虚拟机内存都保存在硬盘上，启动可快速恢复）
vagrant resume      # 恢复虚拟机(与挂起对应)
vagrant halt        # 关闭虚拟机
vagrant destroy     # 销毁虚拟机
vagrant package     # 打包镜像（以后任何地方都能用）

# box
vagrant box list    # 镜像列表
vagrant box add     # 添加镜像
vagrant box remove  # 删除镜像
```



## Docker

### 克隆 [Docker Compose](https://github.com/liz-x/docker-server)

```shell
# 宿主机
# 文件共享目录是 /Users
# 所以只能挂载 /User 的文件
cd ~

# 拉取项目
git clone https://github.com/liz-x/docker-server.git websvr
cd websvr

# 删除 git 信息
rm -rf .git*
```



### 系统初始化

```SHELL
cd ~/vagrant
vagrant ssh

cd /websvr/
sh centos-vagrant-init.sh
```

**备注：**vagrant 内 `chown -R www-data:www-data /websvr/` 无效

1. 退出虚拟机
2. 修改 Vagrantfile

    ```shell
    config.vm.synced_folder "~/websvr", "/websvr"       # 共享目录
        owner: "www-data", group: "www-data"            # 指定目录的拥有者和拥有组
    ```

3. vagrant reload



### 启动容器

```shell
cd ~/vagrant
vagrant ssh

# Demo 服务
cd /websrv/compose/demo
docker-compose up -d

# 查看容器状态
docker-compose ps

# 测试
[vagrant@localhost ~] curl 127.0.0.1
Hello World.
```

**备注：**根据工作所需，安装工具与服务，确认一切正常后，就可以打包镜像，共享给其他开发小伙伴啦！！！


如果容器启动报错：

```shell
docker: Error response from daemon: OCI runtime create failed: container_linux.go:344: starting container process caused "process_linux.go:297: getting the final child's pid from pipe caused \"read init-p: connection reset by peer\"": unknown.
```

原因：docker 版本过高，内核版本过低造成。
解决：降低 docker 版本或升级内核版本。
