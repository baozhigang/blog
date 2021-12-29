---
title: CentOS配置静态IP
date: 2021-12-29
categories:
- CentOS
tags:
- CentOS
- network
---

📶 CentOS配置静态IP的流程整理如下：

<!--more-->

1. ip a 命令确定LAN1接口对应的网卡型号，很可能是 enp62s0f2(双核机型), eno1(单核机型)
2. 配置默认网卡：vi /etc/sysconfig/network 添加如下配置：
    GATEWAYDEV=enp62s0f2
    GATEWAY=192.168.1.1
3. 修改相应的配置文件 vi /etc/sysconfig/network-scripts/ifcfg-enp62s0f2(网卡名称)
   不启用DHCP：BOOTPROTO=none
   开机联网：ONBOOT=yes
   本机静态IP：IPADDR=192.168.1.200 (内网随机一个)
   掩码：PREIFIX=24
4. 启用网卡: nmcli con up enp62s0f2(网卡名称)；
5. 重启网络: service network restart;
6. 测试是否能联网 ping 192.168.1.8， ping 223.5.5.5(外网测试，比如用阿里IP)
7. 配置实际网关  vi /etc/sysconfig/network
8. 按照实际参数修改 IPPADDR, PREFIX vi /etc/sysconfig/network-scripts/ifcfg-网卡名称；
9. 重启网络: service network restart;
10. 检验是否正确
    cat /etc/sysconfig/network
    cat /etc/sysconfig/network-scripts/ifcfg-网卡名称
    ip a