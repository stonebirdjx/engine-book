# Golang编译
```bash
go help build
-a	将命令源码文件与库源码文件全部重新构建，即使是最新的
-n	把编译期间涉及的命令全部打印出来，但不会真的执行，非常方便我们学习
-race	开启竞态条件的检测，支持的平台有限制
-x	打印编译期间用到的命名，它与 -n 的区别是，它不仅打印还会执行
```
# 获取汇编代码
```bash
go tool compile -S main.go
# or
go build -gcflags -S main.go

```

# -ldflags
```bash
-s: 去掉符号表
-w: 去掉 DWARF 调试信息
-X: 设置变量的值

# 例子
go build -ldflags="-s -w"
go build -ldflags="-X main.version=v1.0.0"
```

# -gcflags
```bash
-N: 禁用优化。这对于调试很有用，因为优化可能会改变代码的执行路径。
-l: 禁用内联。内联会将函数的调用展开为函数体内的代码，可能会使调试变得复杂。
-m 标志，你可以查看编译器关于内存分配的分析。

# 例子
go build -gcflags "-N -l"
go build -gcflags "main=-N -l" # 指定包名
go build -gcflags "-m -l" # 查看内存逃逸
```
