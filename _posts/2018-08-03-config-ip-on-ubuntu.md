---
layout: post
title:  "Ubuntu Server 18.04配置网络"
categories: ubuntu
tags:  ubuntu
author: Evan
---
* content
{:toc}

## 查看Ubuntu系统版本
```
lsb_release -a
```

## 查看IP配置
```
ifconfig
```
或
```
ip a
```

## 查看DNS配置
```
systemd-resolve --status
```
Ubuntu Server 18.04 采用netplan作为网络配置管理，配置文件/etc/netplan/50-cloud-init.yaml，使用命令netplan apply使配置生效

## IP配置
```
root@gitser:/etc/netplan# vi /etc/netplan/50-cloud-init.yaml 
# This file is generated from information provided by
# the datasource.  Changes to it will not persist across an instance.
# To disable cloud-init's network configuration capabilities, write a file
# /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg with the following:
# network: {config: disabled}
network:
    ethernets:
        ens33:
            addresses:
            - 192.168.3.184/24
            dhcp4: false
            gateway4: 192.168.3.1
            nameservers:
                addresses:
                - 192.168.3.1
                search: []
    version: 2
```


如果对一个网卡配置多个IP可以如下
```
network:
    ethernets:
        ens33:
            addresses:
            - 192.168.3.184/24
            - 192.168.3.183/24
            dhcp4: false
            gateway4: 192.168.3.1
            nameservers:
                addresses:
                - 192.168.3.1
                search: []
    version: 2
```
ens33:网卡名
addresses:为配置静态IP
```
        ens33:
            addresses:
            - 192.168.3.184/24
```

gateway4:为IPv4的网关
```
gateway4: 192.168.3.1
```

其中nameserverss配置为DNS
```
            nameservers:
                addresses:
                - 192.168.3.1
                search: []
```

## 重新启动/测试网络
使用新方法，您必须使用netplan重新启动网络。因此，一旦配置好了接口，就使用以下命令:
```
netplan apply
```
上述命令将重新启动网络并应用新的配置。如果网络无法正常工作，您可以输入以下命令：
```
netplan  --debug apply
```

- 以上操作均在root用户下进行，如在普通用户，请自行加上sudo。

- 这里有几点需要注意： 
1. 将renderer: NetworkManager注释，否则netplan命令无法生效； 
2. ip配置信息要按如上格式，使用yaml语法格式，每个配置项使用空格缩进表示层级； 
3. 对应配置项后跟着冒号，之后要接个空格，否则netplan命令也会报错。


