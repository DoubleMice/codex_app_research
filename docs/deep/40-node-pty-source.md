# node-pty 源码实现细节分析

## 概述

node-pty 是 Codex Desktop 用于创建伪终端的核心库。

**版本**: 1.1.0
**许可**: MIT License
**作者**: Microsoft Corporation

## 1. 类继承结构

```
Terminal (基类)
    └── UnixTerminal (Unix/macOS 实现)
    └── WindowsTerminal (Windows 实现)
```

## 2. Terminal 基类

### 默认配置

```javascript
exports.DEFAULT_COLS = 80;
exports.DEFAULT_ROWS = 24;

// 流控制字符
var FLOW_CONTROL_PAUSE = '\x13';  // XOFF
var FLOW_CONTROL_RESUME = '\x11'; // XON
```

### 构造函数

```javascript
function Terminal(opt) {
    this._pid = 0;
    this._fd = 0;
    this._cols = 0;
    this._rows = 0;
    this._readable = false;
    this._writable = false;
    this._onData = new eventEmitter2_1.EventEmitter2();
    this._onExit = new eventEmitter2_1.EventEmitter2();
    this._internalee = new events_1.EventEmitter();

    // 流控制
    this.handleFlowControl = !!(opt?.handleFlowControl);
    this._flowControlPause = opt?.flowControlPause || FLOW_CONTROL_PAUSE;
    this._flowControlResume = opt?.flowControlResume || FLOW_CONTROL_RESUME;
}
```

### 类型检查

```javascript
Terminal.prototype._checkType = function (name, value, type, allowArray) {
    if (value === undefined) return;
    if (typeof value !== type) {
        throw new Error(name + " must be a " + type + " (not a " + typeof value + ")");
    }
};
```

## 3. UnixTerminal 实现

### 构造函数参数

```javascript
function UnixTerminal(file, args, opt) {
    // 默认值
    args = args || [];
    file = file || 'sh';  // DEFAULT_FILE
    opt = opt || {};
    opt.env = opt.env || process.env;

    // 终端尺寸
    this._cols = opt.cols || 80;
    this._rows = opt.rows || 24;

    // 用户/组 ID
    var uid = opt.uid ?? -1;
    var gid = opt.gid ?? -1;

    // 环境变量
    var env = assign({}, opt.env);
    env.PWD = opt.cwd || process.cwd();
    env.TERM = opt.name || env.TERM || 'xterm';

    // 编码
    var encoding = opt.encoding === undefined ? 'utf8' : opt.encoding;
}
```

### PTY Fork

```javascript
// 调用原生模块创建 PTY
var term = pty.fork(
    file,       // 可执行文件
    args,       // 参数
    parsedEnv,  // 环境变量
    cwd,        // 工作目录
    this._cols, // 列数
    this._rows, // 行数
    uid,        // 用户 ID
    gid,        // 组 ID
    (encoding === 'utf8'),  // UTF-8 模式
    helperPath, // spawn-helper 路径
    onexit      // 退出回调
);

// 创建读写流
this._socket = new tty.ReadStream(term.fd);
this._writeStream = new CustomWriteStream(term.fd, encoding);
```

### 环境变量清理

```javascript
UnixTerminal.prototype._sanitizeEnv = function (env) {
    // 清理 tmux 变量
    delete env['TMUX'];
    delete env['TMUX_PANE'];

    // 清理 screen 变量
    delete env['STY'];
    delete env['WINDOW'];

    // 清理其他终端变量
    delete env['WINDOWID'];
    delete env['TERMCAP'];
    delete env['COLUMNS'];
    delete env['LINES'];
};
```

## 4. 原生模块接口

### pty.fork()

```javascript
pty.fork(file, args, env, cwd, cols, rows, uid, gid, utf8, helperPath, onexit)
```

返回:
- `fd` - 文件描述符
- `pid` - 进程 ID
- `pty` - PTY 名称

### pty.open()

```javascript
pty.open(cols, rows)
```

返回:
- `master` - 主端文件描述符
- `slave` - 从端文件描述符
- `pty` - PTY 名称

### pty.resize()

```javascript
pty.resize(fd, cols, rows)
```

### pty.process()

```javascript
// macOS
pty.process(fd)

// Linux
pty.process(fd, pty)
```

## 5. CustomWriteStream

### 背压处理

