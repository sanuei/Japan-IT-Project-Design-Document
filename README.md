# 动画脚本自动生成工具 - 设计文档

## 📖 项目简介

这是一个基于思维导图的AI驱动视频脚本生成系统，能够自动生成专业的视频脚本、标题候选、概要描述和缩略图设计指南。

## 🎯 主要功能

- **思维导图编辑器**: 直观的可视化编辑界面
- **AI脚本生成**: 基于大语言模型生成专业脚本
- **多种输出**: 10个标题、概要、脚本、缩略图指南
- **提词器功能**: 专业的视频录制辅助工具
- **灵感推荐**: AI驱动的内容创意推荐

## 📋 文档结构

```
docs/
├── 基本設計書.md      # 系统架构与需求分析
├── 詳細設計書.md      # 技术实现详细设计
├── API仕様書.md       # RESTful API完整规格
├── データベース設計書.md # 数据库架构设计
└── テスト戦略書.md     # 测试计划与策略

进捗記録.md             # 项目进度记录
documentation.html      # 文档查看器
README.md              # 项目说明
```

## 🌐 文档查看器使用方法

### 方法1: 本地HTTP服务器（推荐）

```bash
# 使用Python启动HTTP服务器
python -m http.server 8000

# 或使用Node.js
npx http-server

# 然后访问: http://localhost:8000/documentation.html
```

### 方法2: 直接打开

直接在浏览器中打开 `documentation.html` 文件（可能受CORS限制影响）

## 📊 项目进展

### ✅ 已完成 (Phase 0: 设计阶段)
- [x] 基本设计书 - 系统整体架构设计
- [x] 详细设计书 - 技术实现细节
- [x] API规格书 - 45个端点的完整规格
- [x] 数据库设计书 - MongoDB/Redis/Elasticsearch设计
- [x] 测试策略书 - 综合测试计划
- [x] 进度记录 - 项目状态跟踪

### 🔄 下一步 (Phase 1: MVP开发)
- [ ] 环境搭建与配置
- [ ] 数据库实现
- [ ] 后端API开发
- [ ] 前端界面开发
- [ ] 系统集成测试

## 💻 技术栈

### 前端
- **React.js 18.x** + TypeScript
- **Material-UI** / Ant Design
- **Markmap.js** (思维导图)
- **Axios** (HTTP客户端)

### 后端
- **Node.js** + Express.js + TypeScript
- **MongoDB** + Mongoose ODM
- **Redis** (缓存与会话)
- **JWT** (认证)

### AI集成
- **OpenAI GPT-4** API
- **Anthropic Claude** API (备用)

### 基础设施
- **Docker** 容器化
- **Vercel** (前端部署)
- **Railway/Heroku** (后端部署)

## 🏗️ 系统架构

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│  Frontend   │    │  Backend    │    │   LLM API   │
│             │────│             │────│             │
│ React + TS  │    │ Node.js     │    │ OpenAI/     │
│             │    │ + Express   │    │ Claude      │
└─────────────┘    └─────────────┘    └─────────────┘
                           │
                   ┌─────────────┐
                   │  Database   │
                   │             │
                   │ MongoDB +   │
                   │ Redis +     │
                   │ Elasticsearch│
                   └─────────────┘
```

## 📸 功能预览

### 核心功能流程
1. **思维导图创建** → 用户创建或编辑思维导图
2. **AI分析处理** → 系统分析思维导图结构和内容
3. **脚本生成** → AI生成完整的视频脚本
4. **多格式输出** → 提供标题、概要、脚本、缩略图指南
5. **提词器使用** → 专业的录制辅助功能

## 🚀 快速开始

### 查看设计文档
1. 克隆项目到本地
2. 启动HTTP服务器: `python -m http.server 8000`
3. 访问: `http://localhost:8000/documentation.html`
4. 浏览各个设计文档

### 开发环境准备（即将开始）
```bash
# 克隆仓库
git clone <repository-url>
cd video-script-generator

# 安装依赖
npm install

# 启动开发服务器
npm run dev
```

## 📞 联系方式

如有任何问题或建议，请通过以下方式联系：

- 📧 Email: [your-email@example.com]
- 💬 Issues: 在GitHub上提交问题
- 📱 微信: [your-wechat-id]

## 📄 许可证

本项目采用 MIT 许可证 - 详见 [LICENSE](LICENSE) 文件

---

⭐ 如果这个项目对您有帮助，请给我们一个星标！

📝 **当前状态**: 设计阶段完成，准备进入开发阶段
🎯 **下个里程碑**: MVP版本开发完成 