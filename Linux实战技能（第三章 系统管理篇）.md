@[TOC](第三章 系统管理篇)
# Linux系统管理

这是Linux的网络管理，软件安装，进程管理，内存和磁盘管理。
## 网络管理
### 网络状态查看工具
**net-tools VS iproute**
1. **net-tools**
- ifconfig
- route
- netstat
2. **iproute2**
- ip
- ss
### 网卡（网络接口）命令
#### 查看网卡状态
**ifconfig**

```bash
ifconfig //全部网卡
ifconfig eth0 //指定网卡eth0
```

- **eth0** 第一块网卡（网络接口） 
也可能是以下名字：
**eno1** 板载网卡
**ens33** PCI-E网卡
**enp0s3** ⽆法获取物理信息的 PCI-E 网卡
- **lo**  第二块网卡 —— 用于本地环回
- **virbr0** 第三块网卡 —— 用于虚拟化

CentOS 7 使用了一致性网络设备命名，以上都不匹配则使用 eth0
#### 网卡命名修改
网卡命名则受 biosdevname 和 net.ifnames 两个参数影响
|  | biosdevname | net.ifnames | 网卡名 |
|:----:|:---:|:--:|:-------:|
| 默认 | 0 | 1 | ens33|
| 组合1 | 1 | 0 | em1|
| 组合2 | 0 | 0 | eth0 |

步骤如下：
1. 编辑 /etc/default/grub 文件，增加 biosdevname=0 net.ifnames=0
2. 更新 grub

```bash
grub2-mkconfig -o /boot/grub2/grub.cfg
```

3. 重启

```bash
reboot
```
**注**：如果上述方法无效，则需要手动修改

```bash
vim /etc/sysconfig/network-scripts/ifcfg-enp0s3 //enp0s3为当前网卡名称
```
修改文件中的NMAE=和DEVICE=为eth0，同时修改文件名后缀为eth0，保存后重启。
#### 修改网卡配置
**ifconfig <接口> <IP地址> [netmask 子网掩码 ]** //修改网卡IP地址
**ifup <接口>** //打开网卡
**ifdown <接口>** //关闭网卡

```bash
ifconfig eth0 10.0.21.1 //修改网卡地址为10.0.21.1
ifconfig eth0 10.0.21.1 netmask 255.255.255.0 //修改网卡地址和掩码
ifup eth0 //打开网卡
ifdown eth0 //关闭网卡
```
### 网关命令
#### 查看网关状态
**route** 

```bash
route //解析主机名
route -n //不解析主机名
```

#### 修改网关配置
**route add/del default gw <网关ip>** 添加/删除默认网关
**route add/del -host <指定ip> gw <网关ip>** //添加/删除指定IP网关
**route add/del -net <指定网段> netmask <子网掩码> gw <网关ip>** //添加/删除指定网段网关

```bash
route del default gw 10.0.2.2 //删除默认网关
route add default gw 10.0.2.1 //添加默认网关
route add -host 10.0.4.2 gw 10.0.2.1 //添加明细网关
route add -net 10.0.0.0 netmask 255.255.255.0 gw 10.0.2.5添加网段网关
```
### iproute的ip 命令
**ip addr ls**
—— ifconfig


**ip link set dev eth0 up**
—— ifup eth0

**ip addr add 10.0.0.1/24 dev eth1**
—— ifconfig eth1 10.0.0.1 netmask 255.255.255.0


**ip route add 10.0.0/24 via 192.168.0.1**
—— route add -net 10.0.0.0 netmask 255.255.255.0 gw 192.168.0.1

### 网络故障排除命令
- **mii-tool** 查看网卡物理连接状态

```bash
mii-tool eth0 //查看网卡eth0物理连接状态
```
- **ping** 检测目标主机是否畅通

```bash
ping www.baidu.com //ctrl+c停止
```

- **traceroute** 数据包经过中间路由状态

```bash
traceroute -w 1 www.baidu.com //-w 1 表示中间路由等待1s
```

- **mtr** 数据包经过中间路由状态(内容更丰富)

```bash
mtr //直接回车进入，ctrl+c退出
```

- **nslookup** 查看DNS解析

```bash
nslookup www.baidu.com //由域名获得IP
```