```javascript
var CustomWriteStream = function (_fd, _encoding) {
    this._fd = _fd;
    this._encoding = _encoding;
    this._writeQueue = [];
};

CustomWriteStream.prototype.write = function (data) {
    var buffer = typeof data === 'string'
        ? Buffer.from(data, this._encoding)
        : Buffer.from(data);

    if (buffer.byteLength !== 0) {
        this._writeQueue.push({ buffer: buffer, offset: 0 });
        if (this._writeQueue.length === 1) {
            this._processWriteQueue();
        }
    }
};
```

### 写入队列处理

```javascript
CustomWriteStream.prototype._processWriteQueue = function () {
    var task = this._writeQueue[0];

    fs.write(this._fd, task.buffer, task.offset, function (err, written) {
        if (err) {
            if (err.code === 'EAGAIN') {
                // 让出事件循环，稍后重试
                setImmediate(() => this._processWriteQueue());
            } else {
                // 意外错误，停止处理
                this._writeQueue.length = 0;
                console.error('Unhandled pty write error', err);
            }
            return;
        }

        task.offset += written;
        if (task.offset >= task.buffer.byteLength) {
            this._writeQueue.shift();
        }

        // 继续处理队列
        this._processWriteQueue();
    });
};
```

## 6. 事件处理

### 数据事件

```javascript
this._socket.on('data', function (e) {
    return this._onData.fire(e);
});
```

### 退出事件

```javascript
var onexit = function (code, signal) {
    if (!this._emittedClose) {
        if (this._boundClose) return;
        this._boundClose = true;

        // macOS High Sierra 10.13.2 socket 关闭问题的超时处理
        var timeout = setTimeout(function () {
            this._socket.destroy();
        }, 200);  // DESTROY_SOCKET_TIMEOUT_MS

        this.once('close', function () {
            if (timeout !== null) clearTimeout(timeout);
            this.emit('exit', code, signal);
        });
        return;
    }
    this.emit('exit', code, signal);
};
```

### 错误处理

```javascript
this._socket.on('error', function (err) {
    // 忽略 EAGAIN 错误
    if (err.code && ~err.code.indexOf('EAGAIN')) {
        return;
    }

    this._close();

    if (!this._emittedClose) {
        this._emittedClose = true;
        this.emit('close');
    }

    // 忽略 EIO 错误 (子进程关闭时)
    if (err.code && (~err.code.indexOf('errno 5') || ~err.code.indexOf('EIO'))) {
        return;
    }

    // 抛出其他错误
    if (this.listeners('error').length < 2) {
        throw err;
    }
});
```

## 7. 生命周期方法

### destroy()

```javascript
UnixTerminal.prototype.destroy = function () {
    this._close();

    this._socket.once('close', function () {
        this.kill('SIGHUP');
    });

    this._socket.destroy();
    this._writeStream.dispose();
};
```

### kill()

```javascript
UnixTerminal.prototype.kill = function (signal) {
    try {
        process.kill(this.pid, signal || 'SIGHUP');
    } catch (e) { /* swallow */ }
};
```

### resize()

```javascript
UnixTerminal.prototype.resize = function (cols, rows) {
    if (cols <= 0 || rows <= 0 || isNaN(cols) || isNaN(rows) ||
        cols === Infinity || rows === Infinity) {
        throw new Error('resizing must be done using positive cols and rows');
    }
    pty.resize(this._fd, cols, rows);
    this._cols = cols;
    this._rows = rows;
};
```

## 8. Helper 路径处理

```javascript
var helperPath = native.dir + '/spawn-helper';
helperPath = path.resolve(__dirname, helperPath);

// Electron asar 解包处理
helperPath = helperPath.replace('app.asar', 'app.asar.unpacked');
helperPath = helperPath.replace('node_modules.asar', 'node_modules.asar.unpacked');
```

## 9. 流控制

```javascript
Terminal.prototype.write = function (data) {
    if (this.handleFlowControl) {
        // PAUSE 消息不转发到 PTY
        if (data === this._flowControlPause) {
            this.pause();
            return;
        }
        // RESUME 消息不转发到 PTY
        if (data === this._flowControlResume) {
            this.resume();
            return;
        }
    }
    // 其他数据写入 PTY
    this._write(data);
};
```

## 10. 属性访问器

| 属性 | 说明 |
|------|------|
| pid | 进程 ID |
| fd | 文件描述符 |
| cols | 列数 |
| rows | 行数 |
| process | 进程名称 |
| ptsName | PTY 名称 |
| master | 主端 (openpty) |
| slave | 从端 (openpty) |

