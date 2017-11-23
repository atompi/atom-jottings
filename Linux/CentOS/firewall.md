# CentOS 7 firewall 使用与配置实例

_Centos7 下默认的防火墙是 firewall，替代了之前的 iptables，firewall 有图形界面管理和命令行管理两种方式，本文简要介绍命令行 firewall 的使用。_

## 配置 firewall

进入系统之后，Centos7 默认是已安装了 firewall，但是没有启动的，所以需要先启动下 firewall，同时设置开机自启动

```
systemctl start firewalld       ##启动Firewall
systemctl enable firewalld.service  ##设置开机自启动
```

```
ln -s '/usr/lib/systemd/system/firewalld.service' '/etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service'
ln -s '/usr/lib/systemd/system/firewalld.service' '/etc/systemd/system/basic.target.wants/firewalld.service'
```

## 常用命令介绍

```
firewall-cmd --state                           ##查看防火墙状态，是否是running
firewall-cmd --reload                          ##重新载入配置，比如添加规则之后，需要执行此命令
firewall-cmd --get-zones                       ##列出支持的zone
firewall-cmd --get-services                    ##列出支持的服务，在列表中的服务是放行的
firewall-cmd --query-service ftp               ##查看ftp服务是否支持，返回yes或者no
firewall-cmd --add-service=ftp                 ##临时开放ftp服务
firewall-cmd --add-service=ftp --permanent     ##永久开放ftp服务
firewall-cmd --remove-service=ftp --permanent  ##永久移除ftp服务
firewall-cmd --add-port=80/tcp --permanent     ##永久添加80端口
iptables -L -n                                 ##查看规则，这个命令是和iptables的相同的
man firewall-cmd                               ##查看帮助
```