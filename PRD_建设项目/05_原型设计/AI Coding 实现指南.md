# AI Coding 实现指南

> 项目：罗布人村寨 AI 接驳车调度系统（管理大屏）
> 用途：指导 AI Coding 工具生成可交互 HTML 原型
> 创建时间：2026-04-06

---

## 1. 整体布局架构

### 1.1 页面结构（HTML）

```html
<div class="app">
  <!-- 顶部栏 -->
  <header class="header">
    <div class="header-left">系统标题</div>
    <div class="header-center">运营指标</div>
    <div class="header-right">系统状态</div>
  </header>
  
  <!-- 主内容区 -->
  <main class="main">
    <!-- 左侧信息区 25% -->
    <aside class="sidebar-left">
      <div class="tabs">
        <button class="tab active">车辆</button>
        <button class="tab">站点</button>
      </div>
      <div class="list-container">
        <!-- 车辆列表或站点列表 -->
      </div>
    </aside>
    
    <!-- 中间地图区 60% -->
    <section class="map-container">
      <div id="amap"></div> <!-- 高德地图容器 -->
      <!-- 车辆图标、站点标记通过高德API添加 -->
    </section>
    
    <!-- 右侧消息区 15% -->
    <aside class="sidebar-right">
      <div class="message-header">消息中心</div>
      <div class="message-list">
        <!-- 消息卡片列表 -->
      </div>
    </aside>
  </main>
</div>
```

### 1.2 整体尺寸规范

```css
/* 根元素 */
.app {
  width: 100vw;
  height: 100vh;
  display: flex;
  flex-direction: column;
  background: #0a0e1a; /* 深色背景 */
  color: #ffffff;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'PingFang SC', sans-serif;
}

/* 顶部栏 */
.header {
  height: 64px;
  padding: 0 24px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  background: linear-gradient(90deg, #0d1b2a 0%, #1b263b 100%);
  border-bottom: 1px solid rgba(255,255,255,0.1);
}

/* 主内容区 */
.main {
  flex: 1;
  display: flex;
  overflow: hidden;
}

/* 左侧信息区 */
.sidebar-left {
  width: 25%;
  min-width: 320px;
  display: flex;
  flex-direction: column;
  background: #111827;
  border-right: 1px solid rgba(255,255,255,0.05);
}

/* 中间地图区 */
.map-container {
  flex: 1;
  position: relative;
  background: #0f1419;
}

/* 右侧消息区 */
.sidebar-right {
  width: 15%;
  min-width: 200px;
  display: flex;
  flex-direction: column;
  background: #111827;
  border-left: 1px solid rgba(255,255,255,0.05);
}
```

---

## 2. 设计系统（Design System）

### 2.1 颜色规范

```css
:root {
  /* 背景色 */
  --bg-primary: #0a0e1a;      /* 主背景 */
  --bg-secondary: #111827;    /* 侧栏背景 */
  --bg-tertiary: #1f2937;     /* 卡片背景 */
  --bg-hover: rgba(255,255,255,0.05); /* 悬停背景 */
  --bg-active: rgba(255,255,255,0.1);  /* 选中背景 */
  
  /* 文字色 */
  --text-primary: #ffffff;
  --text-secondary: rgba(255,255,255,0.7);
  --text-tertiary: rgba(255,255,255,0.5);
  
  /* 状态色 */
  --status-success: #10b981;   /* 绿色-正常 */
  --status-warning: #f59e0b;   /* 黄色-拥挤 */
  --status-danger: #ef4444;    /* 红色-危险 */
  --status-info: #3b82f6;      /* 蓝色-预警 */
  --status-offline: #6b7280;   /* 灰色-离线 */
  
  /* 车辆满载率 */
  --vehicle-empty: #10b981;    /* 空载 <30% */
  --vehicle-half: #3b82f6;     /* 半载 30-70% */
  --vehicle-full: #f97316;     /* 满载 >70% */
  
  /* 边框 */
  --border-color: rgba(255,255,255,0.1);
}
```

### 2.2 字体规范

