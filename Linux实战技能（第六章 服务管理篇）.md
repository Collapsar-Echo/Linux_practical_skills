@[TOC](第六章 服务管理篇)
# 服务管理

这是关于防火墙，SSH 服务，FTP 服务，Samba 和 NFS，Nginx，LNMP，BIND知识，以及NAS的搭建。
## 防火墙
### 分类
- 软件防火墙
数据包过滤，IP过滤转发，iptables。
--  包过滤防火墙
-- 应用层防火墙
- 硬件防火墙
防御DDOS攻击，流量攻击。

centos6默认为iptables，
centos7默认为firewallD （底层为netfilter）。
### iptables 的表和链
#### 规则表

- filter 地址过滤

- nat 地址转换

- mangle
- raw
### 规则链
- INPUT 输入
- OUTPUT 输出
- FORWARD 转发
- PREROUTING 路由前转换，目的地址转换
- POSTROUTING 路由后转换，源地址转换

forward 是 同意或者不同意数据经过本机到达其他的网络设备，
NAT的功能，是对数据包的源地址或者目的地址按规则进行修改。

```bash
iptables -t filter 命令 规则链 规则
iptables -t nat 命令 规则链 动作
```

> 命令：
> -L	
> -A -I 
> -D -F -P 
> -N -X -E
> 规则：
> -p 
> -s -d
> -i -o
> -j

### iptables配置文件
/etc/sysconfig/iptables

```bash
service iptables save | start | stop | restart 
```
### firewallD 服务
- 支持区域“zone”概念
- firewall-cmd
```bash
systemctl start | stop | enable | disable firewalld.service
```
## SSH 服务
配置文件
/etc/ssh/sshd_config //服务端配置文件
/etc/ssh/ssh_config //客户端配置文件

```shell
Port 22 //默认端口
PermitRootLogin yes //是否允许root登陆
AuthorizedKeysFile .ssh/authorized_keys //密钥文件公钥位置
```

```bash
systemctl status | start | stop | restart | enable | disable sshd.service
ssh [ -p 端口 ] 用户@远程ip 
ssh-keygen -t rsa
ssh-copy-id
```

```bash
//远程拷贝
scp
sftp
winscp
```
## FTP 服务与vsftpd
- vsftpd 服务安装和启动

```bash
yum install vsftpd ftp
systemctl start vsftpd.service
 建议将 selinux 改为 permissive
 getsebool -a | grep ftpd
 setsebool -P <sebool> 1
```
- vsftpd 服务配置文件

> /etc/vsftpd/vsftpd.conf
> /etc/vsftpd/ftpusers
> /etc/vsftpd/user_list
## Samba 和 NFS（共享服务）
- Samba 
/etc/samba/smb.conf

```powershell
[share]
	comment = my share
 	path=/data/share
 	read only = No
```

```bash
smbpasswd -a //添加用户
smbpasswd -x //删除用户
pdbedit -L //查看用户
```

```bash
systemctl start | stop smb.service
mount -t cifs -o username=user1 //127.0.0.1/user1 /mnt
```
- NFS

 /etc/exports

```powershell
 /data/share *(rw,sync,all_squash)
```

```bash
showmount -e localhost
mount -t nfs localhost:/data/share /ent
systemctl start | stop nfs.service
```
## Nginx
没学😂
## LNMP
没学😂
## BIND
没学😂
## NAS
学了，没记。

