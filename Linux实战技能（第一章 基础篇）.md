@[TOC](第一章 基础篇)
# Linux背景知识
这是Linux的背景知识和虚拟机的安装。
## 什么是Linux？
1.一种是**Linus**（人名）编写的开源操作系统的内核
2.一种是广义的操作系统
## Linux版本
### 内核版本
[https://www.kernel.org/](https://www.kernel.org/)
![在这里插入图片描述](https://img-blog.csdnimg.cn/377d434fe3474d6fa8041a2e7e37bad3.png)

**版本号**：主版本号.次版本号.末版本号
**次版本号**是奇数为开发版，偶数为稳定版（2.6以后不再区分）
### 发行版本
*Redhat  Fedora  Centos  Debian  Ubuntu*
## Linux安装
虚拟机：VirtualBox
Linux系统：Centos

 - 安装Centos时有<kbd>软件选择</kbd>，其中选择**GNOME桌面**，打开Linux就为图形化终端。
 - 虚拟机和主机之间鼠标和键盘的切换：⬇️+left<kbd>command</kbd>
## 登陆Linux
### 终端
 - 图形化终端
 - 命令行终端
 - 远程终端（SSH、VNC）
 
图形化终端切换到命令行终端：init + 3
命令行终端切换到图形化终端：init + 5
```javascript
// 切换终端
init 3
init 5
```
### 多用户：
 - 普通用户
 - root用户
### 常见目录
 - `/` 根目录
 - `/root` root用户的根目录
 - `/home/username` 普通用户的根目录
 - `/etc` 配置文件目录
 - `/bin` 命令目录
 - `/sbin` 管理命令目录
 - `/usr` 系统相关的目录
 - `/usr/bin  /usr/sbin` 系统预装的其他命令
 
设计目录结构是要遵守一个  FHS（ Filesystem Hierarchy Standard） 约定的，即大家开发一个新的命令，该放在哪个目录，让使用的人看到后会更整洁。
 FHS: [https://zh.wikipedia.org/wiki/文件系统层次结构标准](https://zh.wikipedia.org/wiki/%E6%96%87%E4%BB%B6%E7%B3%BB%E7%BB%9F%E5%B1%82%E6%AC%A1%E7%BB%93%E6%9E%84%E6%A0%87%E5%87%86)
# 补充
1. 初始化命令：
```javascript
init 0 //关机
init 1 //切换到单用户root
init 2 //切换到多用户，不能使用net file system
init 3 //切换到命令行终端
init 4 //未知
init 5 //切换到图形化终端
init 6 //重启
```
2. 设置默认启动模式
- 查看当前启动模式

```bash
systemctl get-default
```

- 修改启动模式为终端模式

```bash
systemctl set-default multi-user.target
```

- 修改启动模式为图形化模式

```bash
systemctl set-default grphical.target
```

# 附加知识
一. init是Linux系统操作中不可缺少的程序之一。

　　所谓的init进程，它是一个由内核启动的用户级进程。

　　内核自行启动（已经被载入内存，开始运行，并已初始化所有的设备驱动程序和数据结构等）之后，就通过启动一个用户级程序init的方式，完成引导进程。所以,init始终是第一个进程（其进程编号始终为1）。

　　内核会在过去曾使用过init的几个地方查找它，它的正确位置（对Linux系统来说）是/sbin/init。如果内核找不到init，它就会试着运行/bin/sh，如果运行失败，系统的启动也会失败。
 
二. init一共分为7个级别，这7个级别的所代表的含义如下

0：停机或者关机（千万不能将initdefault设置为0）
1：单用户模式，只root用户进行维护
2：多用户模式，不能使用NFS(Net File System)
3：完全多用户模式（标准的运行级别）
4：安全模式
5：图形化（即图形界面）
6：重启（千万不要把initdefault设置为6）

其实，可以通过查看/etc/rc.d/中的rc*.d的文件来对比理解

