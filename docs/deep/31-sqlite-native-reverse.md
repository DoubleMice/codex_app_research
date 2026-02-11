# better_sqlite3.node 数据库模块逆向分析

## 概述

better_sqlite3.node 是 better-sqlite3 库的原生模块，提供同步 SQLite 数据库访问。

**文件**: `extracted/app/node_modules/better-sqlite3/build/Release/better_sqlite3.node`
**架构**: arm64 (Apple Silicon)
**语言**: C++ (V8 绑定)

## 1. 链接库

```
/usr/lib/libc++.1.dylib
/usr/lib/libSystem.B.dylib
```

注：SQLite 库静态链接到模块中。

## 2. 核心类

### Database 类

| 方法 | 说明 |
|------|------|
| `JS_new` | 创建数据库实例 |
| `JS_prepare` | 准备 SQL 语句 |
| `JS_exec` | 执行 SQL |
| `JS_close` | 关闭数据库 |
| `JS_backup` | 数据库备份 |
| `JS_serialize` | 序列化数据库 |
| `JS_function` | 注册自定义函数 |
| `JS_aggregate` | 注册聚合函数 |
| `JS_table` | 注册虚拟表 |
| `JS_loadExtension` | 加载扩展 |
| `JS_unsafeMode` | 不安全模式 |
| `JS_defaultSafeIntegers` | 安全整数默认值 |
| `JS_inTransaction` | 事务状态属性 |
| `JS_open` | 打开状态属性 |

### Statement 类

| 方法 | 说明 |
|------|------|
| `JS_new` | 创建语句实例 |
| `JS_run` | 执行语句 (INSERT/UPDATE/DELETE) |
| `JS_get` | 获取单行 |
| `JS_all` | 获取所有行 |
| `JS_iterate` | 迭代器 |
| `JS_bind` | 绑定参数 |
| `JS_pluck` | 单列模式 |
| `JS_raw` | 原始数组模式 |
| `JS_columns` | 获取列信息 |
| `JS_safeIntegers` | 安全整数模式 |
| `JS_busy` | 忙碌状态属性 |

### StatementIterator 类

| 方法 | 说明 |
|------|------|
| `JS_new` | 创建迭代器 |
| `JS_next` | 获取下一行 |
| `JS_return` | 返回结果 |
| `JS_symbolIterator` | Symbol.iterator |
| `Next` | 内部迭代 |
| `Return` | 内部返回 |
| `Throw` | 抛出异常 |
| `Cleanup` | 清理资源 |

### Backup 类

用于数据库备份操作。

### CustomTable 类

用于虚拟表实现。

## 3. SQLite 错误码

```
SQLITE_OK          # 成功
SQLITE_ERROR       # SQL 错误
SQLITE_INTERNAL    # 内部错误
SQLITE_PERM        # 权限拒绝
SQLITE_ABORT       # 操作中止
SQLITE_BUSY        # 数据库忙
SQLITE_LOCKED      # 数据库锁定
SQLITE_NOMEM       # 内存不足
SQLITE_READONLY    # 只读数据库
SQLITE_INTERRUPT   # 操作中断
SQLITE_IOERR       # I/O 错误
SQLITE_CORRUPT     # 数据库损坏
SQLITE_NOTFOUND    # 未找到
SQLITE_FULL        # 数据库满
SQLITE_CANTOPEN    # 无法打开
SQLITE_PROTOCOL    # 协议错误
SQLITE_EMPTY       # 空数据库
SQLITE_SCHEMA      # Schema 变更
SQLITE_TOOBIG      # 数据太大
SQLITE_CONSTRAINT  # 约束违反
SQLITE_MISMATCH    # 类型不匹配
SQLITE_MISUSE      # 误用
SQLITE_NOLFS       # 不支持大文件
SQLITE_AUTH        # 授权拒绝
SQLITE_FORMAT      # 格式错误
SQLITE_RANGE       # 范围错误
SQLITE_NOTADB      # 不是数据库
SQLITE_NOTICE      # 通知
```

## 4. Data 辅助类

用于 JS 和 SQLite 之间的数据转换：

| 方法 | 说明 |
|------|------|
| `GetValueJS` | 获取单个值 |
| `GetFlatRowJS` | 获取扁平行 |
| `GetRawRowJS` | 获取原始行 |
| `GetExpandedRowJS` | 获取展开行 |
| `GetArgumentsJS` | 获取参数 |
| `BindValueFromJS` | 绑定 JS 值 |

## 5. 内部常量

```cpp
Database::MAX_BUFFER_SIZE   // 最大缓冲区大小
Database::MAX_STRING_SIZE   // 最大字符串大小
```

## 6. 错误处理

```cpp
ThrowError(const char*)        // 抛出通用错误
ThrowTypeError(const char*)    // 抛出类型错误
ThrowRangeError(const char*)   // 抛出范围错误
Database::ThrowSqliteError()   // 抛出 SQLite 错误
Database::ThrowDatabaseError() // 抛出数据库错误
```

## 7. 特性

### 同步 API

better-sqlite3 提供完全同步的 API，避免回调地狱。

### 预编译语句

- 自动缓存预编译语句
- 支持参数绑定
- 支持命名参数

### 事务支持

- `inTransaction` 属性检查事务状态
- 支持嵌套事务 (SAVEPOINT)

### 安全整数

- `safeIntegers` 模式处理大整数
- 避免 JavaScript 精度丢失

### 虚拟表

- `CustomTable` 支持自定义虚拟表
- `EPONYMOUS_MODULE` 支持同名模块
