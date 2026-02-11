# WebSocket 客户端实现分析

## 概述

Codex App 使用 WebSocket 与 App Server 进行实时通信。

## WebSocket 客户端类

### 类定义
```javascript
// 行号: 59911-59977
class Zce {
  constructor(e) {
    this.options = e;
  }
  kind = "websocket";
  ws = null;
}
```

### 连接建立
```javascript
async start() {
  const r = globalThis.WebSocket;
  const i = new r(this.options.websocketUrl, {
    headers: this.options.headers,
  });
  this.ws = i;
}
```

### 事件处理
```javascript
// onopen - 连接成功
i.onopen = () => {
  a || ((a = !0), e());
};

// onerror - 连接错误
i.onerror = () => {
  const s = new Error("Codex app-server websocket encountered an error");
  this.options.onErrorMessage(s.message);
};

// onmessage - 接收消息
i.onmessage = (s) => {
  const c = s.data;
  for (const u of iue(c)) this.options.onMessageLine(u);
};

// onclose - 连接关闭
i.onclose = (s) => {
  const c = aue(s);
  this.options.onClosed({
    kind: "websocket",
    code: c.code,
    reason: c.reason,
  });
};
```

### 消息发送
```javascript
// 行号: 59960-59964
send(e) {
  if (!this.ws || this.ws.readyState !== 1)
    throw new Error("Codex app-server websocket is not available");
  this.ws.send(JSON.stringify(e));
}
```

### 连接关闭
```javascript
// 行号: 59965-59976
stop() {
  if (this.ws) {
    this.ws.onopen = null;
    this.ws.onmessage = null;
    this.ws.onerror = null;
    this.ws.onclose = null;
    try {
      this.ws.close();
    } catch {}
    this.ws = null;
  }
}
```

## URL 配置

### 环境变量
```javascript
// 行号: 59984-59987
function Xce(t) {
  if (process.env.CODEX_APP_SERVER_FORCE_CLI === "1") return null;
  const e = process.env.CODEX_APP_SERVER_WS_URL ?? t.websocket_url;
  return !e || typeof e != "string" ? null : Jce(e);
}
```

## 关键代码位置

| 功能 | 行号 | 说明 |
|------|------|------|
| 客户端类 | 59911-59977 | Zce 类 |
| 连接建立 | 59917-59958 | start 方法 |
| 消息发送 | 59960-59964 | send 方法 |
| URL 解析 | 59984-59987 | Xce 函数 |

## 总结

WebSocket 客户端的核心设计：
1. **JSON 序列化** - 消息使用 JSON 格式
2. **事件回调** - onMessageLine/onClosed/onErrorMessage
3. **环境变量** - 支持自定义 WS URL
4. **清理机制** - stop 时移除所有监听器
