---
layout: post
title: "如何在Ubuntu 12.04 LTS 上安装L2TP Server"
date: 2014-06-19 15:20
comments: true
categories: ['各种各种']
---
### 安装必要组件
```bash
sudo apt-get install xl2tpd openswan ppp
```
### IPSec / Openswan
- 拷贝下面内容到 `/etc/ipsec.conf`，
并把其中的`x.x.x.x`替换为你服务器的ip
```
config setup
    nat_traversal=yes
    virtual_private=%v4:10.0.0.0/8,%v4:192.168.0.0/16,%v4:172.16.0.0/12,%v4:!10.152.2.0/24
    #contains the networks that are allowed as subnet= for the remote client. In other words, the address ranges that may live behind a NAT router through which a client connects.
    oe=off
    protostack=netkey

conn L2TP-PSK-NAT
    rightsubnet=vhost:%priv
    also=L2TP-PSK-noNAT

conn L2TP-PSK-noNAT
    authby=secret
    pfs=no
    auto=add
    keyingtries=3
    rekey=no
    # Apple iOS doesn't send delete notify so we need dead peer detection
    # to detect vanishing clients
    dpddelay=30
    dpdtimeout=120
    dpdaction=clear
    # Set ikelifetime and keylife to same defaults windows has
    ikelifetime=8h
    keylife=1h
    type=transport
    # Replace IP address with your local IP (private, behind NAT IP is okay as well)
    left=x.x.x.x
    # For updated Windows 2000/XP clients,
    # to support old clients as well, use leftprotoport=17/%any
    leftprotoport=17/1701
    right=%any
    rightprotoport=17/%any
    #force all to be nat'ed. because of iOS
    forceencaps=yes
```
- 拷贝下面内容到 `/etc/ipsec.secrets`，
并把其中的`x.x.x.x`替换为你服务器的ip
```
x.x.x.x   %any:  PSK "somegoodpassword"
```
- 开启ipsec服务
```bash
/etc/init.d/ipsec start
```
- 执行下面脚本
```bash
for each in /proc/sys/net/ipv4/conf/*
do
echo 0 > $each/accept_redirects
echo 0 > $each/send_redirects
done
```
- 验证ipsec服务
```bash
sudo ipsec verify
```
不能出现任何错误
```
Checking your system to see if IPsec got installed and started correctly:
Version check and ipsec on-path                                 [OK]
Linux Openswan U2.6.28/K2.6.32-32-generic-pae (netkey)
Checking for IPsec support in kernel                            [OK]
NETKEY detected, testing for disabled ICMP send_redirects       [OK]
NETKEY detected, testing for disabled ICMP accept_redirects     [OK]
Checking that pluto is running                                  [OK]
Pluto listening for IKE on udp 500                              [OK]
Pluto listening for NAT-T on udp 4500                           [OK]
Checking for 'ip' command                                       [OK]
Checking for 'iptables' command                                 [OK]
Opportunistic Encryption Support                                [DISABLED]
```
- 创建`/etc/init.d/ipsec.vpn`的启动脚本
```bash
case "$1" in
  start)
echo "Starting my Ipsec VPN"
iptables  -t nat   -A POSTROUTING -o eth0 -s 10.152.2.0/24 -j MASQUERADE
echo 1 > /proc/sys/net/ipv4/ip_forward
for each in /proc/sys/net/ipv4/conf/*
do
    echo 0 > $each/accept_redirects
    echo 0 > $each/send_redirects
done
/etc/init.d/ipsec start
/etc/init.d/xl2tpd start
;;
stop)
echo "Stopping my Ipsec VPN"
iptables --table nat --flush
echo 0 > /proc/sys/net/ipv4/ip_forward
/etc/init.d/ipsec stop
/etc/init.d/xl2tpd stop
;;
restart)
echo "Restarting my Ipsec VPN"
iptables  -t nat   -A POSTROUTING -o eth0 -s 10.152.2.0/24 -j MASQUERADE
echo 1 > /proc/sys/net/ipv4/ip_forward
for each in /proc/sys/net/ipv4/conf/*
do
    echo 0 > $each/accept_redirects
    echo 0 > $each/send_redirects
done
/etc/init.d/ipsec restart
/etc/init.d/xl2tpd restart

;;
  *)
 echo "Usage: /etc/init.d/ipsec.vpn  {start|stop|restart}"
 exit 1
  ;;
esac
```
开启执行权限
```bash
sudo chmod +x /etc/init.d/ipsec.vpn
```
关闭默认的ipsec服务
```bash
sudo update-rc.d -f ipsec remove
```
添加刚刚创建的服务
```bash
sudo update-rc.d ipsec.vpn defaults
```
### L2TP
- 拷贝下面内容到`/etc/xl2tpd/xl2tpd.conf`
```
[global]
ipsec saref = no

[lns default]
ip range = 10.152.2.2-10.152.2.254
local ip = 10.152.2.1
require chap = yes
refuse pap = yes
require authentication = yes
ppp debug = yes
pppoptfile = /etc/ppp/options.xl2tpd
length bit = yes
```
- 在`/etc/xl2tpd/l2tp-secrets`中创建验证字符串
```
* * exampleforchallengestring
```
- 拷贝下面内容到`/etc/ppp/options.xl2tpd`
```
refuse-mschap-v2
refuse-mschap
ms-dns 8.8.8.8
ms-dns 8.8.4.4
asyncmap 0
auth
crtscts
idle 1800
mtu 1200
mru 1200
lock
hide-password
local
#debug
name l2tpd
proxyarp
lcp-echo-interval 30
lcp-echo-failure 4
```
### 添加用户
－ 编辑文件`/etc/ppp/chap-secrets`这里和pptp是同一个文件
```
user1 l2tpd chooseagoodpassword *
user2 * chooseagoodpassword *
```
### 开启转发，配置过pptp则可跳过
－ 编辑文件`/etc/sysctl.conf`
```
net.ipv4.ip_forward=1
```
执行
```
sysctl -p
```
### 开启服务
```bash
sudo /etc/init.d/ipsec.vpn restart
```
### 开启防火墙端口，如果使用防火墙
```
L2TP
———————————
UDP:500    (isakmp)
UDP:4500   (nat-t)
UDP:1701   (l2tp)
```
### Mac连接
- 打开network preferences创建l2tp类型的vpn，填入服务器ip，用户名
- 验证里面password填上面设置的somegoodpassword
- shared secret 填 exampleforchallengestring
- iOS设备同理
# Winter is coming
来源:
[L2TPServer](https://help.ubuntu.com/community/L2TPServer),
[VPN端口号：PPTP、L2TP](http://www.xshell.net/networks/339.html)