```css
:root {
  --font-size-xs: 12px;
  --font-size-sm: 14px;
  --font-size-base: 16px;
  --font-size-lg: 20px;
  --font-size-xl: 24px;
  --font-size-2xl: 32px;
  
  --font-weight-normal: 400;
  --font-weight-medium: 500;
  --font-weight-bold: 600;
}
```

### 2.3 间距规范

```css
:root {
  --space-xs: 4px;
  --space-sm: 8px;
  --space-md: 16px;
  --space-lg: 24px;
  --space-xl: 32px;
  
  --border-radius-sm: 4px;
  --border-radius-md: 8px;
  --border-radius-lg: 12px;
}
```

### 2.4 动画规范

```css
:root {
  --transition-fast: 150ms ease;
  --transition-normal: 300ms ease;
  --transition-slow: 500ms ease;
}

/* 常用动画 */
@keyframes pulse {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.5; }
}

@keyframes blink-slow {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.3; }
}

@keyframes blink-fast {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.2; }
}

@keyframes ripple {
  0% { transform: scale(1); opacity: 1; }
  100% { transform: scale(2); opacity: 0; }
}
```

---

## 3. 顶部栏详细设计

### 3.1 HTML 结构

```html
<header class="header">
  <div class="header-left">
    <div class="logo">🔶</div>
    <h1 class="title">罗布人村寨 AI 调度系统</h1>
  </div>
  
  <div class="header-center">
    <div class="stat-item">
      <span class="stat-label">今日接待</span>
      <span class="stat-value">1,247</span>
      <span class="stat-change">+12%</span>
    </div>
    <div class="stat-divider"></div>
    <div class="stat-item">
      <span class="stat-label">在线车辆</span>
      <span class="stat-value">8/12</span>
      <span class="stat-unit">辆</span>
    </div>
  </div>
  
  <div class="header-right">
    <div class="system-status" onclick="toggleStatusDrawer()">
      <span class="status-dot status-success"></span>
      <span class="status-text">系统正常</span>
    </div>
    <div class="current-time">10:32:45</div>
  </div>
</header>

<!-- 系统状态抽屉窗（默认隐藏） -->
<div class="status-drawer" id="statusDrawer">
  <div class="drawer-header">
    <span>系统状态详情</span>
    <button onclick="toggleStatusDrawer()">✕</button>
  </div>
  <div class="service-list">
    <div class="service-item">
      <span class="service-name">GPS服务</span>
      <span class="service-status status-success">● 正常</span>
    </div>
    <!-- 其他服务项... -->
  </div>
</div>
```

### 3.2 CSS 样式

```css
.header-left {
  display: flex;
  align-items: center;
  gap: 12px;
}

.logo {
  font-size: 24px;
}

.title {
  font-size: 18px;
  font-weight: 600;
  color: var(--text-primary);
}

.header-center {
  display: flex;
  align-items: center;
  gap: 24px;
}

.stat-item {
  display: flex;
  align-items: baseline;
  gap: 8px;
}

.stat-label {
  font-size: 14px;
  color: var(--text-secondary);
}

.stat-value {
  font-size: 28px;
  font-weight: 700;
  color: var(--text-primary);
  font-family: 'DIN Alternate', 'Roboto', monospace;
}

.stat-change {
  font-size: 12px;
  color: var(--status-success);
}

.stat-unit {
  font-size: 14px;
  color: var(--text-secondary);
}

.stat-divider {
  width: 1px;
  height: 32px;
  background: var(--border-color);
}

.header-right {
  display: flex;
  align-items: center;
  gap: 24px;
}

.system-status {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 8px 16px;
  background: rgba(255,255,255,0.05);
  border-radius: var(--border-radius-md);
  cursor: pointer;
  transition: background var(--transition-fast);
}

.system-status:hover {
  background: rgba(255,255,255,0.1);
}

.status-dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
}

.status-success { background: var(--status-success); box-shadow: 0 0 8px var(--status-success); }
.status-warning { background: var(--status-warning); }
.status-danger { background: var(--status-danger); box-shadow: 0 0 8px var(--status-danger); }

.current-time {
  font-size: 16px;
  font-weight: 500;
  font-family: 'DIN Alternate', monospace;
  color: var(--text-primary);
}

/* 状态抽屉窗 */
.status-drawer {
  position: fixed;
  top: 64px;
  right: -320px;
  width: 320px;
  height: calc(100vh - 64px);
  background: var(--bg-secondary);
  border-left: 1px solid var(--border-color);
  transition: right var(--transition-normal);
  z-index: 1000;
}

.status-drawer.open {
  right: 0;
}
```

