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

# Shell参数传递
```bash
$0  #当前脚本的文件名
$n(n>=1) #传递给脚本或函数的参数
$# #传递给脚本或函数的参数个数
$* #传递给脚本或函数所有参数 不可迭代
$@ #传递给脚本或函数所有参数 ，当参数有""时 $@和$*有所不同 “$*”会把所以参数当成一个 “$@”会迭代
$? #上一条命令的执行结果，如果是执行的命令是0正常 1异常 ,return 0代表函数正常终止,其他异常终止
$$ #显示当前shell的进程ID
$- #显示Shell使用的当前选项，与set命令功能相同，不常用。

./bootstrap.sh "hello" "world"
echo $0 # bootstrap.sh 
echo $1 # hello
echo $2 # world
echo $? # 0 

function test(){
    echo $1 # hello
    echo $2 # world
    echo $? # 0 
}
test "hello" "world"
```
> 参数传递可以在脚本或者函数中传递

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
## 提取字符串子串
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

# Shell数组
shell数组使用()定义
```bash
array=(1 2 3 4 5)
# 或者
array=(
    ele0 
    ele1 
    ele2 
    ele3
)
```
# 使用下标赋值
```bash
array=()
array[3]="ele3" # 直接给索引赋值不会报错
echo ${array[0]} #  空值
echo ${array[3]} #  ele3
```
## 读取数组
```bash
# 读取下标
echo ${array_name[0]}
echo ${array_name[1]}

# 使用 @ 或 * 可以获取数组中的所有元素
echo ${array_name[@]}
echo ${array_name[*]}

# 取得数组元素的个数
length=${#array_name[@]}
length=${#array_name[*]}

# 取得数组单个元素的长度
length_ele=${#array_name[n]}
```
## 遍历数组
```bash
# method 1
for i in "${array[@]}"; do
  echo "$i"
done

# method 2
for(( i=0;i<${#array[@]};i++));do
echo ${array[i]};
done;

# method 3
i=0
while [ $i -lt ${#array[@]} ];do
​ echo ${array[$i]}
​ let i++
done
```
# ShellMap
使用 declare -A array 或者 直接使用内嵌“索引-值”列表法定义map
> 需要bash 4 以上的版本 `bash -version`
```bash
declare -A info=(["name"]="hjx", ["nick_name"]="stonebird", ["age"]="18f")

# or
declare -A info
info["name"]="hjx"
info["nick_name"]="stonebird"
info["age"]="18f"

echo ${info["nick_name"]}
echo ${!array[*]}  #取关联数组所有键
echo ${!array[@]}  #取关联数组所有键
echo ${array[*]}   #取关联数组所有值
echo ${array[@]}   #取关联数组所有值
echo ${#array[*]}  #取关联数组长度
echo ${#array[@]}  #取关联数组长度
```


# Shell流程控制
```bash
# if
# if condition;then do something;fi
if [ ${nick_name} = "stonebird" ];then # 括号不能省略, 条件判断的等号两边有空格
    echo "stonebird is ok"
fi

# if else
## else
if condition
then
    something
    ...
else
    something
    ...
fi

## elif
if condition1
then
    something
elif condition2 
then 
    something
else
    something
fi
```

# Shell循环
## for循环
```bash 
for var in ele1 ele2 ...
do
    echo "${var}"
done

for (( i = 0; i < 10; i++ )); do
	#statements
done

## 无限循环
for (( ; ; )); do
	#statements
done
```
## while循环
```bash
while condition
do
    something
done

## 无限循环
while true    ## while ((1)) 也行
do
    something
done
```
## until循环
```bash
until condition
do
    something
done
```
## break 跳出循环
使用break跳出 for、until、while循环
```bash
a=0
while; do
	if [[ $a -eq 4 ]]; then
		break
	fi
	let a++
done
```
## continue - 执行下一次循环（跳出当次循环）
```bash
a=0
for (( i = 0; i < 10; i++ )); do
	if [[ $i -eq 8 ]]; then
		continue
	fi
	a=`expr ${a} + ${i}`
done
```

# Shell Case 
类似于其他语言swith case
```bash
case ${variable}  in
    模式1)
        command1;;
    模式2|模式3)
        command2;;
    *)
        default_command;;
esac
```

# Shell运算符
## 算术运算符
```bash
#整数使用expr 、let ，小数使用bc
+、-、*、/、%、
=（#赋值，=左右两边不能有空格）
==	#相等。用于比较两个数字，相同则返回 true。	[ $a == $b ] 返回 false。
!=	#不相等。用于比较两个数字，不相同则返回 true。	[ $a != $b ] 返回 true。

expr `1 + 1`
let a++
echo "2.2+2.5"|bc
```
## 关系运算符
```bash
-eq 相等
-ne 不相等
-gt 大于（左大于右）
-ge 大于等于
-lt 小于
-le 小于等于
```
## 布尔运算符
```bash
！非运算
-a 与运算
-o 或运算
```
## 逻辑运算符
```bash
&& 逻辑与
|| 逻辑或
```
## 字符串运算符
```bash
=  相等[$a = $b]  #shell 是一个 =
!=  不等[$a != $b]
-z  检测字符串长度是否为0 ?为0true
-n  检测字符串长度是否为0 ?不为0 ture
$str 检测字符串是否为空?不为空true
```
## 文件操作
```bash
-d file 检测是否是目录
-s file 检测文件是否为空（文件大小是否大于0），不为空返回 true。
-e file 检测目录或文件是否存在
-r -w -x file 检测文件是否可读写执行
-f file 检测文件是否是普通文件
```

# Shell函数
函数是多条指令的集合，是单独运行的代码块

```bash
[ function ] func_name [()]
{
    action;
    [return int] 
}

# 调用 
func_name
```
>推荐使用 function func_name(){} 的格式
>
>推荐返回状态码；0正常退出，其他异常退出

# Shell 输入/输出重定向
```bash
command > file	将输出重定向到 file。
command < file	将输入重定向到 file。
command >> file	将输出以追加的方式重定向到 file。
n > file	将文件描述符为 n 的文件重定向到 file。
n >> file	将文件描述符为 n 的文件以追加的方式重定向到 file。
n >& m	将输出文件 m 和 n 合并。
n <& m	将输入文件 m 和 n 合并。
# command1 < infile > outfile

<< tag	将开始标记 tag 和结束标记 tag 之间的内容作为输入。
# 例子
wc -l << EOF
    test
EOF
```
## /dev/null
```bash
command > /dev/null 2>&1
# 0 是标准输入（STDIN），1 是标准输出（STDOUT），2 是标准错误输出（STDERR）。 这里的 2 和 > 之间不可以有空格，2> 是一体的时候才表示错误输出.
```

# Shell 标出输入、标准输出、错误输出
一般情况下，每个 Unix/Linux 命令运行时都会打开三个文件：
- 标准输入文件(stdin)：stdin的文件描述符为0，Unix程序默认从stdin读取数据。
- 标准输出文件(stdout)：stdout 的文件描述符为1，Unix程序默认向stdout输出数据。
- 标准错误文件(stderr)：stderr的文件描述符为2，Unix程序会向stderr流中写入错误信息。
```bash
# stderr 重定向到 file
command 2>file
# stderr 追加到 file
command 2>>file

# 将 stdout 和 stderr 合并后重定向到 file
command > file 2>&1
# 将 stdout 和 stderr 合并后追加到 file
command >> file 2>&1
```

# Shell文件导入
Shell 也可以包含外部脚本。
```bash
. filename   # 注意点号(.)和文件名中间有一空格
# or
source filename
```