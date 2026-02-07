# Cesium 卫星锥体投影可视化 - 产品需求文档 (PRD)

## 1. 项目概述

### 1.1 项目背景
开发一个基于 Cesium.js 的三维地球可视化应用，展示卫星在轨道上运行，并在地面形成投影覆盖区域（锥体形状）。

### 1.2 核心功能
- 卫星在 3D 地球上空沿轨道运行
- 地面显示卫星投影覆盖区域（椭圆形状）
- 卫星与投影边缘连线形成锥体可视化
- 实时参数调整面板
- 多底图切换

### 1.3 技术栈
- **框架**: CesiumJS 1.133
- **语言**: JavaScript (ES6+)
- **样式**: CSS3
- **地形**: Cesium World Terrain（可选）

---

## 2. 功能需求

### 2.1 核心可视化元素

#### A. 卫星实体
| 属性 | 规格 |
|------|------|
| 表示方式 | 点标记 + 标签 |
| 大小 | 20像素 |
| 颜色 | 黄色填充 + 红色边框 |
| 标签 | "🛰️ 卫星" |
| 运动 | 沿椭圆轨道实时运动 |

#### B. 轨道线
| 属性 | 规格 |
|------|------|
| 类型 | 闭合椭圆轨道 |
| 颜色 | 青色 (cyan) |
| 透明度 | 80% |
| 线宽 | 2像素 |

#### C. 地面投影（核心）
| 属性 | 规格 |
|------|------|
| 形状 | 椭圆 |
| 半长轴 | 可配置（默认 50km） |
| 半短轴 | 可配置（默认 25km） |
| 采样点数 | 60个点组成椭圆 |
| **填充面** | 绿色半透明 |
| **边框** | 绿色实线，贴地显示 |

#### D. 锥体侧面
| 属性 | 规格 |
|------|------|
| 构成 | 卫星顶点 + 地面椭圆边缘 |
| 形状 | 三角形网格近似锥体 |
| 数量 | 20个三角形（每3个点一个） |
| 颜色 | 绿色半透明（比填充更透明） |

### 2.2 轨道参数规格

```yaml
轨道高度: 800 km
地球半径: 6371 km
轨道半径: 7171 km
轨道倾角: 51.6°
运行周期: 90 分钟
时间倍速: 50x
初始位置: 中国四川上空 (104°E, 30°N)
```

### 2.3 配置面板功能

#### 参数调节范围
| 参数 | 范围 | 默认值 | 步进 |
|------|------|--------|------|
| 半长轴 | 10-200 km | 50 km | 1 km |
| 半短轴 | 5-100 km | 25 km | 1 km |
| 填充透明度 | 0-100% | 60% | 1% |
| 边框宽度 | 1-10 px | 3 px | 1 px |
| 边框透明度 | 0-100% | 100% | 1% |

#### 开关控制
- [x] 显示填充面
- [x] 显示边框
- [x] 显示锥体侧面
- [x] 显示轨道线

#### 颜色选择器
- 填充颜色（默认绿色 #00ff00）
- 边框颜色（默认绿色 #00ff00）

### 2.4 底图支持

#### 默认底图
- **Bing Maps 卫星影像**（通过 Cesium Ion）

#### 可选底图
- OpenStreetMap
- CartoDB 深色 (Dark Matter)
- CartoDB 浅色
- ESRI 卫星

---

## 3. 界面布局

### 3.1 整体布局
```
┌─────────────────────────────────────┐
│  [配置面板]      │  Cesium 3D 地球   │
│  - 参数滑块      │                   │
│  - 颜色选择      │   🛰️ 卫星        │
│  - 开关选项      │      ↓            │
│                  │   🔵 投影椭圆     │
├──────────────────┤      ↘           │
│  [信息面板]      │   ╱ 锥体面 ╲     │
│  - 状态          │                   │
│  - 位置信息      │                   │
└──────────────────┴───────────────────┘
          [控制按钮] [底图选择]
```

### 3.2 控件位置
| 控件 | 位置 | 样式 |
|------|------|------|
| 配置面板 | 左上角 | 半透明黑色背景，圆角 |
| 信息面板 | 左侧中部 | 半透明黑色背景 |
| 控制按钮 | 底部居中 | 蓝色按钮 |
| 底图切换 | 自带 baseLayerPicker | 右上角 |

---

## 4. 交互逻辑

### 4.1 播放控制
```
初始状态: 暂停（卫星静止）
点击 "▶️ 播放": 
  - 开始动画
  - 按钮变为 "⏸️ 暂停"
  - 状态显示 "播放中"

点击 "⏸️ 暂停":
  - 停止动画
  - 按钮变为 "▶️ 播放"
  - 状态显示 "已暂停"
```

### 4.2 视角控制
```
点击 "🌍 星下点视角":
  - 相机移动到卫星正上方
  - 高度 150km
  - 正俯视角度

点击 "🛰️ 跟随卫星":
  - 相机锁定卫星
  - 视角跟随卫星移动
  - 再次点击取消跟随
```

### 4.3 参数实时更新
```
用户拖动滑块 → 立即更新数值显示 → 立即重新生成投影
用户改变颜色 → 立即更新颜色显示 → 立即重新生成投影
用户点击开关 → 立即显示/隐藏对应元素
```

---

## 5. 技术实现规格

### 5.1 使用 Cesium Entity API
```javascript
// 卫星实体示例
viewer.entities.add({
    position: sampledPositionProperty,  // 时间动态位置
    point: { pixelSize: 20, color: Cesium.Color.YELLOW },
    label: { text: '🛰️ 卫星', ... }
});

// 地面投影示例
viewer.entities.add({
    polygon: {
        hierarchy: new Cesium.PolygonHierarchy(groundPoints),
        material: new Cesium.Color(0, 1, 0, 0.6),
        classificationType: Cesium.ClassificationType.TERRAIN  // 贴地形
    }
});

// 边框示例
viewer.entities.add({
    polyline: {
        positions: borderPositions,
        width: 3,
        material: new Cesium.Color(0, 1, 0, 1.0),
        clampToGround: true  // 贴地
    }
});
```