- **telnet** 检测目标端口是否畅通

```bash
telnet www.baidu.com 80 //检测到百度服务器的80端口是否畅通，quit退出
```

- **tcpdump** tcp 协议网络抓包

```bash
tcpdump -i any -n port 80 
//-i any：任意网卡；port 80：80端口，可省略；-n：不解析为域名
tcpdump -i any -n host 10.0.0.1 
//-i any：任意网卡；host 10.0.0.1：10.0.0.1主机，可省略；-n：不解析为域名
tcpdump -i any -n host 10.0.0.1 and port 80 
//-i any：任意网卡；-n：不解析为域名
tcpdump -i any -n host 10.0.0.1 and port 80 -w /afile
//-i any：任意网卡；-n：不解析为域名；-w /afile：将捕获信息保存到文件afile
```

- **netstat** 查看服务(端口)的连接状态

```bash
netstat -ntpl //查看服务监听端口
```

- **ss** 显示处于活动状态的套接字信息

```bash
ss -ntpl //查看服务监听端口
```
### 网络管理服务和配置文件
服务管理程序分为两种，分别为**SysV**（service命令）和**systemd**（systemctl命令）。
> centos7的网络管理服务有两个，分别为network服务和NetworkManage服务，其中network服务只支持service管理。
- **service** 

```bash
service network ststus|start|stop|restart //查看或修改network服务的状态
```
- **chkconfig**

```bash
chkconfig --list network //查看network服务具体状态
chkconfig -level 2345 network off|on //修改network服务具体状态
```

- **systemctl**

```bash
systemctl list-unit-files NetworkManager.service //查看NetworkManager服务的状态
systemctl start|stop|restart NetworkManager //修改NetworkManager服务的状态
systemctl enable|disable NetworkManager //修改NetworkManager服务的状态
```
- **hostname**
```bash
hostname //查看主机名
hostname c7.test //临时修改主机名为c7.test
hostnamectl set-hostname c7.test //永久修改主机名为c7.test
```
注：当主机名变化时，需要修改/etc/hosts文件，添加IP到主机名的映射，否则解析较慢。

**网络配置文件**

> /etc/sysconfig/network-scripts/ifcfg-eth0 //网卡配置文件
> /etc/hosts //主机配置文件
> /etc/rc.local //路由配置文件

