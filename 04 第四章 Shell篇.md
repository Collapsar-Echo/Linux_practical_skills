@[TOC](第四章 Shell篇)
# Shell脚本

这是Shell脚本的语法知识。
## 认识shell
### 什么是shell
Shell 是命令解释器，用于解释用户对操作系统的操作。
```bash
cat /etc/shells //查看shell类型
```
Linux 的 Shell 种类众多，常见的有：
 - Bourne Shell（/usr/bin/sh或/bin/sh）
 - Bourne Again Shell（/bin/bash）
 -  C Shell（/usr/bin/csh）
 -  K Shell（/usr/bin/ksh）
 -  Shell for Root（/sbin/sh）
### Linux启动过程
BIOS - MBR - BootLoader(grub) - kernel - init或systemd - 系统初始化 - shell
### shell脚本
UNIX 的哲学：一条命令只做一件事
demo.sh
```powershell
#!/bin/bash 
# demo 注释
cd /data/
ls
pwd
```
`#!` 是一个约定的标记（Sha-Bang），它告诉系统这个脚本需要什么解释器来执行。

```bash
chmod u+rx demo.sh //赋权

//子进程执行，不会改变当前环境
bash demo.sh //可不需可执行权限
./demo.sh //必须可执行权限

//当前进程执行，会改变当前环境
source ./demo.sh
. filename.sh
```
- 内建命令不需要创建子进程
- 内建命令对当前 Shell 生效
## 管道与重定向
### 管道
匿名管道（管道符）`|` 是 Shell 编程经常用到的通信工具，将前一个命令执行的结果传递给后面的命令。
例如：

