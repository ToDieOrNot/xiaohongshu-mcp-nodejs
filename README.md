# 小红书MCP服务器 - Node.js版本

基于现代化Node.js技术栈的小红书MCP（Model Context Protocol）服务器，提供企业级多账号矩阵管理和反风控功能。

## 🌟 核心特性

### 🔐 多账号管理
- **无限账号支持**：支持无限数量的小红书账号管理
- **独立环境**：每个账号使用独立的浏览器指纹和代理环境
- **状态监控**：实时监控账号登录状态和健康状况
- **Cookie管理**：自动维护登录状态，支持Cookie持久化

### 🛡️ 反风控系统
- **浏览器指纹伪造**：完整的UA、硬件、地理位置模拟
- **行为模拟**：智能鼠标轨迹、键盘输入、页面滚动
- **特征清理**：移除WebDriver、CDP等自动化检测特征
- **代理轮换**：支持HTTP/SOCKS5代理池，智能轮换

### 🚀 任务调度
- **并发执行**：支持多任务并发执行，可配置并发数
- **定时任务**：基于Cron表达式的定时任务调度
- **重试机制**：智能重试和失败恢复
- **任务监控**：实时任务状态追踪和历史记录

### 📊 数据采集
- **内容搜索**：关键词搜索、话题搜索、用户搜索
- **笔记详情**：获取完整的笔记内容和互动数据
- **用户信息**：获取用户基本信息和统计数据
- **评论采集**：获取笔记评论和互动信息
- **热门内容**：获取各分类热门内容

### 🎛️ 管理后台
- **Web界面**：基于Express.js的现代化管理界面
- **实时监控**：实时任务状态、系统资源监控
- **数据可视化**：图表展示账号数据、任务统计
- **配置管理**：可视化配置代理、指纹、任务参数

## 🏗️ 技术架构

```
┌─────────────────────────────────────────────────────────────────┐
│                        系统架构总览                              │
└─────────────────────────────────────────────────────────────────┘

    ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
    │   AI客户端群    │    │   API网关层     │    │   小红书平台     │
    │ Claude/Cursor   │◄──►│  Express+MCP    │◄──►│  多账号并发      │
    │ VSCode/CLI工具  │    │  WebSocket      │    │  自动化操作      │
    └─────────────────┘    └─────────────────┘    └─────────────────┘
                                    │
                    ┌───────────────┼───────────────┐
                    ▼               ▼               ▼
            ┌─────────────┐ ┌─────────────┐ ┌─────────────┐
            │ Directus    │ │   MySQL     │ │ 反风控引擎   │
            │ 管理后台    │ │  数据中心   │ │ Stealth+    │
            │ (零配置)    │ │ (统一前缀)  │ │ Fingerprint │
            └─────────────┘ └─────────────┘ └─────────────┘
                    │               │               │
                    └───────────────┼───────────────┘
                              ▼
            ┌─────────────────────────────────────────────────┐
            │              浏览器实例池                        │
            │ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ │
            │ │ 账号1+代理A │ │ 账号2+代理B │ │ 账号N+代理N │ │
            │ │ 指纹集A     │ │ 指纹集B     │ │ 指纹集N     │ │
            │ └─────────────┘ └─────────────┘ └─────────────┘ │
            └─────────────────────────────────────────────────┘
```

## 🛠️ 技术栈

- **运行时环境**: Node.js 18+ LTS
- **浏览器自动化**: Playwright + playwright-stealth + ghost-cursor
- **数据库**: MySQL 8.0+ (统一表前缀: `idea_xiaohongshu_`)
- **缓存**: Redis 7.0+
- **任务调度**: Bull Queue + node-cron
- **Web框架**: Express.js + Socket.IO
- **MCP协议**: JSON-RPC 2.0 标准实现
- **管理后台**: Directus CMS

## 📦 快速开始

### 1. 环境准备

```bash
# 克隆项目
git clone https://github.com/ToDieOrNot/xiaohongshu-mcp-nodejs.git
cd xiaohongshu-mcp-nodejs

# 安装依赖
npm install
```

### 2. 数据库配置

```bash
# 创建数据库
mysql -u root -p
CREATE DATABASE xiaohongshu_mcp CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

# 运行数据库迁移
npm run db:migrate
```

### 3. 环境配置

```bash
# 复制环境变量模板
cp .env.example .env

# 编辑配置文件
vim .env
```

### 4. 启动服务

```bash
# 开发模式
npm run dev

# 生产模式
npm start
```

## 🔧 配置说明

### 数据库配置
```env
# MySQL数据库配置
DB_HOST=localhost
DB_PORT=3306
DB_NAME=xiaohongshu_mcp
DB_USER=root
DB_PASSWORD=your_password

# Redis缓存配置
REDIS_HOST=localhost
REDIS_PORT=6379
REDIS_PASSWORD=
REDIS_DB=0
```

### 反风控配置
```env
# 浏览器配置
BROWSER_HEADLESS=true
BROWSER_TIMEOUT=30000
BROWSER_MAX_CONCURRENCY=5

# 代理配置
PROXY_ENABLED=true
PROXY_CHECK_INTERVAL=300000
PROXY_TIMEOUT=10000

# 指纹配置
FINGERPRINT_ENABLED=true
FINGERPRINT_CACHE_SIZE=1000
```

### MCP服务配置
```env
# MCP服务器配置
MCP_SERVER_HOST=0.0.0.0
MCP_SERVER_PORT=3000
MCP_WEBSOCKET_PORT=3001

# 任务配置
TASK_MAX_CONCURRENCY=10
TASK_RETRY_ATTEMPTS=3
TASK_RETRY_DELAY=2000
```

## 📋 API接口

### MCP协议接口