### 3.3 交互逻辑

```javascript
// 切换状态抽屉窗
function toggleStatusDrawer() {
  const drawer = document.getElementById('statusDrawer');
  drawer.classList.toggle('open');
}

// 时间更新（每秒）
function updateTime() {
  const timeEl = document.querySelector('.current-time');
  const now = new Date();
  timeEl.textContent = now.toLocaleTimeString('zh-CN', { hour12: false });
}
setInterval(updateTime, 1000);
```

---

## 4. 信息区详细设计

### 4.1 Tab 切换组件

```html
<div class="tabs">
  <button class="tab active" data-tab="vehicles" onclick="switchTab('vehicles')">
    <span class="tab-icon">🚌</span>
    <span class="tab-text">车辆</span>
  </button>
  <button class="tab" data-tab="stations" onclick="switchTab('stations')">
    <span class="tab-icon">📍</span>
    <span class="tab-text">站点</span>
  </button>
</div>
```

```css
.tabs {
  display: flex;
  border-bottom: 1px solid var(--border-color);
}

.tab {
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  padding: 16px;
  background: transparent;
  border: none;
  color: var(--text-secondary);
  font-size: 14px;
  cursor: pointer;
  transition: all var(--transition-fast);
}

.tab:hover {
  background: var(--bg-hover);
  color: var(--text-primary);
}

.tab.active {
  background: var(--bg-active);
  color: var(--text-primary);
  border-bottom: 2px solid var(--status-info);
}
```

### 4.2 车辆列表

```html
<div class="list-container" id="vehicleList">
  <!-- 筛选栏 -->
  <div class="filter-bar">
    <button class="filter-btn active">全部</button>
    <button class="filter-btn">行驶中</button>
    <button class="filter-btn">静止</button>
    <button class="filter-btn">离线</button>
  </div>
  
  <!-- 列表项 -->
  <div class="vehicle-item" data-id="A01" onclick="selectVehicle('A01')">
    <div class="vehicle-main">
      <div class="vehicle-status-indicator status-empty"></div>
      <div class="vehicle-info">
        <div class="vehicle-header">
          <span class="vehicle-plate">A01</span>
          <span class="vehicle-rate">20%</span>
        </div>
        <div class="vehicle-location">距东沙山1.2km</div>
      </div>
    </div>
    <div class="vehicle-status-text">行驶中</div>
  </div>
  <!-- 更多车辆项... -->
</div>
```

```css
.filter-bar {
  display: flex;
  gap: 8px;
  padding: 12px 16px;
  border-bottom: 1px solid var(--border-color);
}

.filter-btn {
  padding: 6px 12px;
  background: transparent;
  border: 1px solid var(--border-color);
  border-radius: var(--border-radius-sm);
  color: var(--text-secondary);
  font-size: 12px;
  cursor: pointer;
  transition: all var(--transition-fast);
}

.filter-btn:hover, .filter-btn.active {
  background: rgba(59, 130, 246, 0.2);
  border-color: var(--status-info);
  color: var(--status-info);
}

.vehicle-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 16px;
  border-bottom: 1px solid var(--border-color);
  cursor: pointer;
  transition: background var(--transition-fast);
}

.vehicle-item:hover {
  background: var(--bg-hover);
}

.vehicle-item.selected {
  background: rgba(59, 130, 246, 0.15);
}

.vehicle-main {
  display: flex;
  align-items: center;
  gap: 12px;
}

.vehicle-status-indicator {
  width: 8px;
  height: 32px;
  border-radius: 4px;
}

.status-empty { background: var(--vehicle-empty); }
.status-half { background: var(--vehicle-half); }
.status-full { background: var(--vehicle-full); box-shadow: 0 0 8px var(--vehicle-full); }
.status-offline { background: var(--status-offline); }

.vehicle-info {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.vehicle-header {
  display: flex;
  align-items: center;
  gap: 12px;
}

.vehicle-plate {
  font-size: 16px;
  font-weight: 600;
  color: var(--text-primary);
}

.vehicle-rate {
  font-size: 14px;
  font-weight: 500;
}

.vehicle-location {
  font-size: 13px;
  color: var(--text-secondary);
}

.vehicle-status-text {
  font-size: 12px;
  color: var(--text-tertiary);
}
```

