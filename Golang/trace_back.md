# GOTRACEBACK
控制程序崩溃时（panic、runtime），打印堆栈信息
```go
package main

import (
	"fmt"
	"time"
)

func test(id string) {
	panic("panic" + id)
}

func main() {
	for i := range 3 {
		go test(fmt.Sprint(i))
	}

	time.Sleep(time.Second)
}
```
GOTRACEBACK取值
- none，不显示任何 goroutine 堆栈信息
- single，默认级别，显示当前 goroutine 堆栈信息
- all，显示所有 user （不包括 runtime）创建的 goroutine 堆栈信息
- system，显示所有 user + runtime 创建的 goroutine 堆栈信息
- crash，和 system 打印一致，但会生成 core dump 文件（Unix 系统上，崩#溃会引发 SIGABRT 以触发 core dump）

# mac不能使用
```go
//go:nosplit
func crash() {
 // OS X core dumps are linear dumps of the med memory,
 // from the first virtual byte to the last, with zeros in the gaps.
 // Because of the way we arrange the address space on 64-bit systems,
 // this means the OS X core file will be 128 GB and even on a zippy
 // workstation can take OS X well over an hour to write (uninterruptible).
 // Save users from making that mistake.
 if GOOS == "darwin" && GOARCH == "64" {
  return
 }

 dieFromSignal(_SIGABRT)
```

> Linux 上还受到 ulimit 的限制。可以用 ulimit -c 查看对 Core dump 的大小限制。 默认情况下，Core dump 的大小为 0，即不生成。ulimit -c unlimited
> 

