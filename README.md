# Text2SQL Agent Frontend

Text2SQL Agent 的 Web 对话界面。用户输入自然语言数据问题后，前端调用后端的流式查询接口，按执行顺序展示智能体处理阶段，并将最终查询结果渲染为表格。

后端项目：[text2sql-agent](../text2sql-agent)。

## 功能

- 简洁的聊天式提问界面；
- 实时展示后端通过 SSE 返回的执行阶段；
- 将最终对象数组结果自动渲染为数据表；
- 清晰呈现服务端或网络错误；
- 请求过程中禁止重复提交，消息区域自动滚动到底部。

## 技术栈

- Vue 3（`<script setup>`）
- Vite 7
- 原生 Fetch API 与 ReadableStream
- Server-Sent Events（SSE）数据格式解析

## 前置条件

- Node.js 18+（建议使用当前 LTS 版本）
- npm
- 已启动的 Text2SQL Agent 后端，默认地址为 `http://localhost:8000`

开发服务器会将 `/api` 请求代理到 `http://localhost:8000`，代理配置见 `vite.config.js`。如后端地址不同，请同步修改该文件。

## 快速开始

### 1. 安装依赖

```bash
npm install
```

### 2. 启动开发服务器

```bash
npm run dev
```

打开终端输出的本地地址即可使用。启动前请确保后端服务已经运行。

### 3. 构建生产版本

```bash
npm run build
```

构建产物输出至 `dist/`。可通过以下命令本地预览：

```bash
npm run preview
```

## 与后端的交互

前端向 `POST /api/query` 发送 JSON 请求：

```json
{
  "query": "统计去年各地区的总销售额"
}
```

后端以 `text/event-stream` 持续返回 `data: <JSON>` 事件。前端对事件作如下处理：

| 事件字段 | 界面行为 |
| --- | --- |
| `stage` | 新增或更新正在执行的步骤 |
| `result` | 将对象数组展示为结果表格 |
| `error` | 标记当前步骤失败并显示错误消息 |

## 项目结构

```text
src/
├── App.vue       # 对话界面、流式读取与事件处理
├── main.js       # Vue 应用入口
├── style.css     # 全局样式
├── assets/       # 静态资源
└── components/   # 可复用组件
vite.config.js    # Vue 插件与 API 反向代理
```

## 开发说明

- 当前 API 地址在 `src/App.vue` 中定义为相对路径 `/api/query`，通过 Vite 代理避免本地开发时的跨域问题。
- 为保证流式步骤在界面上可见，每个阶段事件有短暂的最小展示间隔。
- 后端需返回标准 SSE 分隔符（每条事件以两个换行结束），并使用 JSON 作为 `data` 内容。
