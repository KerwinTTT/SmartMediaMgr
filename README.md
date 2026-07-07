# SmartMediaMgr

📺 **智能媒体库管理工具** - 自动识别、整理和管理您的电影、电视剧和动漫收藏

Docker 镜像体积小巧，资源占用低，开箱即用。

## ✨ 功能特点

### 🎬 智能媒体识别
- **自动类型识别**：从文件名智能识别电影、电视剧、动漫
- **TMDB 元数据刮削**：集成 The Movie Database API，自动获取相关元数据
- **智能文件名解析**：支持多种命名格式（`S01E01`、`第1集`、`1x01` 等），自动提取标题、年份、季数、集数
- **多视频格式支持**：`.mp4`, `.mkv`, `.avi`, `.mov`, `.wmv`, `.flv`, `.webm`, `.m4v`, `.ts`, `.strm`

### 📂 目录监控与自动处理
- **多目录监控**：支持配置多个下载目录同时监控
- **实时扫描**：每分钟自动扫描新文件，支持手动触发扫描
- **灵活的文件组织**：
  - **通用目录模式**：自动将媒体分类到 `电影、剧集、动漫` 子目录
  - **指定目标模式**：所有文件移动到固定目录
- **智能去重**：自动检测重复文件，移至专用目录
- **空目录清理**：处理完成后自动清理空的源目录

### 📋 任务管理
- **可视化任务列表**：查看所有处理任务的状态（待处理、处理中、成功、失败、未识别）
- **分页浏览**：支持分页显示，方便管理大量任务
- **任务日志**：查看每个任务的详细处理日志
- **重试机制**：失败任务可重新尝试处理

### 📢 通知推送
- **NotifyCenter 集成**：媒体成功入库时自动发送通知
- **通知内容**：包含媒体标题、海报图片和入库时间
- **测试通知**：支持发送测试通知验证配置

### ⚙️ 系统设置
- **TMDB API 配置**：支持自定义 API Key、基础地址、图片地址和搜索语言
- **目录配置**：灵活设置监控目录、媒体库目录、未识别/重复文件目录
- **管理员管理**：支持修改用户名和密码
- **视频扩展名**：可自定义支持的视频文件扩展名

## 🚀 快速开始

### 使用 Docker Compose（推荐）

创建 `docker-compose.yml` 文件：

```yaml
version: "3.8"

services:
  smartmediamgr:
    image: ttt216/smartmediamgr:latest
    container_name: smartmediamgr
    ports:
      - "3400:3400"
    volumes:
      # 📊 数据库和应用数据（请勿随意删除）
      - ./data:/data
      # 📥 下载目录（待处理的媒体文件）
      - /your/download/path:/download
      # 🎥 媒体库目录（处理后的媒体文件）
      - /your/media/path:/media
    environment:
      - TZ=Asia/Shanghai
    restart: unless-stopped
```

启动服务：
```bash
docker-compose up -d
```

### 使用 Docker 命令

```bash
docker run -d \
  --name smartmediamgr \
  -p 3400:3400 \
  -v ./data:/data \
  -v /your/download/path:/download \
  -v /your/media/path:/media \
  -e TZ=Asia/Shanghai \
  --restart unless-stopped \
  ttt216/smartmediamgr:latest
```

## 📖 使用指南

### 1. 首次访问

启动服务后，在浏览器中访问：`http://your-server-ip:3400`

**默认登录信息：**
- 用户名：`admin`
- 密码：`123456`

⚠️ **重要**：首次登录后请立即在「设置」页面修改默认密码！

### 2. 配置 TMDB（推荐）

1. 访问 [TMDB 官网](https://www.themoviedb.org/) 注册账号
2. 在账号设置中申请 API Key
3. 在 SmartMediaMgr 的「设置」→「TMDB 设置」中填入 API Key
4. 保存设置

配置 TMDB 后，系统可以获取更准确的媒体元数据和海报图片。

### 3. 配置目录

在「设置」→「目录设置」中配置：

- **监控源目录根路径**：下载目录（如 `/download`）
- **媒体库根路径**：媒体存储目录（如 `/media`）
- **未识别文件目录**：无法识别的文件存放位置
- **重复文件目录**：重复文件存放位置

### 4. 添加监控目录

1. 点击导航栏的「监控」进入监控目录页面
2. 点击「添加目录」
3. 填写信息：
   - **源目录路径**：要监控的下载目录
   - **媒体目录路径**：处理后存放的目录，留空则使用通用目录模式
   - **通用目录模式**：勾选后，系统会将媒体自动分类到 movie/tv/comic 目录
4. 点击「添加」保存

### 5. 配置通知

1. 访问「设置」→「通知设置」
2. 勾选「启用通知」
3. 填写 NotifyCenter 的 URL
4. 点击「保存通知设置」
5. 点击「发送测试通知」验证配置

**通知格式示例：**
```json
{
  "title": "电影星际穿越 (2014) 入库成功",
  "img_url": "https://image.tmdb.org/t/p/w500/...",
  "datetime": "2024-07-01 15:30:00"
}
```

## 📁 卷映射说明

| 路径 | 说明 | 是否必需 |
|------|------|----------|
| `/data` | SQLite 数据库和应用数据 | ✅ 必需 |
| `/download` | 下载目录（待处理的媒体文件） | ✅ 必需 |
| `/media` | 媒体库目录（处理后的媒体文件） | ✅ 必需 |

## 📂 文件组织结构

处理后的媒体文件会按照以下结构组织：

```
/media/
├── movie/                    # 电影
│   └── 电影名 (年份)/
│       └── 电影名 (年份).ext
├── tv/                       # 电视剧
│   └── 剧集名 (年份)/
│       ├── S01/
│       │   └── 剧集名 - S01E01 - 第1集.ext
│       └── S02/
│           └── 剧集名 - S02E01 - 第1集.ext
├── comic/                    # 动漫
│   └── 动漫名 (年份)/
│       └── S01/
│           └── 动漫名 - S01E01 - 第1集.ext
├── duplicate/                # 重复文件
└── other/                    # 未识别文件
```

## 🐛 故障排除

### 媒体无法识别
- 检查文件名格式是否清晰
- 确认已配置 TMDB API Key
- 查看任务日志获取详细错误信息

### 目录监控不工作
- 确认目录路径正确且可访问
- 检查权限设置
- 在「任务」页面点击「立即扫描」手动触发

### 通知不发送
- 确认 NotifyCenter URL 正确
- 点击「发送测试通知」验证
- 查看系统日志中的错误信息

## 📢 通知服务

SmartMediaMgr 通过 [NotifyCenter](https://hub.docker.com/r/ttt216/notifycenter) 发送媒体入库通知。NotifyCenter 是一个轻量级的通知聚合服务，支持多种通知渠道。

**了解更多：** [NotifyCenter Docker Hub](https://hub.docker.com/r/ttt216/notifycenter)

## 📚 技术栈

- **后端**：Go 1.21 + Gin
- **数据库**：SQLite (GORM)
- **前端**：原生 HTML + CSS + JavaScript + Tailwind CSS
- **API**：The Movie Database (TMDB)
- **任务调度**：robfig/cron
- **容器化**：Docker (Alpine 基础镜像)

## 🛠️ 架构支持

- `linux/amd64`

## 📄 许可证

MIT License

---

**⭐ 如果这个项目对您有帮助，请给个 Star！**