### 4.3 站点列表

```html
<div class="list-container" id="stationList" style="display: none;">
  <div class="station-item" data-id="dongsha" onclick="selectStation('dongsha')">
    <div class="station-header">
      <span class="station-dot status-danger"></span>
      <span class="station-name">东沙山</span>
    </div>
    <div class="station-info">
      <div class="station-queue">
        <span class="queue-number danger">48人</span>
        <span class="queue-wait">预计15分钟</span>
      </div>
      <div class="station-incoming">+12人即将到达</div>
    </div>
  </div>
  <!-- 更多站点项... -->
</div>
```

```css
.station-item {
  padding: 16px;
  border-bottom: 1px solid var(--border-color);
  cursor: pointer;
  transition: background var(--transition-fast);
}

.station-item:hover {
  background: var(--bg-hover);
}

.station-item.selected {
  background: rgba(59, 130, 246, 0.15);
}

.station-header {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-bottom: 8px;
}

.station-dot {
  width: 10px;
  height: 10px;
  border-radius: 50%;
}

.station-dot.status-success { background: var(--status-success); }
.station-dot.status-warning { 
  background: var(--status-warning); 
  animation: blink-slow 2s infinite;
}
.station-dot.status-danger { 
  background: var(--status-danger); 
  animation: blink-fast 1s infinite;
  position: relative;
}

.station-dot.status-danger::after {
  content: '';
  position: absolute;
  top: 50%;
  left: 50%;
  width: 100%;
  height: 100%;
  border-radius: 50%;
  border: 2px solid var(--status-danger);
  animation: ripple 1.5s infinite;
}

.station-name {
  font-size: 16px;
  font-weight: 500;
  color: var(--text-primary);
}

.station-info {
  padding-left: 18px;
}

.station-queue {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 4px;
}

.queue-number {
  font-size: 18px;
  font-weight: 700;
}

.queue-number.danger {
  color: var(--status-danger);
}

.queue-wait {
  font-size: 13px;
  color: var(--text-tertiary);
}

.station-incoming {
  font-size: 12px;
  color: var(--text-tertiary);
}
```

### 4.4 交互逻辑

```javascript
// Tab 切换
function switchTab(tabName) {
  document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
  document.querySelector(`[data-tab="${tabName}"]`).classList.add('active');
  
  if (tabName === 'vehicles') {
    document.getElementById('vehicleList').style.display = 'block';
    document.getElementById('stationList').style.display = 'none';
  } else {
    document.getElementById('vehicleList').style.display = 'none';
    document.getElementById('stationList').style.display = 'block';
  }
}

// 选中车辆
function selectVehicle(vehicleId) {
  document.querySelectorAll('.vehicle-item').forEach(i => i.classList.remove('selected'));
  document.querySelector(`[data-id="${vehicleId}"]`).classList.add('selected');
  
  // 触发地图联动
  highlightVehicleOnMap(vehicleId);
}

// 选中站点
function selectStation(stationId) {
  document.querySelectorAll('.station-item').forEach(i => i.classList.remove('selected'));
  document.querySelector(`[data-id="${stationId}"]`).classList.add('selected');
  
  // 触发地图联动
  highlightStationOnMap(stationId);
}
```

---

## 5. 地图区详细设计

### 5.1 高德地图接入

```html
<div class="map-container">
  <div id="amap" style="width: 100%; height: 100%;"></div>
  
  <!-- 地图浮窗（动态生成） -->
  <div class="map-popup" id="mapPopup" style="display: none;">
    <!-- 浮窗内容 -->
  </div>
</div>
```

