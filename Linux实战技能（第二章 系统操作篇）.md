@[TOC](第二章 系统操作篇)
# Linux的帮助、文件和用户管理
这是Linux的帮助命令和文件管理相关命令，以及vim编辑器的使用、用户管理
## 万能的帮助命令
### man
man 是 manual 的缩写
- man 帮助示例

```bash
man ls
man 5 passwd 
man -a passwd 
```

- man 也是一条命令，分为 9 章，可以使⽤用 man 命令获得 man 的帮助

```bash
man 7 man
```

> 手册章节传统上如下定义：
> 1. Commands 
 	用户可从shell运行的命令
> 2. System calls
 	必须由内核完成的功能
> 3. Library calls
 	大多数libc函数，例如qsort(3)
> 4. Special files
 	/dev 目录中的文件
> 5. File formats and conventions
 	/etc/passwd 等人类可读的文件的格式说明
> 6. Games
> 7. Macro packages and conventions
 	文件系统标准描述，网络协议，ASCII和其他字符集，还有你眼前这份文档以及其他东西
> 8. System management commands
 	类似 mount(8) 等命令，大部分只能由 root 执行
> 9. Kernel routines
 	这是废弃的章节。
 	原来曾想把一些核心的文件放在这里，但实际只有极少数文件在这里。
 	
***为什么要分为9章？***
为了区分同名的命令或文件等。
### help
1. **内部命令和外部命令**
	内部命令：shell命令解释自带的命令
	外部命令：其他命令
	区分:	
	
```bash
 //内部命令
type cd
cd 是 shell 的内嵌命令

 //外部命令
type ls
ls 是‘ls --color=auto’的别名
```

 - **help的使用**
 - 内部命令使⽤ help 帮助

```bash
help cd
```

 - 外部命令使用help帮助

```bash
ls --help
```

### info
info 帮助比 help 更更详细，作为 help 的补充

```bash
info ls
```

## 一切皆文件
### 文件操作命令
- **pwd** 显示当前的⽬录名称
- **ls**  查看当前目录下的文件
常用参数:
-l ⻓格式显示文件 
-a 显示隐藏文件
-d 仅显示当前指定目录，不显示下级目录
-r 逆序显示
-t 按照时间顺序显示 
-R 递归显示

示例：

```bash
ls -l //长格式显示
ls -l -r //长格式逆续显示（按文件名逆续）
ls -l -r -t //长格式逆续显示（按时间逆续）
ls -R //递归显示
ls -lrtR //简写，不区分前后顺序
ls -h //显示单位
```
- **cd** 更改当前的操作目录

示例：

```bash
cd . //前往当前目录
cd .. //前往上一级目录
cd - //返回前一个目录
```
技巧：<kbd>tab</kbd>可以自动补全路径
- **mkdir** 建立目录
常用参数：
-p 建⽴多级目录

示例：

```bash
mkdir /dira //单级目录
mkdir -p /dira/dirb/dirc //多级目录
```
- **rmdir**  删除空目录
- **rm** 删除文件或非空目录
常用参数：
-r 删除目录(包括⽬录下的所有文件)
-f 删除文件不进行提示

示例：

```bash
rm -r /dira //删除目录，有确认，ctrl+c结束确认
rm /filea //删除文件
rm -rf //删除目录，不提示，**慎用**
```
- **cp** 复制文件和目录
常用参数：
-r 复制目录
-p 保留用户、权限、时间等文件属性
-v 显示复制过程
-a 相较于-p可以递归复制

示例：

```bash
cp /filea /dira //复制文件
cp -r /dirb /dira //复制目录
```
- **mv** 移动文件

示例：

```bash
mv /filea /fileb //文件重命令
mv /dira/filea /fileb //移动文件，并重命名
mv /dira /tmp //移动目录
```
### 通配符
 - 定义:shell 内建的符号 ⽤
 - 用途:操作多个相似(有简单规律)的⽂件 
 - 常用通配符：
**\*** 匹配任何字符串
**?** 匹配1个字符
**[xyz]** 匹配xyz任意一个字符 
**[a-z]** 匹配一个范围
**[!xyz] 或 [\^xyz]**  不匹配
### 文本查看
 - **cat** 文本内容显示到终端
 - **head** 查看文本开头，默认10行
 示例：

