# 使用哪一种Shell
Bash是唯一被允许执行的shell脚本语言。可执行文件必须以` #!/bin/bash `和最小数量的标志开始。请使用 set 来设置shell的选项，使得用 bash <script_name> 调用你的脚本时不会破坏其功能。
```bash
#!/bin/bash
set -euxo pipefail
```
# 什么时候使用Shell
Shell应该仅仅被用于小功能或者简单的包装脚本。
- 如果你在乎性能，那么请选择其他工具，而不是使用shell。
- shell代码行数过多时，应该考虑使用python等

# 文件扩展名
可执行文件应该没有扩展名（强烈建议）或者使用`.sh`扩展名。库文件必须使用`.sh`作为扩展名，而且应该是不可执行的。

# STDOUT&STDERR
所有的错误信息都应该被导向STDERR。
```bash
err() {
    echo "[$(date +'%Y-%m-%dT%H:%M:%S%z')]: $@" >&2
}

if ! do_something; then
    err "Unable to do_something"
    exit "${E_DID_NOTHING}"
fi
```

# 注释
## 文件头
每个文件的开头是其文件内容的描述。
```bash
#!/bin/bash
#
# Perform hot backups of Oracle databases.
```

## 函数注释
```bash
#######################################
# Cleanup files from the backup dir
# Globals:
#   BACKUP_DIR
#   ORACLE_SID
# Arguments:
#   None
# Returns:
#   None
#######################################
cleanup() {
  ...
}
```

# 格式
## 缩紧 
缩进两个空格，没有制表符。

## 行的长度和长字符串
行的最大长度为80个字符。
```bash
# DO use 'here document's
cat <<END;
I am an exceptionally long
string.
END

# Embedded newlines are ok too
long_string="I am an exceptionally
  long string."
```

# 管道
如果一行容不下整个管道操作，那么请将整个管道操作分割成每行一个管段。
```bash
command1 | command2

# Long commands
command1 \
  | command2 \
  | command3 \
  | command4
```

# 循环
请将 ; do , ; then 和 while , for , if 放在同一行。
```bash
if [[ "$?" -ne 0 ]]; then
    error_message
fi
```

# 使用$()而不是``
```bash
# This is preferred:
var="$(command "$(command1)")"
# This is not:
var="`command \`command1\``"
```

# 使用[[]] 而不是[]
[[ ... ]] 允许正则表达式匹配，而 [ ... ] 不允许。
```bash
if [[ "filename" =~ ^[[:alnum:]]+name ]]; then
  echo "Match"
fi
```

# 避免使用eval
```bash
# What does this set?
# Did it succeed? In part or whole?
eval $(set_my_variables)

# What happens if one of the returned values has a space in it?
variable="$(eval some_function)"
```

# 命名约定
## 函数名
```bash
# Single function
my_func() {
  ...
}

# Part of a package
mypackage::my_func() {
  ...
}
```
## 常量和环境变量名
使用 readonly 或者 declare -r 来确保变量只读。
```bash
# Constant
readonly PATH_TO_FILES='/some/path'
# Both constant and environment
declare -xr ORACLE_SID='PROD'
```

# 本地变量
```bash
my_func2() {
  local name="$1"

  # Separate lines for declaration and assignment:
  local my_var
  my_var="$(my_func)" || return

  # DO NOT do this: $? contains the exit code of 'local', not my_func
  local my_var="$(my_func)"
  [[ $? -eq 0 ]] || return
}
```