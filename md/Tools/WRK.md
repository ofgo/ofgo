# WRK

类似 ab 的HTTP压测工具，项目源码 [WRK](https://github.com/wg/wrk) 。

## 使用方式

```bash
wrk -t10 -c 400 -d30s http://127.0.0.1/
```

## 常用参数

* `-t`: 使用线程个数
* `-c`: 每个线程打开的连接数
* `-d`: 测试持续时间

## 测试结果

* Latency: 响应时间
* Req/Sec: 每个线程每秒完成请求数
* Request/sec: 每秒完成的总请求数
* Transfer/sec: 每秒数据传输量