编译与运行
```bash
go build -o crash
GOTRACEBACK=crash ./crash

# 输出堆栈信息
anic: panic0

goroutine 34 gp=0x1400011c1c0 m=2 mp=0x14000058908 [running]:
panic({0x1000f39c0?, 0x14000010020?})
        /usr/local/go/src/runtime/panic.go:779 +0x140 fp=0x14000132790 sp=0x140001326e0 pc=0x10006d450
main.test(...)
        /Users/stonebird/Somefiles/code/gospace/helloworld/main.go:9
main.main.gowrap1()
        /Users/stonebird/Somefiles/code/gospace/helloworld/main.go:14 +0x50 fp=0x140001327d0 sp=0x14000132790 pc=0x1000c6e10
runtime.goexit({})
        /usr/local/go/src/runtime/asm_arm64.s:1222 +0x4 fp=0x140001327d0 sp=0x140001327d0 pc=0x10009f4f4
created by main.main in goroutine 1
        /Users/stonebird/Somefiles/code/gospace/helloworld/main.go:14 +0x28

goroutine 1 gp=0x140000021c0 m=nil [sleep]:
runtime.gopark(0x1543283d2f6c2?, 0x1000c6d58?, 0xf0?, 0xb0?, 0x1400012c030?)
        /usr/local/go/src/runtime/proc.go:402 +0xc8 fp=0x1400010aeb0 sp=0x1400010ae90 pc=0x100070bd8
time.Sleep(0x3b9aca00)
        /usr/local/go/src/runtime/time.go:195 +0xfc fp=0x1400010aef0 sp=0x1400010aeb0 pc=0x10009c34c
main.main()
        /Users/stonebird/Somefiles/code/gospace/helloworld/main.go:17 +0xbc fp=0x1400010af40 sp=0x1400010aef0 pc=0x1000c6d9c
runtime.main()
        /usr/local/go/src/runtime/proc.go:271 +0x28c fp=0x1400010afd0 sp=0x1400010af40 pc=0x1000707ac
runtime.goexit({})
        /usr/local/go/src/runtime/asm_arm64.s:1222 +0x4 fp=0x1400010afd0 sp=0x1400010afd0 pc=0x10009f4f4

goroutine 17 gp=0x14000084380 m=nil [force gc (idle)]:
runtime.gopark(0x0?, 0x0?, 0x0?, 0x0?, 0x0?)
        /usr/local/go/src/runtime/proc.go:402 +0xc8 fp=0x1400004e790 sp=0x1400004e770 pc=0x100070bd8
runtime.goparkunlock(...)
        /usr/local/go/src/runtime/proc.go:408
runtime.forcegchelper()
        /usr/local/go/src/runtime/proc.go:326 +0xb8 fp=0x1400004e7d0 sp=0x1400004e790 pc=0x100070a68
runtime.goexit({})
        /usr/local/go/src/runtime/asm_arm64.s:1222 +0x4 fp=0x1400004e7d0 sp=0x1400004e7d0 pc=0x10009f4f4
created by runtime.init.6 in goroutine 1
        /usr/local/go/src/runtime/proc.go:314 +0x24

goroutine 18 gp=0x14000084540 m=nil [GC sweep wait]:
runtime.gopark(0x0?, 0x0?, 0x0?, 0x0?, 0x0?)
        /usr/local/go/src/runtime/proc.go:402 +0xc8 fp=0x1400004ef60 sp=0x1400004ef40 pc=0x100070bd8
runtime.goparkunlock(...)
        /usr/local/go/src/runtime/proc.go:408
runtime.bgsweep(0x14000090000)
        /usr/local/go/src/runtime/mgcsweep.go:278 +0xa0 fp=0x1400004efb0 sp=0x1400004ef60 pc=0x10005da50
runtime.gcenable.gowrap1()
        /usr/local/go/src/runtime/mgc.go:203 +0x28 fp=0x1400004efd0 sp=0x1400004efb0 pc=0x100051ec8
runtime.goexit({})
        /usr/local/go/src/runtime/asm_arm64.s:1222 +0x4 fp=0x1400004efd0 sp=0x1400004efd0 pc=0x10009f4f4
created by runtime.gcenable in goroutine 1
        /usr/local/go/src/runtime/mgc.go:203 +0x6c

goroutine 19 gp=0x14000084700 m=nil [GC scavenge wait]:
runtime.gopark(0x14000090000?, 0x1000e8120?, 0x1?, 0x0?, 0x14000084700?)
        /usr/local/go/src/runtime/proc.go:402 +0xc8 fp=0x1400004f760 sp=0x1400004f740 pc=0x100070bd8
runtime.goparkunlock(...)
        /usr/local/go/src/runtime/proc.go:408
runtime.(*scavengerState).park(0x100179cc0)
        /usr/local/go/src/runtime/mgcscavenge.go:425 +0x5c fp=0x1400004f790 sp=0x1400004f760 pc=0x10005b43c
runtime.bgscavenge(0x14000090000)
        /usr/local/go/src/runtime/mgcscavenge.go:653 +0x44 fp=0x1400004f7b0 sp=0x1400004f790 pc=0x10005b994
runtime.gcenable.gowrap2()
        /usr/local/go/src/runtime/mgc.go:204 +0x28 fp=0x1400004f7d0 sp=0x1400004f7b0 pc=0x100051e68
runtime.goexit({})
        /usr/local/go/src/runtime/asm_arm64.s:1222 +0x4 fp=0x1400004f7d0 sp=0x1400004f7d0 pc=0x10009f4f4
created by runtime.gcenable in goroutine 1
        /usr/local/go/src/runtime/mgc.go:204 +0xac

goroutine 33 gp=0x1400011c000 m=nil [finalizer wait]:
runtime.gopark(0x0?, 0x0?, 0x2a?, 0x0?, 0x10004f624?)
        /usr/local/go/src/runtime/proc.go:402 +0xc8 fp=0x14000052580 sp=0x14000052560 pc=0x100070bd8
runtime.runfinq()
        /usr/local/go/src/runtime/mfinal.go:194 +0x108 fp=0x140000527d0 sp=0x14000052580 pc=0x100050f98
runtime.goexit({})
        /usr/local/go/src/runtime/asm_arm64.s:1222 +0x4 fp=0x140000527d0 sp=0x140000527d0 pc=0x10009f4f4
created by runtime.createfing in goroutine 1
        /usr/local/go/src/runtime/mfinal.go:164 +0x80

goroutine 35 gp=0x1400011c380 m=nil [runnable]:
runtime.gcTrigger.test({0x0?, 0x0?, 0x0?})
        /usr/local/go/src/runtime/mgc.go:568 +0x144 fp=0x14000132e10 sp=0x14000132e10 pc=0x1000520f4
runtime.mallocgc(0x6, 0x0, 0x0)
        /usr/local/go/src/runtime/malloc.go:1307 +0x808 fp=0x14000132ea0 sp=0x14000132e10 pc=0x100048358
runtime.rawstring(...)
        /usr/local/go/src/runtime/string.go:267
runtime.rawstringtmp(0x0?, 0x6)
        /usr/local/go/src/runtime/string.go:131 +0x50 fp=0x14000132ed0 sp=0x14000132ea0 pc=0x10008a800
runtime.concatstrings(0x0?, {0x14000132f68?, 0x2, 0x0?})
        /usr/local/go/src/runtime/string.go:51 +0xf4 fp=0x14000132f40 sp=0x14000132ed0 pc=0x10008a264
runtime.concatstring2(0x0?, {0x1000c725d?, 0x0?}, {0x1001725c8?, 0x0?})
        /usr/local/go/src/runtime/string.go:60 +0x48 fp=0x14000132f90 sp=0x14000132f40 pc=0x10008a3e8
main.test(...)
        /Users/stonebird/Somefiles/code/gospace/helloworld/main.go:9
main.main.gowrap1()
        /Users/stonebird/Somefiles/code/gospace/helloworld/main.go:14 +0x3c fp=0x14000132fd0 sp=0x14000132f90 pc=0x1000c6dfc
runtime.goexit({})
        /usr/local/go/src/runtime/asm_arm64.s:1222 +0x4 fp=0x14000132fd0 sp=0x14000132fd0 pc=0x10009f4f4
created by main.main in goroutine 1
        /Users/stonebird/Somefiles/code/gospace/helloworld/main.go:14 +0x28

goroutine 36 gp=0x1400011c540 m=nil [runnable]:
main.main.gowrap1()
        /Users/stonebird/Somefiles/code/gospace/helloworld/main.go:14 fp=0x140001337d0 sp=0x140001337d0 pc=0x1000c6dc0

# linux 会保存一个core文件 可以dlv分析
dlv core main core
dlv) goroutines
* Goroutine 1 - User: ./main.go:21 main.main (0x45b81a) (thread 18061)
  Goroutine 2 - User: /usr/local/go/src/runtime/proc.go:367 runtime.gopark (0x42ed96) [force gc (idle)]
  Goroutine 3 - User: /usr/local/go/src/runtime/proc.go:367 runtime.gopark (0x42ed96) [GC sweep wait]
  Goroutine 4 - User: /usr/local/go/src/runtime/proc.go:367 runtime.gopark (0x42ed96) [GC scavenge wait]
[4 goroutines]
```