#### 账号管理
- `xiaohongshu.create_account` - 创建账号
- `xiaohongshu.get_accounts` - 获取账号列表
- `xiaohongshu.update_account` - 更新账号信息
- `xiaohongshu.delete_account` - 删除账号

#### 任务管理
- `xiaohongshu.create_task` - 创建任务
- `xiaohongshu.get_tasks` - 获取任务列表
- `xiaohongshu.cancel_task` - 取消任务
- `xiaohongshu.get_task_status` - 获取任务状态

#### 内容操作
- `xiaohongshu.search_content` - 搜索内容
- `xiaohongshu.get_post_info` - 获取笔记详情
- `xiaohongshu.get_user_info` - 获取用户信息
- `xiaohongshu.get_comments` - 获取评论
- `xiaohongshu.get_trending` - 获取热门内容

#### 发布操作
- `xiaohongshu.publish_post` - 发布笔记
- `xiaohongshu.schedule_post` - 定时发布笔记
- `xiaohongshu.get_drafts` - 获取草稿列表

### RESTful API

#### 账号管理
```http
GET    /api/accounts           # 获取账号列表
POST   /api/accounts           # 创建账号
PUT    /api/accounts/:id       # 更新账号
DELETE /api/accounts/:id       # 删除账号
```

#### 任务管理
```http
GET    /api/tasks              # 获取任务列表
POST   /api/tasks              # 创建任务
PUT    /api/tasks/:id/cancel   # 取消任务
GET    /api/tasks/:id          # 获取任务详情
```

#### 内容管理
```http
GET    /api/search             # 搜索内容
GET    /api/posts/:id          # 获取笔记详情
GET    /api/users/:id          # 获取用户信息
GET    /api/comments           # 获取评论
GET    /api/trending           # 获取热门内容
```

## 🎯 使用示例

### 1. 创建账号

```javascript
// MCP协议调用
const result = await client.call('xiaohongshu.create_account', {
  username: 'test_user',
  phone: '13800138000',
  proxy_id: 1,
  fingerprint_id: 1
});

// REST API调用
const response = await fetch('/api/accounts', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    username: 'test_user',
    phone: '13800138000',
    proxy_id: 1,
    fingerprint_id: 1
  })
});
```

### 2. 搜索内容

```javascript
// MCP协议调用
const results = await client.call('xiaohongshu.search_content', {
  keyword: '美妆',
  account_id: 1,
  limit: 20,
  sort: 'latest'
});

// REST API调用
const response = await fetch('/api/search?keyword=美妆&account_id=1&limit=20&sort=latest');
```

### 3. 发布笔记

```javascript
// MCP协议调用
const result = await client.call('xiaohongshu.publish_post', {
  account_id: 1,
  title: '测试笔记',
  content: '这是测试内容',
  images: ['image1.jpg', 'image2.jpg'],
  tags: ['测试', '美妆']
});

// REST API调用
const response = await fetch('/api/posts', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    account_id: 1,
    title: '测试笔记',
    content: '这是测试内容',
    images: ['image1.jpg', 'image2.jpg'],
    tags: ['测试', '美妆']
  })
});
```

## 🐳 Docker部署

### 使用Docker Compose

```yaml
version: '3.8'
services:
  xiaohongshu-mcp:
    build: .
    ports:
      - "3000:3000"
      - "3001:3001"
    environment:
      - NODE_ENV=production
    volumes:
      - ./data:/app/data
    depends_on:
      - mysql
      - redis

  mysql:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: root_password
      MYSQL_DATABASE: xiaohongshu_mcp
    volumes:
      - mysql_data:/var/lib/mysql

  redis:
    image: redis:7-alpine
    volumes:
      - redis_data:/data

volumes:
  mysql_data:
  redis_data:
```

### 启动服务

```bash
# 构建并启动
docker-compose up -d

# 查看日志
docker-compose logs -f xiaohongshu-mcp
```

## 🔍 监控和日志

### 日志系统
- **应用日志**：使用Winston记录所有操作和错误
- **访问日志**：记录所有API请求和响应
- **任务日志**：记录任务执行详情和状态变化
- **浏览器日志**：记录浏览器操作和网络请求

### 健康检查
```http
GET /health
```

### 监控指标
- 账号登录状态
- 任务执行统计
- 系统资源使用
- 代理可用性
- 反风控效果

## 🚨 注意事项

### 合规使用
- 请遵守小红书平台的使用条款
- 不要用于恶意刷量、垃圾营销等违规操作
- 合理设置请求频率，避免对平台造成压力

### 安全建议
- 定期更换代理IP
- 使用高质量的代理服务
- 监控账号异常行为
- 及时更新反风控策略

### 性能优化
- 根据服务器配置调整并发数
- 合理设置任务重试策略
- 定期清理过期数据
- 使用CDN加速静态资源

## 🤝 贡献指南

1. Fork项目
2. 创建功能分支 (`git checkout -b feature/AmazingFeature`)
3. 提交更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 创建Pull Request

## 📄 许可证

本项目采用MIT许可证 - 查看 [LICENSE](LICENSE) 文件了解详情。

## 📞 支持

- 📧 邮箱：qq164093410@gmail.com
- 💬 微信：ToDieOrNot
- 🐛 问题反馈：[GitHub Issues](https://github.com/ToDieOrNot/xiaohongshu-mcp-nodejs/issues)

## 🙏 致谢

- [Playwright](https://playwright.dev/) - 浏览器自动化
- [Bull Queue](https://github.com/OptimalBits/bull) - 任务队列
- [Socket.IO](https://socket.io/) - 实时通信
- [Directus](https://directus.io/) - 管理后台

---

⭐ 如果这个项目对你有帮助，请给个Star支持一下！