## 软件安装
### 软件包管理器
包管理器是方便软件安装、卸载，解决软件依赖关系的重要工具
- CentOS、RedHat 使用 **yum** 包管理器，软件安装包格式为 **rpm**
- Debian、Ubuntu 使⽤ **apt** 包管理器，软件安装包格式为 **deb**
![在这里插入图片描述](https://img-blog.csdnimg.cn/5f8e6a08f41c4ab98c98cb595a2ff952.png)

**rpm使用步骤**
1. 挂载光盘，获取rpm包

```bash
ls /dev/sr0 -l //sr0为光盘，块设备
mount /dev/sr0 /mnt //将光盘挂载到/mut

//若要取消挂载使用：
umount /mut 或 umount /dev/sr0

ls /mnt/Packages //查看光盘中的rpm包
ls /mnt/Packages | more //分页查看光盘中的rpm包，按空格键下一页
mkdir /root/rpms //新建rpms文件夹
cp vim-common-7.1.10-el7.x86-64.rpm /root/rpms //复制rpm包到指定文件夹
```

3. 查询、安装、卸载rpm包

```bash
rpm -q vim-common //查询指定安装包
rpm -qa //查询全部安装包
rpm -i vim-common-7.1.10-el7.x86-64.rpm //安装软件安装包，需要安装包完整名称
rpm -e vim-common //卸载安装包
```
注：rpm安装需要手动安装依赖关系的安装包。
### yum包管理器
1. yum源
CentOS yum源
[http://mirror.centos.org/centos/7/](http://mirror.centos.org/centos/7/)
国内镜像
[https://opsx.alibaba.com/mirror](https://opsx.alibaba.com/mirror)
2. yum配置文件
/etc/yum.repos.d/CentOS-Base.repo
3. 修改yum配置文件

```bash
//备份
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
//下载新的yum源
wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
//更新缓存
yum makecache
```
4. 安装、卸载、升级软件包

```bash
yum install vim-enhanced //安装vim-enhanced并安装依赖关系
yum remove vim //卸载vim
yum list //查看已安装软件包
yum update //检查当前的全部安装包是否升级
```

### 源代码安装
以安装openresty软件为例：
> - wget https://openresty.org/download/openresty-1.15.8.1.tar.gz //下载软件包
> - tar -zxf openresty-VERSION.tar.gz
> - cd openresty-VERSION/ //进入软件包
> - ./configure --prefix=/usr/local/openresty //运行脚本，软件安装到指定目录
> - make -j2 //编译，-j2表示使用两个虚拟处理器
> - make install //安装

注：以上为通用安装步骤，个别软件略有不同。
### 升级内核
- 查看内核版本

```bash
uname -r
```

 - rpm 安装

```bash
 rpm -i kernel-3.10.0
```

 - yum 安装
 

```bash
yum install kernel-3.10.0
yum update
```

 - 源代码安装
 1. 安装依赖包

```bash
yum install gcc gcc-c++ make ncurses-devel openssl-devel elfutils-libelf-devel
```
2. 下载并解压内核
内核地址：[https://www.kernel.org](https://www.kernel.org)

```bash
tar xvf linux-5.1.10.tar.xz -C /usr/src/kernels
```
3. 配置内核编译参数

```bash
//1.使用新的内核配置
cd /usr/src/kernels/linux-5.1.10/
make menuconfig/allyesconfig/allnoconfig //分别表示：菜单选择配置，全部配置，全部不配置
//2.使用当前系统内核配置
cp /boot/config-3.10.0-957.el7.x86_64 /usr/src/kernels/ linux-5.1.10/.config
```
4. 编译

```bash
make -j2 all
```

5. 安装内核

```bash
make modules_install //先安装内核模块
make install //再安装内核
```
注：查看cpu信息：`lscup`
### grub配置文件
**grub**是启动引导软件，有grub1和grub2两个版本，现使用grub2。
grub2的配置文件：

> /boot/grub2/grub.cfg //一般不直接修改此文件

通常使用以下步骤修改：
1. 修改以下文件的grub配置

> /etc/default/grub  //grub初级配置
> /etc/grub.d  //grub详细配置
> 
![在这里插入图片描述](https://img-blog.csdnimg.cn/c37e25f4baa649b0b811567706c7fb2e.png)
- 其中GRUB_DEFAULT项配置启动时默认选择的内核

```bash
grub2-editenv list //查看saved指代的内核
grep ^menu /boot/grub2/grub.cfg //文件中查找内核
grub2-set-default 0 //修改saved指代的内核
```
- 其中GRUB_CMDLINE_LINUX中*rhgb*表示图形用户界面，*quiet*表示静默安装
2. 使用命令产生新的grub2文件

```bash
grub2-mkconfig -o /boot/grub2/grub.cfg
```

### 单用户进入系统（忘记root密码）
linux启动时，选择内核，按<kbd>e</kbd>进入编辑，找到

```bash
linux64 .x.x.x.x.x.x. 结尾添加single或rb.break_
```
<kbd>ctrl</kbd>+<kbd>x</kbd>开始

```bash
ls /sysroot //查看根目录
ls /  //查看系统根目录，不是实际根目录
mount -o remount,rw /sysroot //挂载根目录
chroot /sysroot 重新root
ls / //是实际根目录
echo 111111 //显示到屏幕
echo 111111 | passwd --stdin root //传递到root密码
```
最后`reboot`
## 进程管理
### 进程查看命令

 - ps
 

```bash
ps //当前终端的进程
ps -e //查看全部进程
ps -e | more //查看全部进程，分页显示
ps -ef //查看较详细进程信息
ps -eLF //查看最详细进程信息
```

 - pstree
 

```bash
pstree //显示进程树
```

 - top

```bash
top //动态显示进程信息
top -p 1234 //查看进程号为1234的进程信息
```
其中NI表示进程优先级

按<kbd>1</kbd>显示多核CPU状态
按<kbd>s</kbd>修改刷新时间
按<kbd>E</kbd>修改显示单位
按<kbd>?</kbd>x显示帮助
### 进程优先级与控制
- nice ：范围从-20到19，值越⼩优先级越⾼，抢占资源就越多

```bash
nice -n 10 ./a.sh //设置a.sh的优先级，进程未运行时
```

- renice：重新设置优先级

```bash
renice -n 15 1234 //设置1234（PID）的优先级，进程运行时
```
- &：进程后台运行
- jobs：获取后台进程编号
- fg：调到前台

```bash
./a.sh & //进程后台运行
jobs //获取后台进程编号
fg 1 //将编号为1的后台进程调到前台
bg 1 //将编号为1的进程调到后台运行
```
- 按<kbd>ctrl</kbd>+<kbd>z</kbd>将前台进程调入后台，并暂停。
### 进程通信
进程通信：管道，信号。

- kill -l //查看全部信号

-- SIGINT 通知前台进程组终⽌进程 ctrl + c

-- SIGKILL ⽴即结束程序，不能被阻塞和处理 kill -9 pid
### nohup与守护进程
- nohup
nohup 命令使进程忽略 hangup（挂起）信号

```bash
nohup ./a.sh & //关闭终端，进程不停止
```

- daemon（守护进程）
一种特殊的进程，没有控制终端也不和前台交互，一般用于服务的管理，通过对daemon进程发不同的信号，控制它对应的进程起停，无终端启动。父进程是1。
### screen环境
不受会话关闭影响
```bash
srceen //进入screen环境
screen -ls //查看 screen 的会话
screen -r 31557 //恢复31557会话
exit //退出screen环境
```
### 系统日志
/var/log
- messages 系统错误信息
- dmesg 内核相关信息
- cron 计划任务信息
- secure 系统安全信息
<kbd>ctrl</kbd>+<kbd>a</kbd><kbd>d</kbd> 退出 (detached) screen 环境

### 服务管理工具
- service
路径：/etc/init.d/
- systemctl
路劲：/usr/lib/systemd/system/

```bash
systemctl start xx.service 
systemctl stop xx.service
systemctl restart xx.service
systemctl reload xx.service
systemctl enable xx.service
systemctl disable xx.service
```

```bash
cd /lib/systemd/system  //服务文件目录
ls -l runlevel*.target  //查看服务级别，文件映射
systemctl get-default //获取当前启动级别
systemctl set-default multi-user.target //修改启动级别为字符界面
```


- systemctl 的服务配置
```powershell
[Unit]

Requires = 新的依赖服务

After = 新的依赖服务

[Service]

[Install]
```
### SELinux(安全增强)
- 查看 SELinux 的命令

```bash
getenforce //查看SELinux状态
setenforce 0 //临时改变状态
```
编辑/etc/selinux/config永久修改状态，reboot生效。

```bash
//查看安全信息
ps -Z 
ls -Z
id -Z
```

## 内存与磁盘管理
### 查看内存状态
- free

```bash
free //单位为k
free -m //单位为M
free -g //单位为G
```

- top
见上节进程查看命令
### 查看磁盘状态
- fdisk

```bash
fdisk -l //查看磁盘信息
parted -l //查看分区信息
ls -l /dev/sd? //查看磁盘类型，分块
ls -l /dev/sd?? //查看磁盘类型，分区
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/23ecc8d116df4db8abaf5c41673cd11a.png)
- df、du、dd

```bash
df -h //分区使用和挂载信息
ls -l /data/filename //查看文件大小，单位byte
ls -lh /data/filename //查看文件大小，显示单位,文件开始到结束的大小
du -h /data/filename //查看文件设计大小，显示单位，不计空洞文件
dd if=afile bs=4M count=10 of=bfile //复制afile到bfile，指定块大小为4M，数量为10
dd if=afile bs=4M count=10 seek=20 of=bfile //复制和转换一个文件，带空洞
```
### 文件系统
Linux支持的文件系统：

 - EXT4
 - XFS
 - NTFS（有版权，需额外软件）

****EXT4文件系统****
- 超级块
记录整个文件系统或分区的文件总信息
- 超级块副本
超级块的备份，不止一份
- i 节点(inode)
记录每个文件的名称，编号，大小，权限等

```bash
ls -l //查看文件权限等
ls -i //查看文件所属inode编号，第一个
```
文件名记录到父目录的inode中，其他信息记录在文件的inode中
- 数据块(datablock)
记录数据信息，挂在inode后，ls查看的是inode，du查的是datablock
### 文件命令过程

```bash
touch afile //新建普通文件
echo 123 > afile //将123写入afile；du、ls大小为4k，即一个datablock大小
cp afile bfile //复制aflie，afile的inode编号不变
mv afile cfile //同目录移动afile，inode编号不变，只是改名
mv afile /data/cfile //跨目录移动afile，inode编号改变
vim afile //修改文件内容，inode编号改变，新建inode，将文件名重新链接到新inode中
echo 123 > afile //修改文件内容，inode编号不变
rm afile //删除文件，只断开文件名和inode的链接
```
***链接***（硬链接及符号链接）
```bash
ln afile bfile //将bfile文件名链接到afile的inode上，硬链接，不能跨文件系统和分区
ln -s afile cfile //新建cfile的inode，cfile的inode中保存afile的路径，符号链接，可跨文件系统和分区
```
链接文件的权限是777，对链接文件的权限操作会加到原文件。
**facl**

```bash
getfacl afle //获取文件ACL权限
setfacl -m u:username:rw afile //赋予用户username对afile文件rw的ACL权限
setfacl -m g:groupname:rx afile //赋予用户组groupname对afile文件rx的ACL权限
setfacl -x u:username:w afile //回收用户username对afile文件w的ACL权限
```
### 分区与挂载
- fdisk（小于2T磁盘分区）

```bash
fdisk /dev/sdc //进入fdisk界面
```
![fdisk界面](https://img-blog.csdnimg.cn/fa03b5f855e544ccb319b917dc956065.png)

帮助：m

增加分区：n

![建主分区](https://img-blog.csdnimg.cn/deb77b6eb00442c28e1ca0ec825a15a5.png)

打印分区：p

保存分区：w

退出不保存：q

删除分区：d

- mkfs

```bash
mkfs[tab][tab] //查看可设置的文件系统类型
mkfs.ext4 /dev/sdc1 //将分区sdc1设置为ext4类型
```
- mkdir

```bash
mkdir /data //新建目录，用于挂载分区
```
- mount

```bash
mount // 查看mount信息
mount /dev/sdc1 /data //将分区sdc1挂载到/data目录,自动识别文件系统类型
mount -t ext4 /dev/sdc1 /data //将分区sdc1挂载到/data目录,指定文件系统类型为ext4
mount -t auto /dev/sdc1 /data //将分区sdc1挂载到/data目录,自动识别文件系统类型
```
总结：
 1. 添加磁盘 sdc
 2. 磁盘分区 sdc1
 3. 格式化 ext4
 4. 挂载 mount
 **注：仅临时保存在内存中，需在/etc/fstab文件中添加才能永久生效**
 在文件中添加一行：
```
/dev/sdc1 /data ext4 defaults 0 0 //被挂载点 挂载点 文件类型  . . .
```

 - parted（大于2T磁盘分区）
```bash
parted /dev/sdc //进入parted界面
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/ef70f0b5219d44978a8aee8c4f756931.png)

帮助：help
### xfs磁盘配额
 1. 首先格式化xfs分区 /dev/sdb1
 2. 挂载时指定用户配额，组配额
```bash
mount -o uquota, gquota /dev/sdb1 /mnt/data
```
 3. 目录赋权
```bash
chmod 1777 /mnt/data
```
 4. xfs_quota报告配额
```bash
xfs_quota -x -c ‘report -ugibh’ /mnt/data //报告u用户，g组，i节点的磁盘配额
```
 5. xfs_quota限制磁配额
```bash
xfs_quota -x -c ‘limit -u isoft=5 ihard=10 username’ /mnt/data //对u用户、g组，的i节点、b块，添加soft软限制、hard硬限制配额
```
### 交换分区
增加交换分区
 1. 新建分区 /dev/sdd1或 新建文件 /swapfile
```bash
dd if=/dev/zero bs=4M count=1024 of=/swapfile //建文件
```
 3. 格式化交换分区
```bash
mkswap /dev/sdd1
mkswap /swapfile
```
 4. 打开交换分区
```bash
swapon /dev/sdd1
```
5. 关闭交换分区

```bash
swapoff /dev/sdd1
```
 **注：仅临时保存在内存中，需在/etc/fstab文件中添加才能永久生效**
  在文件中添加一行：
```
/swapfile swap swap defaults 0 0 //被挂载点 虚拟挂载点 文件类型  . . .
```
### 磁盘阵列（RAID）
RAID 的常⻅级别及含义
- RAID 0 striping 条带⽅式，提⾼单盘吞吐率，至少两块硬盘
- RAID 1 mirroring 镜像⽅式，提⾼可靠性，至少两块硬盘
- RAID 5 有奇偶校验，至少三块硬盘
- RAID 10 是RAID 1 与 RAID 0 的结合

RAID有软件RAID（mdadm）和硬件RAID两种实现方式
### 逻辑卷（LVM）
逻辑卷在磁盘上层。
一、新建逻辑卷
1. 添加三个硬盘并分区
2. 组成物理卷
```bash
pvcreate /dev/sdb1 /dev/sdc1 /dev/sdd1 //创建物理卷
pvs //查看物理卷
```
3. 创建卷组
```bash
vgcreate vgname /dev/sdb1 /dev/sdc1 //创建卷组
vgs //查看卷组
```
4. 创建逻辑卷
```bash
lvcreate -L 100M -n lvname vgname //在卷组vgname中添加逻辑卷lvname，大小为100M
lvs //查看逻辑卷
```
5. 格式化
```bash
mkfs.xfs /dev/vgname/lvname //格式化逻辑卷lvname
```
6. 挂载
```bash
mount /dev/vgname/lvname /data //挂载逻辑卷lvname到目录/data
```
 **注：仅临时保存在内存中，需在/etc/fstab文件中添加才能永久生效**
  在文件中添加一行：
```
/dev/vgname/lvname /data ext4 defaults 0 0 //被挂载点 挂载点 文件类型  . . .
```
二、扩展逻辑卷
```bash
vgextend vgname /dev/sdd1 //扩大卷组
lvextend -L +50G /dev/vgname/lvname //扩大逻辑卷
xfs_growfs /dev/vgname/lvname //扩大文件系统中逻辑卷
```
### 系统综合状态
- ifstat iftop //网络状况
- iostat iotop //io状况
- vmstat
- pidstat
- uptime
- w
```bash
sar -u 1 10 //查看cup状态，每隔1秒采样一次，采样10次
sar -r 2 20 //查看内存状态，每隔2秒采样一次，采样20次
sar -b 1 10 //查看IO状态，每隔1秒采样一次，采样10次
sar -d 1 10 //查看磁盘状态，每隔1秒采样一次，采样10次
sar -q 1 10 //查看进程状态，每隔1秒采样一次，采样10次
```
# 补充

```bash
chkconfig --list
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/73242ec0f5414ecb8c194c7d5fbe62f7.png)
# 附加知识
 一、下有3个特殊的进程，idle进程(PID = 0), init进程或systemd进程(PID = 1)和kthreadd(PID = 2)

idle进程由系统自动创建, 运行在内核态。

idle进程其pid=0，其前身是系统创建的第一个进程，也是唯一一个没有通过fork或者kernel_thread产生的进程。完成加载系统后，演变为进程调度、交换。

init进程由idle通过kernel_thread创建，在内核空间完成初始化后, 加载init程序, 并最终用户空间。

由0进程创建，完成系统的初始化. 是系统中所有其它用户进程的祖先进程
Linux中的所有进程都是有init进程创建并运行的。首先Linux内核启动，然后在用户空间中启动init进程，再启动其他系统进程。在系统启动完成完成后，init将变为守护进程监视系统其他进程。

二、终端，泛指一切能控制计算机的输入接口，包括串口线的硬件终端、也包括类似windows的图形界面，默认运行linux服务器的字符界面；而在Linux字符界面上，软件终端又分成标准终端设备tty1-tty6 ，和虚拟终端pts 。像是我们使用的ssh连接的终端、在图形界面打开的终端程序，都属于虚拟终端，它们和tty设备的区别就是没有设备编号，所以叫做“虚拟”终端


