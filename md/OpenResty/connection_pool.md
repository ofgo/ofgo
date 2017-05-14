# 连接池

## 什么是连接

连接是两台计算机之间建立的通信通道。

由于 HTTP 使用的是传输层的 TCP 协议，因此，我们说的 HTTP 连接，本质上是指 TCP 连接。

TCP 最大的特点就是可靠性和面向连接。TCP 连接的建立和断开分别需要经历三次握手和四次挥手的过程。由此可知，建立和断开都需要大量的资源、时间消耗，尤其是在大并发的情况下。

## 短连接 VS 长连接

短连接：建立连接 -> 传输数据 -> 断开连接
长连接：建立连接 -> 传输数据 -> （保持连接） -> ... -> 断开连接

由此可以看出，

* 短连接：管理简单，存在的连接都是有效的，无需额外连接控制手段；
* 长连接：多次传输数据时，可以省去连接的建立和断开操作，减少浪费，但是需要额外的连接管理手段；

因此，

* 短连接：适用于数据传输简单，连接数可能非常大量的情况；
* 长连接：适用于数据传输频繁，且连接数不能太多的情况；

HTTP/1.0 中，默认使用短连接；HTTP/1.1 起，默认使用长连接，响应头中会有 `Connection: keep-alive`。`keep-alive` 的时长可以在Web Server软件中设定，如Nginx。

以浏览器访问网页为例，短连接情况下，每个 JS 文件、图片、CSS 文件都会建立一个 HTTP 连接；长连接情况下，则会共用请求 HTML 文件时建立的连接。

## 连接池

上面说到，大并发情况下，创建和断开连接会耗费巨大的时间和资源。此时，理想的情况应该是：创建连接->传输数据->传输数据->...->销毁连接。

这就要求使用长连接，用于保存长连接的地方就是连接池，它是一种连接共享策略。

客户机在连接服务器时，首先从连接池里寻找可用的长连接，那就用现成的，否则创建新的长连接。待本次数据传输完成后，又把长连接放回连接池。

这样做的主要目的有两个：1. 减小连接建立和断开次数，节省资源； 2. 控制向服务器发起的连接数量，以免将其撑爆。

## 在 OpenResty 中的应用

在 OpenResty 中，所有具备 `set_keepalive()` 方法的类或函数都自带连接池的。

以 `lua-resty-mysql` 中的 `set_keepalive(max_idle_timeout, pool_size)` 为例，调用此方法表示把当前MySQL连接放入连接池。其中，`max_idle_timeout` 表示最大休眠时间，达到此时间后该连接将从连接池销毁；`pool_size` 表示每个nginx worker中连接池保存长连接的最大数量。（如果每次 `pool_size` 值都不一样，那是不是以最后一次为准？）

什么时候调用 `set_keepalive()` ?

在本应该调用 `close()` 的时候调用。调用 `close()` 和 `set_keepalive()` 都会使 `resty.mysql` 对象进入 `closed` 状态（不能再进行任何 SQL 操作，除非再次 `connect()` ），不同的是，`set_keepalive()` 会把长连接放入连接池。

## Reference

* [openresty中连接池的应用](https://7byte.github.io/2016/10/02/trytouse-conn-pool/)