# 2-HTTP 协议

> 📌 **本章内容**: HTTP 协议的核心知识点，包括状态码、请求方法、报文结构、版本演进等。面试高频，必须掌握。

---

## 📑 目录

1. [HTTP 状态码](#http-状态码)
2. [HTTP 请求方法](#http-请求方法)
3. [GET vs POST](#get-vs-post)
4. [HTTP 报文结构](#http-报文结构)
5. [HTTP 版本演进](#http-版本演进)
6. [HTTP 长连接](#http-长连接)
7. [HTTP 无状态](#http-无状态)
8. [Session 与 Cookie](#session-与-cookie)

---

## HTTP 状态码

### 🔥 一句话速答（⭐⭐⭐）
1xx 信息 / 2xx 成功 / 3xx 重定向 / 4xx 客户端错误 / 5xx 服务器错误

### 📊 分类详解

#### 1xx 信息性（握手阶段的"收到！"）

| 代码 | 口令 | 含义与场景 |
| --- | --- | --- |
| 100 Continue | "继续灌" | 客户端先发送请求首部（含 `Expect: 100-continue`），收到 100 再发包体，适用于大文件分块上传 |
| 101 Switching Protocols | "换频道" | WebSocket 升级、HTTP/1.1 → HTTP/2 时常见 |

#### 2xx 成功（服务器"OK！"）

| 代码 | 口令 | 含义与典型用法 |
| --- | --- | --- |
| 200 OK | "一切安好" | 最常见；GET/POST 都可能返回 |
| 201 Created | "新建完成" | POST `/users` 创建用户；响应中一般给 `Location` 头 |
| 202 Accepted | "我收下先" | 异步任务排队，如上传转码 |
| 204 No Content | "办完了，没料" | 删除成功、不需要返回体 |
| 206 Partial Content | "分段寄" | 断点续传，`Range: bytes=...` |

#### 3xx 重定向（服务器"去那边"）

| 代码 | 口令 | 含义与差异点 |
| --- | --- | --- |
| 301 Moved Permanently | "搬家永久" | 浏览器会缓存；SEO 友好 |
| 302 Found | "临时搬家" | 老版浏览器照样改成 GET；早期最滥用 |
| 303 See Other | "换 GET 拿" | POST 后重定向到 GET 资源（支付回跳常见） |
| 304 Not Modified | "缓存命中" | `If-None-Match` / `If-Modified-Since` 协商缓存 |
| 307 Temporary Redirect | "临时搬家但保持方法" | 强制客户端使用原请求方法（如 POST 仍为 POST） |
| 308 Permanent Redirect | "永久搬家且保持方法" | 301 + 保留方法；HTTP/2 推广 |

#### 4xx 客户端错误（服务器"你错了"）

| 代码 | 口令 | 典型场景 |
| --- | --- | --- |
| 400 Bad Request | "报文烂了" | JSON 语法错、请求头过大等 |
| 401 Unauthorized | "先登录" | 缺失/失效 Token；配合 `WWW-Authenticate` |
| 403 Forbidden | "我认得你，但不给" | 鉴权通过但无权限；IP 黑名单 |
| 404 Not Found | "地址错了" | 经典"404 页面" |
| 405 Method Not Allowed | "动手方式错" | PUT 到只允许 GET 的 URL |
| 408 Request Timeout | "你太慢了" | 客户端未在时限内发完整请求 |
| 409 Conflict | "版本冲突" | 编辑冲突、资源重复创建 |
| 410 Gone | "永别了" | 资源永久删除，不会再有 |
| 413 Payload Too Large | "包太大" | 上传超过限制 |
| 415 Unsupported Media Type | "格式不懂" | `Content-Type` 不被接受 |
| 429 Too Many Requests | "别刷了" | 限流/防刷必备 |

#### 5xx 服务端错误（服务器"我挂了"）

| 代码 | 口令 | 说明与排查方向 |
| --- | --- | --- |
| 500 Internal Server Error | "后台炸了" | 日志第一时间看 stack trace |
| 501 Not Implemented | "功能未上" | 服务器不支持当前方法 |
| 502 Bad Gateway | "网关炸了" | 上游服务无响应或反向代理配置错误 |
| 503 Service Unavailable | "临时停业" | 服务暂时不可用（维护、限流），需通过 `Retry-After` 头告知重试时间 |
| 504 Gateway Timeout | "上游超时" | 反向代理等待后端超时 |
| 505 HTTP Version Not Supported | "版本太古" | 服务器不支持请求里的 HTTP 版本 |

### 💡 记忆口诀
```
1xx 我收到了；2xx 全搞定；
3xx 去别处；4xx 你先改；
5xx 我先修。
```

### 🎯 高频追问

**Q1: 301 和 302 的区别？**
- **301 永久重定向**: 浏览器会缓存，搜索引擎会更新索引，SEO 友好
- **302 临时重定向**: 不缓存，搜索引擎不更新索引

**比喻**: 301 是嫁人的新垣结衣，302 是有男朋友的长泽雅美 😄

**Q2: 304 是什么场景？**
- **协商缓存命中**: 资源未修改，使用本地缓存
- **实现方式**:
  - `If-None-Match` + `ETag`（实体标签）
  - `If-Modified-Since` + `Last-Modified`（最后修改时间）

**Q3: 401 和 403 的区别？**
- **401 Unauthorized**: "你还没登录"，需要身份验证，响应头会带 `WWW-Authenticate`
- **403 Forbidden**: "我知道你是谁，但你没权限"，即使登录也不行

### 💼 面试加分 Tips
1. 断点续传要提 **206 + `Range`/`Content-Range`**
2. RESTful API 创建资源用 **201 + `Location`** 返回新资源地址
3. 限流场景用 **429 + `Retry-After`** 告知客户端何时重试

---

## HTTP 请求方法

### 🔥 一句话速答（⭐⭐⭐）
GET/POST/PUT/DELETE/HEAD/OPTIONS/PATCH/TRACE/CONNECT

### 📊 常用方法详解

| 方法 | 作用 | 幂等性 | 安全性 | 典型场景 |
|------|------|--------|--------|----------|
| **GET** | 获取资源 | ✅ 是 | ✅ 是 | 查询列表、查看详情 |
| **POST** | 提交数据 | ❌ 否 | ❌ 否 | 创建资源、提交表单 |
| **PUT** | 替换资源 | ✅ 是 | ❌ 否 | 更新整个资源 |
| **DELETE** | 删除资源 | ✅ 是 | ❌ 否 | 删除数据 |
| **PATCH** | 部分更新 | ❌ 否 | ❌ 否 | 修改部分字段 |
| **HEAD** | 获取头部 | ✅ 是 | ✅ 是 | 检查资源是否存在 |
| **OPTIONS** | 获取支持的方法 | ✅ 是 | ✅ 是 | CORS 预检请求 |

### 🎯 幂等性（⭐⭐⭐）

**什么是幂等？**
> 无论操作执行多少次，结果都是相同的。

**幂等方法**:
- ✅ **GET**: `GET /users/1` 查询多次结果一样
- ✅ **PUT**: `PUT /users/1` 替换多次结果一样
- ✅ **DELETE**: `DELETE /users/1` 第一次返回 200，后续返回 404，但最终状态一致

**非幂等方法**:
- ❌ **POST**: `POST /orders` 每次调用都会创建新订单
- ❌ **PATCH**: `PATCH /users/1 {age: age+1}` 每次调用年龄都会加 1

### 🎯 高频追问

**Q1: HTTP 的 GET 方法可以实现写操作吗？**
- 技术上可以，但**不推荐**
- 违反 RESTful 设计原则
- 存在安全风险（CSRF 攻击）
- 正确做法：写操作用 POST/PUT/DELETE

**示例**（反面教材）:
```
❌ GET /users/delete?id=1  // 错误！
✅ DELETE /users/1         // 正确！
```

**Q2: OPTIONS 方法的作用？**
- 用于 **CORS 预检请求**（Preflight Request）
- 浏览器在跨域发送 PUT/DELETE 等请求前，先发送 OPTIONS 询问服务器是否允许
- 服务器返回 `Access-Control-Allow-Methods` 等头部

---

## GET vs POST

### 🔥 一句话速答（⭐⭐⭐）
GET 获取数据参数在 URL，POST 提交数据参数在 Body，POST 更安全

### 📊 详细对比

| 对比维度 | GET | POST |
|---------|-----|------|
| **语义** | 获取资源 | 提交数据 |
| **参数位置** | URL 查询字符串 | 请求 Body |
| **参数长度** | 浏览器限制（2-8KB） | 理论无限制 |
| **安全性** | 参数暴露在 URL，不安全 | 参数在 Body，相对安全 |
| **缓存** | 浏览器会缓存 | 不会缓存 |
| **书签** | 可以添加书签 | 不能添加书签 |
| **幂等性** | ✅ 幂等 | ❌ 非幂等 |
| **历史记录** | 参数保存在历史记录 | 不保存 |
| **编码方式** | URL 编码 | 支持多种（form-data/json/xml） |

### 📝 示例对比

**GET 请求**:
```http
GET /search?keyword=java&page=1 HTTP/1.1
Host: www.example.com
```

**POST 请求**:
```http
POST /users HTTP/1.1
Host: www.example.com
Content-Type: application/json

{
  "name": "张三",
  "age": 25
}
```

### 🎯 高频追问

**Q1: GET 的长度限制是多少？**
- **HTTP 协议本身没有限制**
- 限制来自浏览器和服务器：
  - IE: 2083 字符
  - Chrome: 8182 字符
  - Firefox: 65536 字符
- 服务器也有限制（如 Nginx 默认 4-8KB）

**Q2: POST 真的比 GET 安全吗？**
- **相对安全**，但不是绝对安全
- GET 参数暴露在 URL，容易被日志记录、浏览器历史记录
- POST 参数在 Body，但抓包工具仍能看到
- **真正安全**: 使用 HTTPS + 参数加密

**Q3: GET 和 POST 底层有什么区别？**
- 都是基于 TCP 连接
- **传闻**: GET 产生一个 TCP 数据包，POST 产生两个（先发 Header，再发 Body）
- **真相**: 这取决于浏览器实现，不是标准规定

---

## HTTP 报文结构

### 🔥 一句话速答（⭐⭐）
请求报文：请求行 + 请求头 + 空行 + 消息体  
响应报文：状态行 + 响应头 + 空行 + 消息体

### 📊 请求报文结构

```http
GET /index.html HTTP/1.1              ← 请求行
Host: www.javabetter.cn               ← 请求头开始
Accept: text/html
User-Agent: Mozilla/5.0
Cookie: sessionid=abc123
                                       ← 空行（\r\n）
name=张三&age=25                       ← 消息体（可选）
```

**请求行三要素**:
1. 请求方法（GET/POST/PUT...）
2. 请求 URL（/index.html）
3. 协议版本（HTTP/1.1）

**常见请求头**:
- `Host`: 目标服务器域名
- `User-Agent`: 客户端信息（浏览器类型）
- `Accept`: 可接收的内容类型
- `Content-Type`: 消息体的类型（POST/PUT 才有）
- `Cookie`: 客户端 Cookie
- `Authorization`: 认证信息（如 JWT Token）
- `Range`: 请求部分内容（断点续传）

### 📊 响应报文结构

```http
HTTP/1.1 200 OK                       ← 状态行
Content-Type: text/html               ← 响应头开始
Content-Length: 1024
Set-Cookie: sessionid=xyz789
Cache-Control: max-age=3600
                                       ← 空行
<html>                                 ← 消息体
  <body>沉默王二很天真</body>
</html>
```

**状态行三要素**:
1. 协议版本（HTTP/1.1）
2. 状态码（200）
3. 状态消息（OK）

**常见响应头**:
- `Content-Type`: 响应内容类型
- `Content-Length`: 响应内容长度
- `Set-Cookie`: 设置 Cookie
- `Cache-Control`: 缓存策略
- `ETag`: 资源标识（协商缓存）
- `Last-Modified`: 最后修改时间
- `Location`: 重定向地址（3xx 状态码）

---

## HTTP 版本演进

### 🔥 一句话速答（⭐⭐⭐）
HTTP/1.0 短连接 → HTTP/1.1 长连接 → HTTP/2.0 多路复用+二进制 → HTTP/3.0 基于 QUIC(UDP)

### 📊 版本对比

| 特性 | HTTP/1.0 | HTTP/1.1 | HTTP/2.0 | HTTP/3.0 |
|------|----------|----------|----------|----------|
| **连接方式** | 短连接 | 长连接 | 多路复用 | QUIC (UDP) |
| **默认端口** | 80 | 80 | 80 | 443 |
| **传输格式** | 文本 | 文本 | 二进制帧 | 二进制 |
| **头部压缩** | ❌ | ❌ | ✅ HPACK | ✅ QPACK |
| **服务端推送** | ❌ | ❌ | ✅ | ✅ |
| **队头阻塞** | 严重 | 有改善 | 应用层解决 | 彻底解决 |

### 📝 HTTP/1.0（1996）

**特点**:
- 默认**短连接**（每次请求都要建立 TCP 连接）
- 可以通过 `Connection: keep-alive` 开启长连接

**问题**:
- 频繁建立/断开 TCP 连接，开销大

### 📝 HTTP/1.1（1999）

**改进**:
- ✅ **默认长连接**（`Connection: keep-alive`）
- ✅ **流水线**（Pipelining）：可以在一个连接上发送多个请求
- ✅ **Host 头部**：支持虚拟主机（一个 IP 部署多个网站）
- ✅ **分块传输**（Chunked Transfer）：不需要提前知道内容长度

**问题**:
- **队头阻塞**（Head-of-Line Blocking）：前一个请求阻塞后续请求

### 📝 HTTP/2.0（2015）

**重大改进**:
- ✅ **二进制分帧**：更高效的解析
- ✅ **多路复用**：一个 TCP 连接同时处理多个请求/响应
- ✅ **头部压缩**（HPACK）：减少冗余头部
- ✅ **服务端推送**（Server Push）：主动推送资源

**解决问题**:
- 彻底解决应用层的队头阻塞
- 减少延迟，提升性能

**遗留问题**:
- 仍基于 TCP，TCP 层的队头阻塞未解决

### 📝 HTTP/3.0（2022）

**革命性改变**:
- ✅ 基于 **QUIC 协议**（Quick UDP Internet Connections）
- ✅ 使用 **UDP** 而非 TCP
- ✅ **0-RTT** 连接建立（首次连接除外）
- ✅ 彻底解决队头阻塞

**优势**:
- 更快的连接建立
- 更好的移动网络支持（IP 切换无需重连）

### 🎯 高频追问

**Q1: HTTP/2 多路复用解决了什么问题？**
- 解决了 HTTP/1.x 的队头阻塞问题
- 一个 TCP 连接可以并发多个请求/响应，互不干扰
- 不再需要多个 TCP 连接，减少握手开销

**Q2: 为什么 HTTP/3 要基于 UDP？**
- TCP 存在队头阻塞：一个包丢失会阻塞整个连接
- UDP 无连接、无状态，更灵活
- QUIC 在 UDP 之上实现了可靠传输（类似 TCP），但避免了 TCP 的缺陷

**Q3: 目前使用最广泛的是哪个 HTTP 版本？**
- **HTTP/2.0**（2022 年占比约 47%）
- HTTP/3.0 正在快速普及（Google、Cloudflare 等大厂已支持）

---

## HTTP 长连接

### 🔥 一句话速答（⭐⭐）
通过 `Connection: keep-alive` 保持 TCP 连接，减少建立/断开连接的开销

### 📝 详细说明

**HTTP/1.0**:
- 默认短连接，每次请求后关闭
- 可以手动设置 `Connection: keep-alive` 开启长连接

**HTTP/1.1**:
- 默认开启长连接
- 可以通过 `Connection: close` 关闭

### ⏱️ 超时机制

**HTTP 层超时**:
- 通过 `Keep-Alive: timeout=5, max=100` 设置
  - `timeout=5`: 5 秒无数据则关闭连接
  - `max=100`: 最多处理 100 个请求后关闭

**TCP 层超时**:
- `tcp_keepalive_time = 1800`（秒）: 闲置多久后发送探测包
- `tcp_keepalive_intvl = 15`: 探测包间隔
- `tcp_keepalive_probes = 5`: 探测次数，超过则关闭连接

### 🎯 高频追问

**Q: 长连接会一直保持吗？**
- 不会，有超时机制
- 服务器会主动关闭长时间空闲的连接
- 客户端也可以主动关闭

---

## HTTP 无状态

### 🔥 一句话速答（⭐⭐）
每个请求独立，服务器不保存客户端状态，通过 Cookie/Session/Token 维持状态

### 📝 什么是无状态？

**HTTP 协议本身不记录任何客户端信息**:
- 每次请求都是独立的
- 服务器不知道"你是谁"、"你刚才做了什么"

**比喻**:
> 我家大门常打开，是人是神都欢迎，我不在乎，只要按规矩办事，一切好说。

### 🛠️ 如何记录状态？

#### 方法 1: Cookie
- 服务器通过 `Set-Cookie` 响应头设置 Cookie
- 客户端在后续请求中自动携带 Cookie

```http
// 服务器响应
Set-Cookie: sessionid=abc123; Path=/; HttpOnly

// 客户端请求
Cookie: sessionid=abc123
```

#### 方法 2: Session
- 服务器生成唯一 Session ID，存储在 Cookie 中
- 服务器端维护 Session 数据（内存/Redis）

#### 方法 3: Token（JWT）
- 服务器签发 Token，包含用户信息
- 客户端在请求头中携带 Token

```http
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

---

## Session 与 Cookie

### 🔥 一句话速答（⭐⭐⭐）
Cookie 存客户端，Session 存服务端，Session ID 通过 Cookie 传递

### 📊 详细对比

| 对比维度 | Cookie | Session |
|---------|--------|---------|
| **存储位置** | 客户端浏览器 | 服务器端 |
| **存储内容** | 只能存字符串（ASCII） | 可存任意对象 |
| **大小限制** | 单个 4KB | 无明显限制（受内存限制） |
| **安全性** | 相对不安全（易被窃取） | 相对安全 |
| **有效期** | 可设置长期有效 | 关闭浏览器或超时失效 |
| **跨域** | 受同源策略限制 | 不受限（但需共享存储） |

### 🔗 Session 和 Cookie 的关系

![Session 和 Cookie](https://cdn.nlark.com/yuque/0/2025/jpeg/49518801/1746090118564-b64d51c1-3f27-4475-a6a0-f147e44caa45.jpg.jpeg)

**工作流程**:

![Session 和 Cookie 工作流程](https://cdn.nlark.com/yuque/0/2025/jpeg/49518801/1746090118761-a2d4eb53-eace-4e62-a0d2-30e35d1cfd25.jpg.jpeg)

1. 用户首次访问，服务器创建 Session，生成 Session ID
2. 服务器通过 `Set-Cookie` 将 Session ID 返回给客户端
3. 客户端保存 Session ID 到 Cookie
4. 后续请求自动携带 Cookie（Session ID）
5. 服务器根据 Session ID 查找 Session 数据

### 🎯 高频追问

**Q1: 分布式环境下 Session 怎么处理？**

**问题**: 负载均衡后，用户请求可能分配到不同服务器，Session 无法共享

**解决方案**:
1. **Session 粘滞**（Sticky Session）
   - 同一用户的请求总是路由到同一台服务器
   - 缺点：负载不均衡，某台服务器挂了会丢失 Session

2. **Session 复制**
   - 各服务器之间同步 Session
   - 缺点：开销大，延迟高

3. **集中式 Session 存储**（推荐）
   - 使用 Redis/Memcached 等存储 Session
   - 所有服务器共享同一个 Session 存储

![Session 共享](https://cdn.nlark.com/yuque/0/2025/jpeg/49518801/1746090119315-bfc936c9-061e-4ae8-8ead-478ed26539a2.jpg.jpeg)

**Q2: 客户端禁用 Cookie 怎么办？**

**问题**: 浏览器禁用 Cookie，Session ID 无法存储

**解决方案**:
1. **URL 重写**: 将 Session ID 拼接到 URL
   ```
   https://example.com/page?sessionid=abc123
   ```

2. **请求头传递**: 将 Session ID 放到自定义请求头
   ```http
   X-Session-ID: abc123
   ```

3. **本地存储**: 使用 localStorage/sessionStorage 存储
   ```javascript
   localStorage.setItem('sessionId', 'abc123');
   ```

**Q3: Cookie 的安全属性有哪些？**

```http
Set-Cookie: sessionid=abc123; 
            Path=/; 
            Domain=example.com; 
            Max-Age=3600;
            HttpOnly; 
            Secure; 
            SameSite=Strict
```

- **HttpOnly**: 禁止 JavaScript 访问，防止 XSS 攻击
- **Secure**: 仅通过 HTTPS 传输
- **SameSite**: 防止 CSRF 攻击
  - `Strict`: 完全禁止跨站发送
  - `Lax`: GET 请求允许跨站
  - `None`: 允许跨站（需配合 Secure）

---

## 🎓 本章总结

### 必背知识点
- ✅ HTTP 状态码（重点 200/301/302/304/400/404/500）
- ✅ GET vs POST 的 5 个区别
- ✅ HTTP 版本演进（1.0 → 1.1 → 2.0 → 3.0）
- ✅ 幂等性概念及幂等方法
- ✅ Session vs Cookie 的区别和关联

### 加分项
- 📌 断点续传实现（206 + Range）
- 📌 分布式 Session 解决方案
- 📌 HTTP/2 多路复用原理
- 📌 Cookie 安全属性

### 面试频率
- 🔥🔥🔥🔥🔥 HTTP 状态码
- 🔥🔥🔥🔥🔥 GET vs POST
- 🔥🔥🔥🔥 HTTP 版本区别
- 🔥🔥🔥 幂等性
- 🔥🔥🔥 Session vs Cookie

---

**相关笔记**:
- [[0-计算机网络面试核心]] - 核心知识点清单
- [[3-HTTPS与加密]] - HTTPS 加密原理
- [[4-TCP协议详解]] - TCP 底层机制

---

_最后更新：2026-02-11_  
_来源：整合自 JavaBetter、牛客面经、小林 coding_
