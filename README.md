# XPS 兑换码后端 - Cloudflare Workers

## 部署步骤

### 1. 安装 Wrangler CLI

```bash
npm install -g wrangler
```

### 2. 登录 Cloudflare

```bash
wrangler login
```

### 3. 创建 KV 命名空间

```bash
# 创建兑换码存储
wrangler kv:namespace create "CODES"

# 创建使用记录存储
wrangler kv:namespace create "USAGE"
```

执行后会输出类似：
```
✨ Success!
Add the following to your configuration file:
[[kv_namespaces]]
binding = "CODES"
id = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
```

把 `id` 复制到 `wrangler.toml` 文件中。

### 4. 设置管理员密钥

```bash
wrangler secret put ADMIN_KEY
```

输入一个安全的随机字符串（用于生成兑换码）。

### 5. 部署 Worker

```bash
wrangler deploy
```

部署成功后会输出 Worker URL，例如：
```
https://xps-redeem.your-account.workers.dev
```

### 6. 更新插件配置

把部署后的 URL 更新到插件的 `background.js`：

```javascript
const BACKEND_URL = 'https://xps-redeem.your-account.workers.dev';
```

同时更新生成工具的配置：

```javascript
// generate-code.js 和 batch-generate.js
const CONFIG = {
  workerUrl: 'https://xps-redeem.your-account.workers.dev',
  adminKey: process.env.XPS_ADMIN_KEY || ''
};
```

---

## 生成兑换码

### 方式1：交互式生成（推荐）

```bash
node generate-code.js
```

按提示输入：
- 管理员密钥
- 会员类型（basic/pro/proPlus）
- 有效期（天）
- 最大使用次数

### 方式2：批量生成

```bash
# 生成10个 PRO 会员码，有效期30天
node batch-generate.js --count 10 --type pro --days 30

# 生成5个 PRO+ 会员码，有效期365天，可使用3次
node batch-generate.js --count 5 --type proPlus --days 365 --uses 3

# 使用环境变量设置密钥
export XPS_ADMIN_KEY=your_key
node batch-generate.js --count 20 --type basic --days 30
```

### 方式3：curl 命令

```bash
curl -X POST https://xps-redeem.your-account.workers.dev/generate \
  -H "Content-Type: application/json" \
  -d '{
    "adminKey": "你的管理员密钥",
    "membership": "pro",
    "durationDays": 30,
    "maxUses": 1
  }'
```

参数说明：
- `membership`: `basic`, `pro`, `proPlus`
- `durationDays`: 会员有效期（天）
- `maxUses`: 最大使用次数（默认1次）

返回示例：
```json
{
  "success": true,
  "code": "PS-A1B2C-D3E4F",
  "membership": "pro",
  "durationDays": 30,
  "maxUses": 1
}
```

---

## 工作流程

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   客户付款   │────▶│  你生成兑换码  │────▶│  发送给客户  │
└─────────────┘     └─────────────┘     └─────────────┘
                           │
                           ▼
                    ┌─────────────┐
                    │  Cloudflare  │
                    │   Worker    │
                    │  (存储兑换码) │
                    └─────────────┘
                           ▲
                           │
┌─────────────┐     ┌─────────────┐
│  客户在插件   │────▶│  验证兑换码   │
│   输入兑换码  │     │  (绑定设备)   │
└─────────────┘     └─────────────┘
```

---

## API 接口

### POST /validate
验证兑换码

**请求体：**
```json
{
  "code": "PS-A1B2C-D3E4F",
  "deviceId": "设备唯一标识"
}
```

**响应：**
```json
{
  "valid": true,
  "membership": "pro",
  "expiresAt": 1234567890123,
  "durationDays": 30
}
```

### POST /generate
生成新兑换码（需要管理员密钥）

**请求体：**
```json
{
  "adminKey": "管理员密钥",
  "membership": "pro",
  "durationDays": 30,
  "maxUses": 1
}
```

### POST /stats
查看统计（需要管理员密钥）

**请求体：**
```json
{
  "adminKey": "管理员密钥"
}
```

---

## 免费额度

Cloudflare Workers 免费版：
- 每天 100,000 次请求
- 每天 1,000 次 KV 读取
- 每天 1,000 次 KV 写入
- 每天 1,000 次 KV 删除

对于兑换码验证场景完全够用。

---

## 安全建议

1. **管理员密钥**要足够复杂，不要泄露
2. **定期更换**管理员密钥
3. **监控** `/stats` 接口查看异常使用
4. 考虑添加**速率限制**防止暴力破解
5. **不要**把 `wrangler.toml` 和密钥提交到 Git

---

## 故障排查

### Worker 部署失败
检查 `wrangler.toml` 中的 KV namespace ID 是否正确。

### 兑换码验证失败
- 检查插件中的 `BACKEND_URL` 是否正确
- 检查 Worker 是否正常运行
- 检查兑换码是否已过期或达到使用次数

### CORS 错误
确保 Worker 代码中的 CORS 配置正确。

### 生成工具报错
- 检查 `workerUrl` 是否已更新
- 检查管理员密钥是否正确
- 检查网络连接