```css
.map-container {
  position: relative;
  flex: 1;
  background: #0f1419;
}

#amap {
  width: 100%;
  height: 100%;
}

/* 高德地图自定义样式覆盖 */
.amap-logo, .amap-copyright {
  display: none !important;
}
```

### 5.2 地图初始化

```javascript
// 高德地图初始化
let map;
let currentMarkers = [];

function initMap() {
  map = new AMap.Map('amap', {
    zoom: 14,
    center: [86.654321, 40.123456], // 景区中心坐标
    viewMode: '2D',
    mapStyle: 'amap://styles/dark', // 深色主题
    features: ['bg', 'road', 'building'],
    showBuildingBlock: true
  });
  
  // 添加站点标记
  addStationMarkers();
  
  // 添加车辆标记
  addVehicleMarkers();
  
  // 点击空白处关闭浮窗
  map.on('click', () => {
    closeMapPopup();
    clearListSelection();
  });
}
```

### 5.3 车辆标记

```javascript
// 车辆标记样式（使用自定义DOM）
function createVehicleMarker(vehicle) {
  const div = document.createElement('div');
  div.className = `vehicle-marker ${vehicle.status}`;
  div.innerHTML = `
    <div class="marker-icon">
      <svg width="32" height="32" viewBox="0 0 32 32">
        <!-- 巴士图标 -->
        <rect x="8" y="4" width="16" height="24" rx="3" fill="currentColor"/>
        <rect x="10" y="6" width="12" height="8" fill="rgba(255,255,255,0.3)"/>
        <circle cx="11" cy="22" r="2" fill="#333"/>
        <circle cx="21" cy="22" r="2" fill="#333"/>
      </svg>
    </div>
    <div class="marker-label">${vehicle.id}</div>
  `;
  
  const marker = new AMap.Marker({
    position: [vehicle.lng, vehicle.lat],
    content: div,
    offset: new AMap.Pixel(-16, -32),
    anchor: 'center'
  });
  
  marker.on('click', () => {
    showVehiclePopup(vehicle);
    selectVehicleInList(vehicle.id);
  });
  
  return marker;
}
```

```css
/* 车辆标记样式 */
.vehicle-marker {
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.marker-icon {
  width: 32px;
  height: 32px;
  color: var(--vehicle-empty);
  transition: all var(--transition-fast);
}

.vehicle-marker.half .marker-icon { color: var(--vehicle-half); }
.vehicle-marker.full .marker-icon { 
  color: var(--vehicle-full); 
  filter: drop-shadow(0 0 4px var(--vehicle-full));
}
.vehicle-marker.offline .marker-icon { 
  color: var(--status-offline);
  opacity: 0.5;
}

.marker-label {
  margin-top: 2px;
  padding: 2px 6px;
  background: rgba(255,255,255,0.9);
  border-radius: var(--border-radius-sm);
  font-size: 10px;
  font-weight: 600;
  color: #333;
  white-space: nowrap;
}

.vehicle-marker.selected .marker-icon {
  transform: scale(1.5);
  filter: drop-shadow(0 0 8px #fbbf24);
}

.vehicle-marker.selected::after {
  content: '';
  position: absolute;
  top: 50%;
  left: 50%;
  width: 48px;
  height: 48px;
  border: 2px solid #fbbf24;
  border-radius: 50%;
  transform: translate(-50%, -50%);
  animation: pulse 1.5s infinite;
}
```

### 5.4 站点标记

```javascript
// 站点标记
function createStationMarker(station) {
  const div = document.createElement('div');
  div.className = `station-marker ${station.status}`;
  div.innerHTML = `
    <div class="station-badge">${station.queue}</div>
    <div class="station-dot"></div>
    <div class="station-name">${station.name}</div>
  `;
  
  const marker = new AMap.Marker({
    position: [station.lng, station.lat],
    content: div,
    offset: new AMap.Pixel(0, -20),
    anchor: 'bottom-center'
  });
  
  marker.on('click', () => {
    showStationPopup(station);
    selectStationInList(station.id);
  });
  
  return marker;
}
```

