# 使用 systemd 部署单节点 Kubernetes 集群

## 准备

* 了解 `systemd`
* 机器内存 **2GB** 以上

## 关闭防火墙

出现 pod 网络不通的情况，可以尝试关闭防火墙

```bash
$ sudo systemctl stop firewalld && systemctl disable firewalld
```

## 编辑 `.env` 文件

`~/lnmp/kubernetes/systemd/.env`

* `192.168.199.100` 替换为电脑 IP 或公有云主机公网 IP

* `node1` 替换为主机实际的 `hostname`

## 修改主机 hostname

主机的 `hostname` 与 etcd 启动时指定的 `--name` **必须一致**

```bash
$ sudo hostnamectl set-hostname node1
```

## 增加 hosts

`/etc/hosts`

```bash
# 格式: 节点IP hostname

192.168.199.100 node1
```

## 部署 Kubernetes

```bash
# 生成证书
$ dockr-composer up cfssl-local

# 部署
$ ./lnmp-k8s local-install

# 启动
$ ./lnmp-k8s local-up
# 按照提示，手动执行 systemctl 命令，依次启动 kubernetes 各组件
```

### 手动复制 `kubectl` 配置文件

复制 `kubectl` 配置文件，为防止覆盖原有文件，脚本不支持自动复制，必须手动复制

```bash
$ cd ~/lnmp/kubernetes

$ cp systemd/certs/kubectl.kubeconfig ~/.kube/config
```

### 脚本原理

将 `*.service` 放入 `/etc/systemd/system/*`

将 `docker.conf` 放入 `/etc/systemd/system/docker.service.d/*` 中

将证书文件放入 `/opt/k8s/certs`

将配置文件放入 `/opt/k8s/conf`

## 容器运行时

* docker
* containerd
* cri-o

## OCI 运行时

* runc
* runsc