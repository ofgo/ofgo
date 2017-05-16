# Cache

OR提供两种缓存： LuaSharedDict 和 LuaLRUCache 。

### LuaSharedDict

  应用级别缓存，在 Nginx 所有 worker 之间共享，内部使用LRU算法。

  1. 需预先根据 key/value 大小和数量分配好缓存大小；
  2. 每次操作都是全局锁，不同worker之间容易引起竞争；
  3. 支持更多的API操作；
  4. 只能存储简单的数据类型，如布尔值、整数、字符串，不能存储表；

### LuaLRUCahce

  Worker 级别缓存，不会在Nginx workers 之间共享，在同一 worker 中的所有请求之间共享。

  1. 只需确定缓存 key/value 条数；
  2. 不触发锁，没有体积限制，效率高；
  3. 不同 worker 之间不共享，存在冗余存储；
  4. 只有 get / set / delete 三个API；
  5. 可以存储任意类型；