```css
.station-marker {
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.station-badge {
  position: absolute;
  top: -24px;
  padding: 4px 8px;
  background: rgba(0,0,0,0.8);
  border-radius: var(--border-radius-md);
  font-size: 14px;
  font-weight: 700;
  color: white;
  white-space: nowrap;
}

.station-dot {
  width: 16px;
  height: 16px;
  border-radius: 50%;
  background: var(--status-success);
  border: 3px solid rgba(255,255,255,0.3);
}

.station-marker.warning .station-dot {
  background: var(--status-warning);
  animation: blink-slow 2s infinite;
}

.station-marker.danger .station-dot {
  background: var(--status-danger);
  animation: blink-fast 1s infinite;
}

.station-marker.danger .station-dot::after {
  content: '';
  position: absolute;
  top: 50%;
  left: 50%;
  width: 100%;
  height: 100%;
  border-radius: 50%;
  border: 2px solid var(--status-danger);
  animation: ripple 1.5s infinite;
}

.station-name {
  margin-top: 4px;
  padding: 2px 8px;
  background: rgba(0,0,0,0.7);
  border-radius: var(--border-radius-sm);
  font-size: 12px;
  color: white;
  white-space: nowrap;
  text-shadow: 0 1px 2px rgba(0,0,0,0.5);
}
```

### 5.5 地图浮窗

```javascript
// 显示车辆浮窗
function showVehiclePopup(vehicle) {
  const popup = document.getElementById('mapPopup');
  popup.innerHTML = `
    <div class="popup-header">
      <span>🚌 车辆 ${vehicle.id}</span>
      <button onclick="closeMapPopup()">✕</button>
    </div>
    <div class="popup-body">
      <div class="popup-row">
        <span class="popup-label">状态</span>
        <span class="popup-value ${vehicle.statusClass}">${vehicle.statusText}</span>
      </div>
      <div class="popup-row">
        <span class="popup-label">当前位置</span>
        <span class="popup-value">距离${vehicle.nearestStation}${vehicle.distance}km</span>
      </div>
      <div class="popup-row">
        <span class="popup-label">载客</span>
        <span class="popup-value">${vehicle.passengers}/${vehicle.capacity}人 (${vehicle.rate}%)</span>
      </div>
    </div>
  `;
  popup.style.display = 'block';
  
  // 定位到标记位置
  const pixel = map.lngLatToContainer([vehicle.lng, vehicle.lat]);
  popup.style.left = `${pixel.x + 20}px`;
  popup.style.top = `${pixel.y - 50}px`;
}
```

```css
.map-popup {
  position: absolute;
  min-width: 240px;
  background: rgba(17, 24, 39, 0.95);
  border: 1px solid var(--border-color);
  border-radius: var(--border-radius-lg);
  padding: 16px;
  z-index: 100;
  backdrop-filter: blur(8px);
}

.popup-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 12px;
  padding-bottom: 12px;
  border-bottom: 1px solid var(--border-color);
  font-weight: 600;
}

.popup-header button {
  background: none;
  border: none;
  color: var(--text-secondary);
  cursor: pointer;
  font-size: 16px;
}

.popup-row {
  display: flex;
  justify-content: space-between;
  padding: 8px 0;
}

.popup-label {
  color: var(--text-secondary);
  font-size: 13px;
}

.popup-value {
  color: var(--text-primary);
  font-size: 13px;
  font-weight: 500;
}

.popup-value.status-empty { color: var(--vehicle-empty); }
.popup-value.status-half { color: var(--vehicle-half); }
.popup-value.status-full { color: var(--vehicle-full); }
```

---

## 6. 消息推送区详细设计

### 6.1 HTML 结构

```html
<aside class="sidebar-right">
  <div class="message-header">
    <span class="header-title">🔔 消息中心</span>
    <span class="header-badge">3</span>
  </div>
  <div class="message-list" id="messageList">
    <!-- 消息卡片动态生成 -->
  </div>
</aside>
```

### 6.2 消息卡片

