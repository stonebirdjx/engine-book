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

# Shell注释
```bash
# tips 单行注释

# 单行注释 EOF 可以换成其他符合
:<<EOF
注释内容...
注释内容...
注释内容...
EOF
```

# Shell换行
代码太长 可以使用 \ 进行跨行编写

在 '', "" , () , {} ,`` 里面的内容可以直接跨行输入

多行命令写成一行，用 ; 隔开

# Shell变量
定义变量 = 两边不能有空格，注意命令规范使用单词数字下划线（lower_with_under）的形式
```bash
file_name="stdout.log"
# 取值
$file_name # 不建议使用
${file_name} # 建议使用
```
> shell变量类型都是字符串
>
> shell只有全局变量，注意值的可靠性，不要取到被更改的值
>
> 为了防止边界问题，建议统一使用${var}的格式

## 使用 $() 或者 `` 进行语句赋值
```bash
file_name=`cat config|grep file_name|awk -F = '{print $2}'`
file_name=$(cat config|grep file_name|awk -F = '{print $2})
```
## 只读和删除
```bash
# 只读变量 readonly 
name="stonebird"
readonly name
# 删除变量 unset
unset name
```
> 只读变量不能被更改，删除变量不会影响其他变量

## '' 和 "" 的区别
```bash
# '' 原样输出
suff='bird'
name='stone${suff}' # stone${suff}

# "" 支持转义 ${} \n \t \r
name="stone${suff}" # stonebird
```
# 字符串操作
## 拼接
shell 拼接直接使用'' 或者 "" 拼接标准输出即可
```bash
suff="bird"
name="stone'${suff}'ya'
name="stone${suff}ya"
```
## 字符串长度
使用${#var}获取长度
```bash
name="stonebird"
echo "${#name}"   # 9
```
## 提取字符串
使用${string:start:end}获取子字符串，索引从0开始
```bash
name="stonebird"
echo "${name:1:3}" # ton
```
## expr 对字符串的其他操作
- expr match	中匹配 REGEXP 字符串并返回匹配字符串的长度
- expr substr	从 POS 位置获取长度为 LENGTH 的字符串
- expr index	杳找子字符串的起始位置
- expr length	计算字符串的长度
```bash
expr match "123 456 789" ".*5"  # 6 匹配字符串的长度，若找不到则返回 0
expr substr " this is a test" 3 5 # his i 
expr index "test for the game" "e" # 2 
expr length "this is a test" # 14
```