```bash
head -5 //查看文本前5行
```

 - **tail** 查看文本结尾，默认10行
常用参数：
-f  实时显示文本内容
- **wc** 统计文本内容信息
常用参数：
-w word，字数
-l line，行数
-c character，字符数
-b byte，字节数

示例：

```bash
wc -l //行数
```
 - **more** 分屏查看内容，<kbd>blankspeace</kbd>下一页
 - **less** 分屏查看内容，<kbd>DownPage</kbd>下一页，<kbd>UpPage</kbd>上一页
### 打包和压缩
 - **tar** 打包命令
常⽤参数：
c 打包
x 解包
f 指定操作类型为⽂件
- **gzip** 和 **bzip2**   压缩命令
命令可单独操作 ，通常和 tar 命令配合操作
常⽤参数:
-z gzip 格式压缩和解压缩
-j bzip2 格式压缩和解压缩

**经常使⽤用的扩展名是 *.tar.gz .tar.bz2 .tgz***
示例：

```bash
tar cf /dira/etc-backup.tar /etc //将etc目录打包
tar czf /dira/etc-backup.tar.gz /etc //将etc目录打包压缩为.tar.gz
tar cjf /dira/etc-backup.tar.bz2 /etc //将etc目录打包压缩为.tar.bz2

tar xf /etc-backup.tar -C /tmp //将etc-backup.tar分包到tmp目录
tar zxf /etc-backup.tar.gz -C /tmp //将etc-backup.tar.gz解压缩到tmp目录
tar jxf /etc-backup.tar.bz2 -C /tmp //将etc-backup.tar.bz2解压缩到tmp目录

tar zxvf /etc-backup.tar.gz -C /tmp //将etc-backup.tar.gz解压缩到tmp目录,并显示进度
```
注：czf的参数f位置不能变，f表示文件，后面要跟文件名。
## vim
**vi**或**vim**进入编辑器

### 模式切换：

> **i I a A o O** 进入插入模式
> **v V ctrl+v** 进入可视化模式
> **:** 进入命令模式
> **esc** 从其他模式回到正常模式

### 正常模式 (Normal-mode) 
1. 光标移动
* h 左⬅️
* l 右➡️
* j 下⬇️
* k 上⬆️
* G 移动到指定行
> 11G 移动到11行
* g 移动到第一行
* G 移动到最后一行
* ^ 移动到当前行首
* $ 移动到当前行尾
 2. 复制与粘贴
 
- **y 复制**

>  yy  复制单行   
>  3yy 复制包括当前行的下3行
>  y$ 复制当前位置到这行结尾

- **d 剪切**

> dd 剪切单行 
> d$ 剪切当前位置到这行结尾

- **p 粘贴** 

> p 粘贴单行 
> 3p 粘贴3行

- **u 撤销** 
- **ctrl + r 重做**

3. 删除与替换
 **x  删除单个字符**
 **r 替换单个字符**

### 插入模式 (Insert-mode) 
### 命令模式 (Command-mode) 

> `:w` 写入，后可接文件路径 
> `:q` 退出 
> `:!` 执行Shell 命令  如`:!ipconfig` 表示临时查看命令
> `:s/old charater/new charater` 替换 如 `:s/x/X` 表示x替换为X
> `/charater` 查找 如`\x` 表示查找x
> :set 设置命令  如:set nu 暂时设置行号

补充：
vim永久设置行号：

```bash
vim /etc/vimrc 添加set nu //全部用户生效
vim ~.vimrc 添加set nu //当前用户生效
```

### 可视模式 (Visual-mode)

> v 字符可视模式 
> V 行可视模式 
> ctrl+v 块可视模式

与d（删除）和I（大写的i，插入）联合使用
## 用户和权限管理
### 用户管理
- **useradd** 新建用户

```bash
useradd user1 //新建用户，默认创建同名用户组
useradd -g group1 user1 //新建用户，并指定用户组
```

- **userdel** 删除用户

```bash
userdel user1
```