```html
<!-- 预防性预警卡片 -->
<div class="message-card warning" data-type="prevent" data-station="dongsha">
  <div class="card-header">
    <span class="card-icon">⚠️</span>
    <span class="card-title">预防性预警</span>
  </div>
  <div class="card-body">
    <p class="card-content">东沙山即将涌入50人</p>
    <p class="card-detail">当前可用车辆：1辆</p>
    <p class="card-suggest">建议增派车辆</p>
  </div>
  <div class="card-footer">
    <button class="card-btn" onclick="locateStation('dongsha')">查看</button>
  </div>
</div>

<!-- 补救性预警卡片 -->
<div class="message-card danger" data-type="remedy" data-station="abudan">
  <div class="card-header">
    <span class="card-icon">🔥</span>
    <span class="card-title">排队超限预警</span>
  </div>
  <div class="card-body">
    <p class="card-content">阿不旦排队48人</p>
    <p class="card-detail">已超过阈值(40人)</p>
    <p class="card-suggest">建议立即派车</p>
  </div>
  <div class="card-footer">
    <button class="card-btn" onclick="locateStation('abudan')">查看</button>
  </div>
</div>
```

```css
.message-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 16px;
  border-bottom: 1px solid var(--border-color);
}

.header-title {
  font-size: 14px;
  font-weight: 600;
}

.header-badge {
  padding: 2px 8px;
  background: var(--status-danger);
  border-radius: 10px;
  font-size: 12px;
  font-weight: 600;
}

.message-list {
  flex: 1;
  overflow-y: auto;
  padding: 12px;
}

.message-card {
  margin-bottom: 12px;
  padding: 12px;
  background: var(--bg-tertiary);
  border-radius: var(--border-radius-md);
  border-left: 3px solid transparent;
  cursor: pointer;
  transition: all var(--transition-fast);
}

.message-card:hover {
  background: var(--bg-hover);
}

.message-card.warning {
  border-left-color: var(--status-info);
}

.message-card.danger {
  border-left-color: var(--status-danger);
  animation: pulse 2s infinite;
}

.card-header {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-bottom: 8px;
}

.card-icon {
  font-size: 16px;
}

.card-title {
  font-size: 13px;
  font-weight: 600;
}

.card-body {
  margin-bottom: 12px;
}

.card-content {
  font-size: 14px;
  font-weight: 500;
  color: var(--text-primary);
  margin-bottom: 4px;
}

.card-detail, .card-suggest {
  font-size: 12px;
  color: var(--text-secondary);
}

.card-footer {
  display: flex;
  justify-content: flex-end;
}

.card-btn {
  padding: 6px 16px;
  background: rgba(59, 130, 246, 0.2);
  border: 1px solid var(--status-info);
  border-radius: var(--border-radius-sm);
  color: var(--status-info);
  font-size: 12px;
  cursor: pointer;
  transition: all var(--transition-fast);
}

.card-btn:hover {
  background: var(--status-info);
  color: white;
}
```

### 6.3 交互逻辑

```javascript
// 定位到站点
function locateStation(stationId) {
  highlightStationOnMap(stationId);
  selectStationInList(stationId);
  switchTab('stations');
}

// 添加消息（模拟）
function addMessage(message) {
  const list = document.getElementById('messageList');
  const card = createMessageCard(message);
  list.insertBefore(card, list.firstChild);
  
  // 播放提示音
  playNotificationSound();
  
  // 更新角标
  updateBadge();
}

// 1小时后自动移除预警消息
function autoRemoveWarning(messageId) {
  setTimeout(() => {
    const card = document.querySelector(`[data-id="${messageId}"]`);
    if (card) {
      card.style.opacity = '0';
      setTimeout(() => card.remove(), 300);
    }
  }, 3600000); // 1小时
}
```

---

## 7. 模拟数据