```bash
ls -l | more
more filename
cat filename | more //分页显示文件内容
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/f56e09039059455b8f14e1d713ec7844.png)

管道符会将两侧命令（外建命令）建两个子进程，如果是内建命令，子进程的修改，不会传递到父进程。

外部命令会产生子进程，管道符同样会产生子进程。内部命令在shell当前进程运行，不会产生子进程。

在管道符两端放置内部命令，相当于打开了新的子shell。内部命令执行结束之后，子shell也会跟着一起结束。因此在管道符两端放置内部命令，对当前的shell是不生效的。
### 重定向

一个进程默认会打开标准输入、标准输出、错误输出三个文件描述符。
默认的标准输入是键盘，默认的标准输出是屏幕，默认的错误输出是屏幕，而改变这个行为就是重定向。
- < 输入重定向
- \> 输出重定向
- \>> 追加重定向
- 2> 错误重定向
- &> 全部重定向
- | 管道重定向
- &1 引用重定向

由于引用重定向“&1”的存在，会使得下面几个命令等价：
command > file 2> file（要打开两次file，效率较低）
command 2> file > file（要打开两次file，效率较低）
command > file 2> &1（只打开一次file，效率较高）
command 2> file > &1（只打开一次file，效率较高）
command &> file (只打开一次file，效率最高)
## 变量
### 变量定义
变量名的命名规则
- 字母、数字、下划线
- 不以数字开头
### 变量操作
#### 赋值
```bash
a=123 //等号左右侧不能有空格，有空格会被认为是命令
let a=10+20 //let赋值，可计算
l=ls //将命令赋值给变量
string="hello bash" //将字符串赋值给变量
c=$(ls-l /etc) //将命令结果赋值给变量
c='ls-l /etc' //将命令结果赋值给变量
```
空格等特殊字符可以包含在`""`或`''`中
#### 引用
- ${变量名} 为变量的引用
- echo ${变量名}  查看变量值，部分情况可省略为 $变量名
#### 作用范围
- 默认作用范围为当前终端，不跨子父终端。
- export 导出，使子进程可以访问父进程变量
```bash
export demo_string
```
- unset 删除

```bash
unset demo_string
```
### 环境变量
环境变量：每一个终端都可以获得的变量
- env 查看环境变量
- set 查看更多环境变量

```bash
echo $PATH //当前终端的搜索路径
echo $PS1 //当前终端的提示信息
echo $? //查看上一条命令是否正确执行
echo $! //
echo $$ //显示当前进程的pid
echo $0 //显示当前进程名称
```
- 位置参数
$1 $2 $3 .... ${10} 

`${2-_}`  //如果第二个参数无值，则结果为_
- 配置文件
- - /etc下保存全部用户配置
- - ~/下保存对应用户配置
- - login用户使用四个配置，no-login使用bashrc
> /etc/profile 
> /etc/bashrc 
> ~/.bashrc 
> ~/.bash_profile 
> /etc/profile.d/

login 模式下加载顺序为 /etc/profile ~/.bash_profile ~/.bashrc /etc/bashrc 
no-login 模式下加载顺序为  ~/.bashrc   /etc/bashrc
### 数组
- 定义数组
```powershell
IPTS=(112 324 112 54 2544)
```
- 显示数组全部元素

```powershell
echo ${IPTS[@]}
echo ${IPTS[*]}
```

- 显示数字元素个数

```powershell
echo ${#IPTS[@]}
echo ${#IPTS[*]}
```

- 显示第n个元素

```powershell
echo ${IPTV[N]}
```
## 转义与引用 
### 特殊字符
一个字符不仅有字面意义，还有元意（meta-meaning）
- `#` 注释
- `;` 分号
- `\` 转义符号

 `\n` `\r` `\t` 单个字母的转义
 
`\$` ` \” ` `\\` 单个非字母的转义

- `"` 和`'` 引号

![在这里插入图片描述](https://img-blog.csdnimg.cn/6624205f8b294c98b0d1f7d6abc534e5.png)

### 运算符（前后有空格）
- 赋值运算符
`=` 用于算数赋值和字符串赋值
- 算数运算符
`+` `-` `*` `/` `**` `%`

```powershell
c=`expr 4 + 5` //反引号
```

- 数字常量
 let “变量名 = 变量值”
 变量值使用 0 开头为八进制
 变量值使用 0x 开头为十六进制
- 双圆括号
 双圆括号是 let 命令的简化
`(( a = 10 ))`
`(( a++ ))`
`echo $((10+20))`
### test（判断）

```powershell
test expression
```
当 test 判断 expression 成立时，退出状态为 0，否则为非 0 值。
test 命令也可以简写为[]：

```powershell
[ expression ]
```
注：[]和expression之间的空格，这两个空格是必须的，否则会导致语法错误。
- 文件类型判断

```powershell
-b filename	判断文件是否存在，并且是否为块设备文件。
-c filename	判断文件是否存在，并且是否为字符设备文件。
-d filename	判断文件是否存在，并且是否为目录文件。
-e filename	判断文件是否存在。
-f filename	判断文件是否存在，井且是否为普通文件。
-L filename	判断文件是否存在，并且是否为符号链接文件。
-p filename	判断文件是否存在，并且是否为管道文件。
-s filename	判断文件是否存在，并且是否为非空。
-S filename	判断该文件是否存在，并且是否为套接字文件。
```

- 文件权限判断

```powershell
-r filename	判断文件是否存在，并且是否拥有读权限。
-w filename	判断文件是否存在，并且是否拥有写权限。
-x filename	判断文件是否存在，并且是否拥有执行权限。
-u filename	判断文件是否存在，并且是否拥有 SUID 权限。
-g filename	判断文件是否存在，并且是否拥有 SGID 权限。
-k filename	判断该文件是否存在，并且是否拥有 SBIT 权限。
```

- 文件比较

```powershell
filename1 -nt filename2	判断 filename1 的修改时间是否比 filename2 的新。
filename1 -ot filename2	判断 filename1 的修改时间是否比 filename2 的旧。
filename1 -ef filename2	判断 filename1 是否和 filename2 的 inode 号一致。
```
- 数值比较

```powershell
num1 -eq num2	判断 num1 是否和 num2 相等。
num1 -ne num2	判断 num1 是否和 num2 不相等。
num1 -gt num2	判断 num1 是否大于 num2。
num1 -lt num2	判断 num1 是否小于 num2。
num1 -ge num2	判断 num1 是否大于等于 num2。
num1 -le num2	判断 num1 是否小于等于 num2。
```
- 字符串比较

```bash
-z str	判断字符串 str 是否为空。
-n str	判断宇符串 str 是否为非空。
str1 = str2
str1 == str2	=和==是等价的，都用来判断 str1 是否和 str2 相等。
str1 != str2	判断 str1 是否和 str2 不相等。
str1 \> str2	判断 str1 是否大于 str2。\>是>的转义字符，这样写是为了防止>被误认为成重定向运算符。
str1 \< str2	判断 str1 是否小于 str2。同样，\<也是转义字符。
```
- 逻辑判断

```bash
expression1 -a expression	逻辑与，表达式 expression1 和 expression2 都成立，最终的结果才是成立的。
expression1 -o expression2	逻辑或，表达式 expression1 和 expression2 有一个成立，最终的结果就成立。
!expression	逻辑非，对 expression 进行取反。
```
注：当你在 test 命令中使用变量时，我强烈建议将变量用双引号""包围起来
### 流程控制

```powershell
if condition
then
    command1 
    command2
    ...
    commandN
else
    command
fi
```

```powershell
case 值 in
模式1)
    command1
    command2
    ...
    commandN
    ;;
模式2)
    command1
    command2
    ...
    commandN
    ;;
esac

```

```powershell
for var in item1 item2 ... itemN
do
    command1
    command2
    ...
    if condition
	then
		break
	fi
    commandN
done
```

```powershell
while condition
do
    command
    if condition
	then
		continue
	fi
done
```

```powershell
until condition
do
    command
done
```
### 函数

```powershell
funname(){
	local var	//局部变量
    action;
    echo $1 $2 $3 … $n //参数
    [return int]
}
funname 1 2 3
```

```powershell
#!/bin/bash
# signal demo
trap "echo sig 15" 15
trap "echo sig 2" 2

echo $$

while :
do
  :
done
```
kill 默认发送15号信号；
ctrl + c 默认发送2号信号；
利用trap信号捕获，可以防止脚本被kill（除了用kill -9以外）
### 系统脚本
系统自建函数库

```bash
/etc/init.d/functions
```
使用 source 函数脚本文件“导入”函数
### 计划任务
- 一次性计划任务 at

```bash
at 18:12 //在18：12执行
atq //查看一次性任务
```

- 周期性计划任务 cron

```bash
crontab -e //配置任务
crontab -l //查看任务
min hour data mon week //配置格式
```
注：命令的路径问题
- 计划任务加锁 

```bash
anacontab 延时计划任务
flock 锁文件
```

# 补充
一、

进入子shell：bash

回到父shell：exit

二、

su - username //login用户

su username //no-login用户

