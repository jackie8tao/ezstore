# ezstore - 项目指南与 Agent 协作说明

## 1. 项目简介与目标 (Project Overview & Goals)

`ezstore` 是一个专门用于学习和实践**数据库核心实现原理**的 Go 语言开源项目。
本项目的主要目标是通过纯 Go 语言从零实现现代数据库的核心组件与特征，帮助开发者深入理解存储引擎、数据结构、I/O 调度、故障恢复与并发控制等底层机制。

### 核心目标与特性

- **存储引擎 (Storage Engine)**：实现底层数据页面（Page）、磁盘管理器（Disk Manager）与数据布局。
- **缓冲池管理 (Buffer Pool Manager)**：基于 LRU / Clock 淘汰策略的内存缓存池，实现脏页刷盘与页面固定（Pin/Unpin）机制。
- **索引机制 (Indexing)**：实现高效的持久化索引结构（如 Disk-based B+ Tree 或 LSM-Tree / SkipList）。
- **预写日志与故障恢复 (WAL & Crash Recovery)**：实现 Write-Ahead Logging 及类似 ARIES 的恢复机制（Analysis, Redo, Undo）。
- **事务与并发控制 (Transactions & Concurrency)**：支持 ACID 特性，实现锁管理器（Lock Manager）及并发控制协议（如 2PL 或 MVCC）。
- **查询与接口层 (Query & API Layer)**：提供简洁高效的读写 API，并支持逐步扩展简单的查询解析与执行计划。

---

## 2. 架构设计原则 (Architecture Principles)

1. **分层清晰与高内聚低耦合**：各子模块（Storage, Buffer, Index, Concurrency, Txn）职责分明，接口定义严谨。
2. **教学性与工程性兼备**：优先保证算法逻辑和数据结构清晰直观，同时兼顾 Go 语言的工程化实践（如并发安全、资源释放、零值可用等）。
3. **测试驱动与验证完备**：核心数据结构与并发逻辑必须具备完善的单元测试（Unit Tests）、竞态检测（Race Detector）和性能基准测试（Benchmark）。

---

## 3. 编码与协作规范 (Conventions & Guidelines)

- **开发语言**：Go (>= 1.22)
- **代码注释**：代码中的所有注释和 Docstrings **必须使用英文**。
- **用户沟通与文档**：与用户的日常交流、技术方案探讨、以及生成的 Walkthrough / 架构设计文档**必须使用中文**。
- **错误处理**：显式返回 `error`，避免 `panic`，定义清晰的领域错误类型（Sentinel Errors 或自定义 Error 类型）。
- **并发安全**：合理使用互斥锁（`sync.Mutex` / `sync.RWMutex`）与原子操作（`sync/atomic`），注意死锁防范与锁粒度控制。

---

## 4. 推荐演进路线 (Roadmap)

1. **Phase 1: 基础设施与存储层 (Storage Layer)**
   - `Page` 内存与磁盘二进制表示设计
   - `DiskManager` 磁盘读写与空间分配
2. **Phase 2: 缓冲池层 (Buffer Pool Manager)**
   - `Replacer`（LRU / Clock 算法）
   - `BufferPoolManager` 页面调度、淘汰与落盘
3. **Phase 3: 树形索引层 (B+ Tree Index)**
   - B+ 树内部节点与叶子节点 Page 结构
   - 插入、分裂、删除、合并与点查/范围查找
4. **Phase 4: 日志与恢复层 (WAL & Recovery)**
   - Log Record 格式定义
   - Log Manager 异步刷盘与 Checkpoint
   - 故障恢复算法（Redo/Undo）
5. **Phase 5: 并发控制与事务 (Concurrency & Transactions)**
   - Lock Manager（共享锁/排他锁、意向锁）
   - 事务上下文与隔离级别实现