```javascript
// 模拟车辆数据
const mockVehicles = [
  { id: 'A01', status: 'empty', statusText: '空载行驶中', statusClass: 'status-empty', passengers: 4, capacity: 20, rate: 20, nearestStation: '东沙山', distance: 1.2, lat: 40.123, lng: 86.654, moving: true },
  { id: 'A02', status: 'half', statusText: '半载静止', statusClass: 'status-half', passengers: 12, capacity: 20, rate: 60, nearestStation: '阿不旦', distance: 0.8, lat: 40.125, lng: 86.652, moving: false },
  { id: 'A03', status: 'full', statusText: '满载行驶中', statusClass: 'status-full', passengers: 19, capacity: 20, rate: 95, nearestStation: '游客中心', distance: 2.1, lat: 40.121, lng: 86.658, moving: true },
  { id: 'A04', status: 'offline', statusText: '离线', statusClass: 'status-offline', passengers: 0, capacity: 20, rate: 0, nearestStation: '未知', distance: 0, lat: 40.120, lng: 86.650, moving: false }
];

// 模拟站点数据
const mockStations = [
  { id: 'visitor', name: '游客中心', status: 'success', queue: 12, incoming: 0, waitTime: 3, lat: 40.120, lng: 86.660 },
  { id: 'abudan', name: '阿不旦广场', status: 'warning', queue: 35, incoming: 8, waitTime: 8, lat: 40.125, lng: 86.655 },
  { id: 'dongsha', name: '东沙山', status: 'danger', queue: 48, incoming: 12, waitTime: 15, lat: 40.130, lng: 86.650 },
  { id: 'shennv', name: '神女湖', status: 'success', queue: 8, incoming: 2, waitTime: 2, lat: 40.135, lng: 86.645, closed: false }
];

// 模拟消息
const mockMessages = [
  { type: 'prevent', station: 'dongsha', title: '预防性预警', content: '东沙山即将涌入50人', detail: '当前可用车辆：1辆', suggest: '建议增派车辆' },
  { type: 'remedy', station: 'abudan', title: '排队超限预警', content: '阿不旦排队48人', detail: '已超过阈值(40人)', suggest: '建议立即派车' }
];
```

---

## 8. AI Coding Prompt 模板

使用以下 Prompt 让 AI 生成完整原型：

```
你是一个前端开发专家，请根据以下详细设计文档生成一个完整的可交互 HTML 原型。

【项目】罗布人村寨 AI 接驳车调度系统（管理大屏）

【技术栈】
- HTML5 + CSS3 + JavaScript
- 高德地图 JS API（使用模拟数据，不接入真实地图）
- 深色主题大屏风格

【设计系统】
- 背景：#0a0e1a（主）、#111827（侧栏）
- 文字：白色主色、70%透明度次要色
- 状态色：绿(#10b981)、蓝(#3b82f6)、橙(#f97316)、红(#ef4444)
- 字体：系统默认 + monospace用于数字

【布局】
- 顶部栏：64px高，显示系统标题、今日接待、在线车辆、系统状态、当前时间
- 左侧信息区：25%宽度，Tab切换（车辆/站点）
- 中间地图区：60%宽度，高德地图+车辆图标+站点标记
- 右侧消息区：15%宽度，消息卡片列表

【必须实现的功能】
1. Tab切换：车辆列表/站点列表切换
2. 车辆列表：显示车牌、状态、满载率、位置，点击联动地图
3. 站点列表：显示名称、状态灯、排队人数，点击联动地图
4. 地图：使用高德地图（申请临时key或使用mock），显示车辆标记（不同颜色）和站点标记（带人数）
5. 消息卡片：预警消息，点击查看定位到站点
6. 系统状态抽屉：点击顶部状态展开

【交互要求】
- 所有点击有反馈
- 选中状态有高亮
- 危险站点有闪烁动画
- 消息卡片有过渡动画

【模拟数据】
车辆：A01(空载行驶)、A02(半载静止)、A03(满载行驶)、A04(离线)
站点：游客中心(正常12人)、阿不旦(拥挤35人)、东沙山(危险48人)、神女湖(正常8人)

请生成完整的单个HTML文件，包含所有CSS和JS，可以直接在浏览器打开运行。
```

---

## 9. 检查清单

生成原型后检查：

- [ ] 页面布局正确（顶部栏+三栏）
- [ ] Tab切换正常工作
- [ ] 车辆/站点列表显示正确
- [ ] 点击列表项地图有反馈
- [ ] 地图能正常显示（或用占位图）
- [ ] 车辆标记有颜色区分
- [ ] 站点标记有状态动画
- [ ] 消息卡片显示正常
- [ ] 深色主题一致
- [ ] 响应式正常（最小1280x720）
