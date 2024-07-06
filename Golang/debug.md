# GODEBUG
GODEBUG 是 golang 中一个控制runtime调度变量的变量，其值为一个用逗号隔开的 name=val对列表 

比较重要的参数有以下
# schedtrace
```bash
# 1000ms 打印一次trace信息
GODEBUG=schedtrace=1000 go run main.go 
```
- sched：每一行都代表调度器的调试信息，后面提示的毫秒数表示启动到现在的运行时间，输出的时间间隔受 schedtrace 的值影响。
- gomaxprocs：当前的 CPU 核心数（GOMAXPROCS 的当前值）。
- idleprocs：空闲的处理器数量，后面的数字表示当前的空闲数量。
- threads：OS 线程数量，后面的数字表示当前正在运行的线程数量。
- spinningthreads：自旋状态的 OS 线程数量。
- idlethreads：空闲的线程数量。
- runqueue：全局队列中中的 Goroutine 数量，而后面的 [0 0 1 1] 则分别代表这 4 个 P 的本地队列正在运行的 Goroutine 数量。
# scheddetail
scheddetail 提供的信息比 schedtrace 更加详细，包括但不限于：
- 当前正在运行的goroutine ID。
- goroutine的状态变化。
- goroutine的阻塞原因。
```bash
GODEBUG=scheddetail=1,schedtrace=1000 go run main.go
```

# gctrace
gctrace 是一个控制 GC 的变量
```bash
GODEBUG=gctrace=1 go run main.go    
gc 1 @0.032s 0%: 0.019+0.45+0.003 ms clock, 0.076+0.22/0.40/0.80+0.012 ms cpu, 4->4->0 MB, 5 MB goal, 4 P
gc 2 @0.046s 0%: 0.004+0.40+0.008 ms clock, 0.017+0.32/0.25/0.81+0.034 ms cpu, 4->4->0 MB, 5 MB goal, 4 P
gc 3 @0.063s 0%: 0.004+0.40+0.008 ms clock, 0.018+0.056/0.32/0.64+0.033 ms cpu, 4->4->0 MB, 5 MB goal, 4 P
gc 4 @0.080s 0%: 0.004+0.45+0.016 ms clock, 0.018+0.15/0.34/0.77+0.065 ms cpu, 4->4->1 MB, 5 MB goal, 4 P
gc 5 @0.095s 0%: 0.015+0.87+0.005 ms clock, 0.061+0.27/0.74/1.8+0.023 ms cpu, 4->4->1 MB, 5 MB goal, 4 P
gc 6 @0.113s 0%: 0.014+0.69+0.002 ms clock, 0.056+0.23/0.48/1.4+0.011 ms cpu, 4->4->1 MB, 5 MB goal, 4 P
gc 7 @0.140s 1%: 0.031+2.0+0.042 ms clock, 0.12+0.43/1.8/0.049+0.17 ms cpu, 4->4->1 MB, 5 MB goal, 4 P
```
- gc 7：第 7 次 GC。
- @0.140s：当前是程序启动后的 0.140s。
- 1%：程序启动后到现在共花费 1% 的时间在 GC 上。
- 0.031+2.0+0.042 ms clock：
    - 0.031：表示单个 P 在 mark 阶段的 STW 时间。
    - 2.0：表示所有 P 的 mark concurrent（并发标记）所使用的时间。
    - 0.042：表示单个 P 的 markTermination 阶段的 STW 时间。
- 0.12+0.43/1.8/0.049+0.17 ms cpu：
    - 0.12：表示整个进程在 mark 阶段 STW 停顿的时间。
    - 0.43/1.8/0.049：0.43 表示 mutator assist 占用的时间，1.8 表示 - - dedicated + fractional 占用的时间，0.049 表示 idle 占用的时间。
    - 0.17ms：0.17 表示整个进程在 markTermination 阶段 STW 时间。
- 4->4->1 MB：
    - 4：表示开始 mark 阶段前的 heap_live 大小。
    - 4：表示开始 markTermination 阶段前的 heap_live 大小。
    - 1：表示被标记对象的大小。
- 5 MB goal：表示下一次触发 GC 回收的阈值是 5 MB。
- 4 P：本次 GC 一共涉及多少个 P。

## Runtime
```go
debug.SetGCPercent()
```