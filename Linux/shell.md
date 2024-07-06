# Shell:第一门编程语言
shell 是一个命令行解释器，它由一个解释器程序和一个命令行解释器程序组成。
shell 脚本的后缀名是.sh
```bash
bootstrap.sh
```
# 第一个程序
```bash
#!/bin/bash 
echo "hello world"
```
执行
```bash
chmod +x bootstrap.sh
./bootstrap.sh 
"hello world"
```
> 到此，恭喜你已经成为一个优秀的程序员了

# #! 约定解释器
#! 是一个约定的标记，它告诉系统这个脚本需要什么解释器来执行
```bash
#!/bin/bash 
    ./bootstrap.sh  => bash bootstrap.sh
#!/bin/sh => sh bootstrap.sh
    ./bootstrap.sh  => sh bootstrap.sh
#!/usr/bin/python3 => python3 bootstrap.py
    ./bootstrap.py  => python3 bootstrap.py
```
> 不指定解释器可以手动执行 bash bootstrap.sh
>
> 不指定解释器 直接执行会被当成shell文件

# Shell种类
`cat /etc/shells` 查看当前环境可以使用的shell
```bash
cho "$SHELL" # 查看当前登录系统shell
# 使用chsh 更改shell
chsh -s /bin/bash # 更改当初shell为bash
```
> Linux默认shell 建议使用bash。
>
> MacOS默认shell 为zsh