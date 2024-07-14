# zkServer
```bash
./zkServer.sh [--config <conf-dir>] {start|start-foreground|stop|version|restart|status|print-cmd}
```

# zkCli
## help 查看命令
```bash
help
	addWatch [-m mode] path # optional mode is one of [PERSISTENT, PERSISTENT_RECURSIVE] - default is PERSISTENT_RECURSIVE
	addauth scheme auth
	close 
	config [-c] [-w] [-s]
	connect host:port
	create [-s] [-e] [-c] [-t ttl] path [data] [acl]
	delete [-v version] path
	deleteall path [-b batch size]
	delquota [-n|-b|-N|-B] path
	get [-s] [-w] path
	getAcl [-s] path
	getAllChildrenNumber path
	getEphemerals path
	history 
	listquota path
	ls [-s] [-w] [-R] path
	printwatches on|off
	quit 
	reconfig [-s] [-v version] [[-file path] | [-members serverID=host:port1:port2;port3[,...]*]] | [-add serverId=host:port1:port2;port3[,...]]* [-remove serverId[,...]*]
	redo cmdno
	removewatches path [-c|-d|-a] [-l]
	set [-s] [-v version] path data
	setAcl [-s] [-v version] [-R] path acl
	setquota -n|-b|-N|-B val path
	stat [-w] path
	sync path
	version 
	whoami 
```
> -e 临时节点
>
> -s 顺序节点: create -s /app 自动加上000000x尾缀
## ls node 列出节点
```bash
ls /
ls /app
```

## create node [value] 创建节点
```bash
create -s /app
create  /app1 "hello"
```

## set node [value] 设置节点
```bash
set /app1 "world"
``` 

## get node 获取节点
```bash
get /app1
```
## delete node 删除节点
```bash
delete /app1
```
## deleteall node 删除节点及子节点
```bash
create /app1/path1
deleteall app1
```

## watch node 监听节点
```bash

```

# golang SDK
```go
package main

import (
	"fmt"
	"time"

	"github.com/go-zookeeper/zk"
)

func main() {
	c, _, err := zk.Connect([]string{"127.0.0.1:2181"}, time.Second*5)
	if err != nil {
		panic(err)
	}
	defer c.Close()
	content := []byte("hello")
	res, err := c.Create("/stone", content, 0, zk.WorldACL(zk.PermAll))
	if err != nil {
		panic(err)
	}
	fmt.Println(res)
}
```