- **passwd** 修改用户密码

```bash
passwd user1 //仅删除用户
passwd -r user1 //删除用户，同时删除/home目录下的用户目录
```

- **usermod** 修改用户权限

```bash
usermod -d /home/user1_change //修改用户家目录
usermod -g group1 user1 //修改用户user1的用户组为group1
```

- **chage** 修改用户属性
- **id** 查看用户的uid,gid,组

```bash
id user1 //查看用户的uid,gid,组
```

### 用户组管理
- **groupadd** 新建⽤户组

```bash
groupadd group1
```

- **groupdel** 删除⽤户组

```bash
groupdel group1
```

### 用户切换
- **su** 切换用户

```bash
su - user1 //切换用户，同时切换用户环境
su - root //切换到root用户
su user1 //仅切换用户
```

 - **sudo** 以其他用户身份执行命令
 - 使用sudo前需要使用`visudo`命令，打开文件并修改相应内容
 - sudo执行命令时需要使用命令的完整路径
### 用户配置文件
**/etc/passwd** 用户配置文件
 `user1:x:1001:1001::/home/user1:/bin/bash`
> 用户配置有7个字段：
>  - 用户名
>  - x 表示需要密码，否则为空
>  - uid
>  - gid
>  - 注释，可为空
>  - 用户家目录
>  - 用户登陆的命令解释器，/bin/bash（bash终端登陆）或/sbin/nologin(不允许终端登陆)

**/etc/shadow** ⽤户密码相关配置文件
 `user1:$fwhfuhewoh$jfkjhfheh$hhsfjsh$fhehfk:18049:0:99999:7:::`
>  - 用户名
>  - 密码，保密保存，相同密码也不一样

**/etc/group** ⽤户组配置文件
`group1:x:1001:`

> 配置文件有4个字段：
> - 用户组名
> - x 表示需要密码，否则为空
> - gid
> - 用户名，此用户的其他组

### 文件（目录）权限
#### 权限说明
![](https://img-blog.csdnimg.cn/e01ea35bb14b40e18730244afaae1967.png)
1. 文件类型

>  - \- 普通文件 
>  - d 目录文件 
>  - b 块特殊文件，其实是设备
>  - c 字符特殊文件，其实是设备
>  - l 符号链接，link链接 
>  - f 命名管道 
>  - s 套接字文件
2. 权限表示方法
> r = 4	读  
> w = 2	写 
> x = 1 	执⾏

![](https://img-blog.csdnimg.cn/f9b0db3f8e9e42f683b8afbc7d76eeb5.png)
当属主权限与属组权限冲突时，以属主权限为主。
3. 目录权限
> x 进入目录 
> rx 显示目录内的文件名  
> wx 修改目录内的文件名
#### 权限修改
- chmod 修改文件、目录权限

```bash
chmod u+x /tmp/testfile //属主增加执行权限
chmod g-w /tmp/testfile //属组减少写权限
chmod o=rx /tmp/testfile //其他用户权限改为读，执行
chmod a=rwx /tmp/testfile //全部用户权限改为读，写，执行
chmod 755 /tmp/testfile //用数字修改属主，属组，其他用户权限
```
其中u=user, g=group, o=other, a=all
- chown 更改属主、属组

```bash
chown user2 /test //修改目录属主为user2
chown :group2 /test //修改目录属组为group2
chown user2:group2 /test //同时修改目录属主和属组
```
 - chgrp 可以单独更改属组，不常⽤
#### 特殊权限
 - SUID 用于二进制可执行文件，执行命令时取得文件属主权限 
 如 /usr/bin/passwd
 - SGID 用于目录，在该目录下创建新的文件和目录，权限自动更改为该目录的属组
 - SBIT 用于目录，该目录下新建的文件和目录，仅 root 和自己可以删除
如 /tmp
# 补充
- **touch** 创建普通文本文件

```bash
touch /test/afile //新建afile文本文件
```

- **echo** 显示或写入

```bash
echo 123 //123显示到终端
echo 123 > afile //123写入afile文件，原有内容会被覆盖
```
- <kbd>ctrl</kbd>+<kbd>r</kbd> 查找历史命令
