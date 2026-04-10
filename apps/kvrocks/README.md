# Kvrocks

Kvrocks 是一个基于 **RocksDB** 的分布式键值数据库，使用 **Redis 协议**，可以作为 Redis 的高性能、低成本替代方案。它的目标是：

* 保持对 Redis 协议的兼容性
* 降低存储成本（通过磁盘存储替代内存存储）
* 提供更强的可扩展性和大数据量支持

---

## ✨ 特性

* **Redis 协议兼容**

  * 支持大多数常用 Redis 命令
  * 可以直接使用现有的 Redis 客户端和工具

* **RocksDB 存储引擎**

  * 数据持久化存储在磁盘，降低内存成本
  * 适合存储海量数据

* **丰富的数据结构**

  * String, Hash, List, Set, ZSet, Bitmap
  * 支持 TTL、EXPIRE、SCAN 等命令

* **高可用与扩展**

  * 支持主从复制
  * 部分支持 Redis Cluster 模式
  * 可结合哨兵或其他外部系统实现高可用

* **低成本大容量**

  * 适合数百 GB 到 TB 级别数据场景
  * 存储成本远低于 Redis 内存模式

---

## 🚀 快速开始

### 1. 安装

#### Docker 方式

```bash
docker run -d --name kvrocks \
  -p 6666:6666 \
  apache/kvrocks
```

#### 源码编译

```bash
git clone https://github.com/apache/kvrocks.git
cd kvrocks
make -j
./build/kvrocks -c kvrocks.conf
```

### 2. 连接

Kvrocks 使用 Redis 协议，可以直接用 `redis-cli` 连接：

```bash
redis-cli -p 6666
```

示例：

```bash
127.0.0.1:6666> set foo bar
OK
127.0.0.1:6666> get foo
"bar"
```

---

## ⚖️ Kvrocks vs Redis

| 特性       | Redis (内存型) | Kvrocks (RocksDB) |
| -------- | ----------- | ----------------- |
| 协议兼容性    | ✅ 完全支持      | ✅ 高度兼容            |
| 数据存储方式   | 内存+持久化      | RocksDB 磁盘存储      |
| 性能（小数据量） | 🚀 极快（内存级别） | 较快                |
| 成本（大数据量） | 💰 内存成本极高   | 💰 更低，TB 级别可行     |
| 适用场景     | 缓存、秒级高频读写   | 大容量 KV 存储、冷热数据混合  |

---

## ⚠️ 注意事项

* 不支持 Redis 的 **Lua 脚本** 与 **模块系统**
* 部分 Redis 命令可能行为不同或未实现
* 高性能写入场景需要合理配置 RocksDB 参数（如 `write_buffer_size`）

---

## 📚 参考资料

* [Kvrocks GitHub](https://github.com/apache/kvrocks)
* [RocksDB 文档](https://github.com/facebook/rocksdb)
* [Redis 协议说明](https://redis.io/topics/protocol)