### 5.2 位置计算算法

#### 卫星轨道计算
```javascript
function getSatellitePosition(time) {
    const elapsedSeconds = Cesium.JulianDate.secondsDifference(time, startTime);
    const angle = (elapsedSeconds / ORBIT_PERIOD) * 2 * Math.PI;
    
    // 轨道平面坐标
    const x = ORBIT_RADIUS * Math.cos(angle);
    const y = ORBIT_RADIUS * Math.sin(angle);
    
    // 应用倾角旋转
    const cosInc = Math.cos(INCLINATION);
    const sinInc = Math.sin(INCLINATION);
    
    return new Cesium.Cartesian3(x, y * cosInc, y * sinInc);
}
```

#### 地面椭圆点计算
```javascript
function computeGroundPoints(satPos, semiMajor, semiMinor) {
    const satCarto = Cesium.Cartographic.fromCartesian(satPos);
    const centerLon = Cesium.Math.toDegrees(satCarto.longitude);
    const centerLat = Cesium.Math.toDegrees(satCarto.latitude);
    const cosLat = Math.cos(Cesium.Math.toRadians(centerLat));
    
    const points = [];
    for (let i = 0; i < 60; i++) {
        const theta = (i / 60) * 2 * Math.PI;
        // 椭圆参数方程
        const dx = semiMajor * Math.cos(theta);
        const dy = semiMinor * Math.sin(theta);
        // 转换为经纬度偏移
        const deltaLon = Cesium.Math.toDegrees(dx / (EARTH_RADIUS * cosLat));
        const deltaLat = Cesium.Math.toDegrees(dy / EARTH_RADIUS);
        points.push(Cesium.Cartesian3.fromDegrees(
            centerLon + deltaLon, 
            centerLat + deltaLat, 
            0
        ));
    }
    return points;
}
```

### 5.3 实时更新机制
```javascript
// 播放时更新
viewer.scene.preUpdate.addEventListener(function() {
    if (viewer.clock.shouldAnimate) {
        createOrUpdateProjection();
    }
});

// 参数变化时更新
function updateConfig() {
    // 读取配置...
    createOrUpdateProjection();  // 删除旧实体，创建新实体
}
```

---

## 6. 性能要求

### 6.1 帧率要求
- 目标帧率：30 FPS
- 最低可接受：15 FPS

### 6.2 优化策略
1. **减少三角形数量**：20个而非60个
2. **预采样轨道**：200个点预计算
3. **条件更新**：仅播放时更新
4. **实体复用**：可考虑使用 CallbackProperty 避免删除重建

### 6.3 内存管理
- 及时移除旧实体：`viewer.entities.remove(entity)`
- 限制实体总数：< 100个

---

## 7. 代码结构示例

```javascript
// 配置对象
const config = {
    semiMajor: 50000,      // 米
    semiMinor: 25000,      // 米
    fillColor: {r:0, g:1, b:0},
    fillOpacity: 0.6,
    borderColor: {r:0, g:1, b:0},
    borderWidth: 3,
    borderOpacity: 1.0,
    showFill: true,
    showBorder: true,
    showCone: true,
    showOrbit: true
};

// 核心函数
function createOrUpdateProjection() {
    // 1. 获取卫星当前位置
    // 2. 计算地面60个点
    // 3. 移除旧实体
    // 4. 创建新实体（填充面、边框、锥体面）
}

// 事件绑定
document.querySelectorAll('input').forEach(input => {
    input.addEventListener('input', updateConfig);
});
```

---

## 8. 验收标准

### 8.1 功能验收
- [ ] 卫星沿轨道正常运动
- [ ] 地面投影跟随卫星移动
- [ ] 锥体面正确显示（卫星到地面连线）
- [ ] 配置面板所有参数可调
- [ ] 参数调整实时生效
- [ ] 底图可切换

### 8.2 性能验收
- [ ] 动画流畅无卡顿
- [ ] 参数调整响应时间 < 100ms
- [ ] 内存占用稳定

### 8.3 兼容性
- [ ] Chrome 最新版
- [ ] Firefox 最新版
- [ ] Safari 最新版

---

## 9. 示例截图描述

### 正常视角
> 从卫星斜上方俯视，可见黄色卫星点、绿色地面椭圆投影、半透明锥体面连接卫星和地面。

### 星下点视角
> 正俯视角度，清晰显示地面椭圆形状，可见底图地形纹理。

### 配置面板
> 左侧显示参数滑块、颜色选择器、开关按钮，实时调整投影大小和颜色。

---

## 10. 附录

### 10.1 坐标参考
- 地心坐标系 (ECEF)
- WGS84 椭球
- 经纬度转 Cartesian3: `Cesium.Cartesian3.fromDegrees(lon, lat, height)`

### 10.2 颜色参考
- 卫星黄色: `Cesium.Color.YELLOW`
- 轨道青色: `new Cesium.Color(0, 1, 1, 0.8)`
- 投影绿色: `new Cesium.Color(0, 1, 0, 0.6)`

### 10.3 数学公式
```
椭圆参数方程:
x = a * cos(θ)
y = b * sin(θ)

经纬度偏移:
Δlon = (dx / (R * cos(lat))) * (180/π)
Δlat = (dy / R) * (180/π)
```

---

**文档版本**: 1.0
**最后更新**: 2024
**作者**: AI Assistant
