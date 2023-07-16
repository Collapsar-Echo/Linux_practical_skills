@[TOC](第五章 文本操作篇)
# Linux文本操作

这是Linux的正则表达式，find，grep，awk，sed。
## 正则表达式
### 元字符
- `.` 匹配除换行符外的任意单个字符
```bash
grep pass.... //匹配pass后任意四个字符
```
- `*` 匹配它前面的字符0次和多次，**需与通配符*区分**
```bash
grep pas* //匹配pa pas
```
- `[]` 匹配方括号中的字符类中的任意一个
```bash
grep pass[Hh] //匹配H,h
```
- `^` 匹配开头
```bash
grep ^p //匹配以p开头
```
- `$` 匹配结尾
```bash
grep pass....$ //匹配pass后任意四个字符结尾
```
- `\` 转义后面的特殊字符
```bash
grep "\." //匹配.
```
### 拓展元字符
- `+` 匹配前面的正则表达式至少出现一次
- `?` 匹配前面的正则表达式出现零次或一次
- `|` 匹配它前面或后面的正则表达式
## 文本搜索
### 文件查找
- find
find 路径 查找条件 [补充条件]
```bash
find passwd //查找精确passwd
find /etc -name passwd //查找/etc下文件名passwd
```
### 文本内容查找
- grep
grep 选项 文本文件1 [ … 文本文件n ]

```bash
grep -i afile //忽略大小写
grep -r /data //递归查找
```
## 行编辑器
- sed
- awk

用不上，没学。。。

