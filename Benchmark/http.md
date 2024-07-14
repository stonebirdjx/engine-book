# 压测重要指标
响应时间(RT) ：指系统对请求作出响应的时间.
吞吐量(Throughput) ：指系统在单位时间内处理请求的数量
QPS每秒查询率(Query Per Second) ：“每秒查询率”，是一台服务器每秒能够响应的查询次数，是对一个特定的查询服务器在规定时间内所处理流量多少的衡量标准。
TPS(TransactionPerSecond)：每秒钟系统能够处理的交易或事务的数量
并发连接数：某个时刻服务器所接受的请求总数

# 压测工具
## ab
全称Apache Bench，是Apache自带的性能测试工具。使用这个工具，只须指定同时连接数、请求数以及URL，即可测试网站或网站程序的性能。

文档说明：https://httpd.apache.org/docs/2.4/programs/ab.html

```bash
ab [options] uri
# 并发参数
-n requests 总请求数
-c concurrency 一次产生的请求数，可以理解为并发数
```

## wrk
```bash
wrk -t12 -c400 -d30s http://127.0.0.1:8080/index.html

# 参数
-c, --connections: total number of HTTP connections to keep open with
                   each thread handling N = connections/threads

-d, --duration:    duration of the test, e.g. 2s, 2m, 2h

-t, --threads:     total number of threads to use

-s, --script:      LuaJIT script, see SCRIPTING

-H, --header:      HTTP header to add to request, e.g. "User-Agent: wrk"

    --latency:     print detailed latency statistics

    --timeout:     record a timeout if a response is not received within
                   this amount of time.
```

## go-stress-testing
go-stress-testing 是一款基于Go语言开发的压力测试工具，可以模拟并发请求，测试网站的性能。

```bash
-c 表示并发数
-n 每个并发执行请求的次数，总请求的次数 = 并发数 * 每个并发执行请求的次数
-u 需要压测的地址
go-stress-testing -c 10 -n 100 -u https://www.baidu.com/
```