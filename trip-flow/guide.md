# 📝 数据配置指南 (Data Configuration Guide)

**TripFlow** 的核心哲学是 **"Itinerary as Code" (行程即代码)**。整个应用的所有内容（标题、天气、清单、行程节点、地图坐标）都由根目录下的 `data.json` 驱动。

本指南将详细解释每个字段的含义和用法。

---

## 1. 全局元数据 (`meta`)

控制应用的基础信息和地图初始化视角。

```json
"meta": {
  "schema_version": "1.1",           // [勿动] 用于版本校验和缓存隔离
  "title": "🇯🇵 东京韧性之旅",       // 页面顶部的标题
  "date_range": "2024.05.01 - 05.05",// 显示在标题下的日期范围
  "budget": "20,000 JPY",            // (可选) 预算显示
  "location_coords": [35.6895, 139.6917] // [关键] 地图初始化的中心坐标 [纬度, 经度]
}

```

---

## 2. 行前指挥中心 (`pre_trip`)

这部分数据渲染在左侧边栏顶部的“指挥中心”区域。

### 2.1 混合天气源 (`weather_guide`)

```json
"weather_guide": "☁️ 预期多云，15-20℃"

```

* **作用：** 这是**静态兜底文案**。
* **逻辑：** 页面加载时会优先尝试调用 API 获取实时天气。如果 API 失败（或无网），则显示这段文字。

### 2.2 关键信息 (`key_info`)

用于展示航班、酒店单号、保险等。

```json
"key_info": [
  {
    "label": "🚑 紧急医疗保险",      // 标签小字
    "value": "+86-10-88889999",     // 主内容粗体
    "link": "tel:+861088889999",    // 点击跳转 (支持 http:// 或 tel:)
    "is_emergency": true            // [可选] true = 红色高亮 + 呼吸灯特效
  }
]

```

### 2.3 阶段性清单 (`checklists`)

支持按阶段切换 Tab。

```json
"checklists": [
  {
    "category": "行前",          // Tab 按钮上的文字
    "phase": "pre",              // [关键] 唯一标识 (pre | in_trip | post)
    "items": [
      { 
        "id": "c_passport",      // [关键] 全局唯一 ID，用于存储勾选状态
        "text": "护照原件", 
        "priority": "high"       // [可选] high = 红色高亮文字
      }
    ]
  }
]

```

---

## 3. 每日行程核心 (`days`)

这是数据结构最复杂的部分。每一天包含多个行程节点 (`items`)。

### 3.1 节点基础结构 (`items`)

```json
{
  "id": "d1_nex",             // [必填] 全局唯一 ID (建议格式: d{天数}_{关键词})
  "status": "anchor",         // [必填] 节点类型 (决定颜色和逻辑)
  "type": "transport",        // [必填] 图标类型
  "name": "成田特快 N'EX",     // 标题
  "coords": [35.77, 140.39],  // [必填] 坐标 [纬度, 经度]
  
  // ... 其他可选字段
}

```

### 3.2 节点类型 (`status`)详解

| 值 | 颜色 | 语义 | 必填字段 | 适用场景 |
| --- | --- | --- | --- | --- |
| **`anchor`** | 🔴 红 | **锚点 (硬性)** | `time` | 飞机、火车、预约制的餐厅/演出。不可迟到。 |
| **`standard`** | 🔵 蓝 | **游标 (弹性)** | `duration` | 景点、逛街、无需预约的吃饭。 |
| **`option`** | ⚪️ 灰 | **备选 (Plan B)** | 无 | 只有时间充裕才去，或作为暴雨备选方案。 |

### 3.3 图标类型 (`type`)映射

TripFlow 会根据 `type` 自动渲染 Emoji 图标：

* `transport` -> 🚅 (交通)
* `sight` -> 📷 (景点)
* `food` -> 🍜 (餐饮)
* `hotel` -> 🏨 (住宿)
* `cafe` -> ☕ (咖啡/休闲)
* *(默认)* -> 📍 (其他)

---

## 4. 高级功能配置 (Advanced Features)

### 4.1 缓冲带 (Buffer Strip)

仅适用于 `status: "anchor"`。用于可视化的展示“即使这里延误了，后面也还有时间”。

```json
{
  "status": "anchor",
  "name": "航班落地",
  "time": "14:30",
  "buffer": "45m"  // UI 会在卡片下方渲染一条写着 "缓冲预留: 45m" 的灰色条纹
}

```

### 4.2 备选方案分组 (Option Grouping)

仅适用于 `status: "option"`。当连续多个 Option 具有相同的 `group_id` 时，UI 会自动将它们折叠成一个手风琴组件。

```json
// 第一个备选点 (组头)
{
  "id": "opt_1",
  "status": "option",
  "name": "商场A",
  "group_id": "rain_plan_a",       // [关键] 分组标识
  "group_title": "🌧️ 雨天备选方案"  // [关键] 折叠面板显示的标题 (仅需在第一个点填写)
},
// 第二个备选点 (组员)
{
  "id": "opt_2",
  "status": "option",
  "name": "咖啡厅B",
  "group_id": "rain_plan_a"        // 相同的 ID 会被吸附进去
}

```

### 4.3 票夹功能 (Booking Wallet)

在卡片内添加一个“票务信息”下拉按钮。

```json
"booking": {
  "code": "Car 6, Seat 4A",  // 显示座位号或预订码
  "qr_text": "TICKET-ID"     // (预留字段，目前仅作文本展示，未来可生成二维码)
}

```

---

## 5. 常见问题 (FAQ)

**Q: 如何获取 `coords` 坐标？**
A: 在电脑上打开 Google Maps -> 右键点击地点 -> 点击第一行的数字（例如 `35.689, 139.691`）-> 坐标即复制成功。

**Q: 为什么修改了 `data.json` 页面没变化？**
A: 浏览器可能缓存了 JSON 文件。

1. 尝试强制刷新 (Ctrl+F5 或 Cmd+Shift+R)。
2. 如果在本地开发，确保 Live Server 正在运行。

**Q: 为什么地图是灰色的？**
A: 检查 `coords` 是否正确。如果坐标是 `[0, 0]` 或者格式错误，地图将无法定位。另外，确保你的网络能访问 OpenStreetMap 的瓦片服务器。