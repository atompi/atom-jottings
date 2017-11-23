# iptables 使用与配置实例

_iptables 是与 Linux 内核集成的 IP 信息包过滤系统，其自带防火墙功能，我们在配置完服务器的角色功能后，需要修改 iptables 的配置。_

配置 CentOS 和 Ubuntu 等 Linux 服务器时需要对服务器的 iptables 进行配置，以下是 iptables 常见的几种配置方法。

## 1. 查看当前所有的 iptables 规则

```
iptables -L -n
```

## 2. 添加允许 INPUT 访问规则，以下时常见服务的端口设置，如果需要拒绝访问，则将 ACCEPT 改为 DROP 即可

```
#SSH
iptables -A INPUT -p tcp --dport 22 -j ACCEPT

#HTTP
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -p tcp --dport 8080 -j ACCEPT

#HTTPS
iptables -A INPUT -p tcp --dport 443 -j ACCEPT

#POP3
iptables -A INPUT -p tcp --dport 110 -j ACCEPT

#SMTP
iptables -A INPUT -p tcp --dport 25 -j ACCEPT

#FTP
iptables -A INPUT -p tcp --dport 21 -j ACCEPT
iptables -A INPUT -p tcp --dport 20 -j ACCEPT

#DNS
iptables -A INPUT -p tcp --dport 53 -j ACCEPT
```

## 3. 添加使用 IP 限制 INPUT 访问规则，这里拿 SSH 为例，192.168.0.100 为允许的 IP

```
#DELETE
iptables -D INPUT -p tcp --dport 22 -j ACCEPT

#ADD
iptables -A INPUT -s 192.168.0.100 -p tcp --dport 22 -j ACCEPT
```

## 4. 保存 iptables 的设置，修改完规则后记得保存

```
/etc/rc.d/init.d/iptables save
```

## 5. 修改 /etc/sysconfig/iptables 添加防火墙开放80端口规则

```
-A RH-Firewall-1-INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT
```

## 6. 打开主动模式 21 端口

```
iptables -A INPUT -p tcp --dport 21 -j ACCEPT
```

## 7. 打开被动模式 49152~65534 之间的端口

```
iptables -A INPUT -p tcp --dport 49152:65534 -j ACCEPT
iptables -A INPUT -i lo -j ACCEPT
iptables -A INPUT -m state --state ESTABLISHED -j ACCEPT
```

## 8. iptables 配置文件 /etc/sysconfig/iptables 模版

```
# Firewall configuration written by system-config-firewall
# Manual customization of this file is not recommended.
*filter
:INPUT ACCEPT [0:0]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [0:0]
-A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
-A INPUT -p icmp -j ACCEPT
-A INPUT -i lo -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT
-A INPUT -j REJECT --reject-with icmp-host-prohibited
-A FORWARD -j REJECT --reject-with icmp-host-prohibited
COMMIT
```