为一个优秀的开源项目（或者个人工具）添加清晰的说明文档是至关重要的。

鉴于 **TripFlow** 的特性（**无构建、纯静态、JSON驱动**），文档应该侧重于 **“如何修改数据”** 和 **“如何免费部署”**。

以下是为你准备的 **`README.md`** 和 **`使用手册`**，你可以直接在项目根目录下新建一个 `README.md` 文件并将内容粘贴进去。

---

# 🗺️ TripFlow (弹性路书)

> **Resilient, Static, JSON-driven Travel Itinerary.**
> 一个基于 Vue 3 + Tailwind CSS 的零构建、高韧性、纯前端旅行路书系统。

## 🚀 核心特性 (Features)

* **零构建 (No Build Step):** 纯 HTML/JS，无需 Node.js，无需 Webpack/Vite，复制即用。
* **行程即代码 (Itinerary as Code):** 所有数据存储于 `data.json`，支持 Git 版本管理。
* **高韧性设计 (Resilience):**
* **缓冲带 (Buffer):** 可视化展示行程容错时间。
* **备选方案 (Option Groups):** 支持折叠显示的 Plan B 分组。
* **混合天气源:** 优先 API 实时天气，失败自动降级为预设文本。


* **离线优先:** 关键信息（航班、酒店、保单）本地化存储，地图加载失败自动降级为列表模式。
* **移动端适配:** 专为手机浏览器优化的 UI/UX，支持 PWA 级别的流畅度。

---

## 🛠️ 快速开始 (Quick Start)

### 1. 本地运行

由于使用了 ES Modules，直接双击 `index.html` 可能无法运行。你需要一个本地静态服务器：

**方法 A: VS Code (推荐)**

1. 安装 **Live Server** 插件。
2. 右键 `index.html` -> `Open with Live Server`。

**方法 B: Python**

```bash
# 在项目目录下运行
python -m http.server
# 访问 http://localhost:8000

```

### 2. 目录结构

```text
/
├── index.html        # 核心应用 (逻辑 + UI)
├── data.json         # 数据源 (你的行程配置)
└── README.md         # 说明文档

```

---

## 📝 数据配置指南 (Data Configuration)

所有行程数据均在 `data.json` 中修改。

### 1. 节点类型 (`status`)

| 类型 | 颜色 | 语义 | 适用场景 |
| --- | --- | --- | --- |
| **anchor** | 🔴 红 | **锚点 (不可动)** | 航班、火车、预约制的餐厅/门票。支持 `buffer` 缓冲带。 |
| **standard** | 🔵 蓝 | **游标 (弹性)** | 普通景点、逛街、吃饭。 |
| **option** | ⚪️ 灰 | **备选 (可放弃)** | 只是想去看看的地方，或者 Plan B。支持 `group_id` 折叠。 |

### 2. 关键字段说明

**基础字段：**

```json
{
  "id": "unique_id_1",       // [必填] 全局唯一ID，用于状态存储
  "status": "anchor",        // [必填] anchor | standard | option
  "type": "transport",       // 图标类型: transport, sight, food, hotel, cafe
  "name": "地点名称",
  "coords": [35.689, 139.69] // [必填] 经纬度 (用于导航和地图)
}

```

**高级功能字段：**

```json
{
  // 锚点缓冲带 (仅 anchor)
  "time": "14:00",
  "buffer": "45m",           // 渲染一个45分钟的灰色缓冲条

  // 备选分组 (仅 option)
  "group_id": "rain_plan",   // 相同 ID 的 option 会被折叠在一起
  "group_title": "🌧️ 雨天方案", // 仅在组内第一个元素填写

  // 票夹功能
  "booking": {
    "code": "座位号/预订码",
    "qr_text": "二维码内容"
  }
}

```

### 3. 如何获取坐标？

1. 打开 Google Maps (网页版)。
2. 右键点击目标地点。
3. 点击第一行的数字（例如 `35.6895, 139.6917`），坐标会自动复制。
4. 粘贴到 `coords` 数组中。

---

## ☁️ 免费部署 (Deployment)

推荐使用 **GitHub Pages**，完全免费且支持 HTTPS。

1. **新建仓库：** 在 GitHub 新建一个仓库（如 `my-tokyo-trip`）。
2. **上传代码：** 将 `index.html` 和 `data.json` 推送上去。
3. **开启 Pages：**
* 进入仓库 `Settings` -> `Pages`。
* `Source` 选择 `main` 分支。
* 点击 `Save`。


4. **获取链接：** 等待 1 分钟，你将获得一个链接（如 `https://username.github.io/my-tokyo-trip/`），发给你的旅伴即可。

---

## ⚠️ 注意事项

1. **隐私安全：** 如果你的 `data.json` 包含敏感信息（如详细的护照号、家庭住址），请**不要**部署到公开的 GitHub 仓库。建议使用 Private 仓库 + Netlify/Vercel 部署，或者仅使用本地可识别的代码（如 "护照号: **********889"）。
2. **地图配额：** 默认使用 OSM + CartoDB 免费瓦片，无 API Key 限制，但在极端高并发下可能会受限。
3. **浏览器兼容性：** 支持所有现代浏览器（Chrome, Safari, Edge）。不兼容 IE。

---

## 🤝 贡献与反馈

本项目设计为“一次性交付”工具。如果你发现了 Bug，欢迎提交 Issue 或直接修改源码。

**Happy Traveling! ✈️**