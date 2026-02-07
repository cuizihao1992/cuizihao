# Cesium 卫星锥体投影 - 实现文档

## 1. 技术选型

### 1.1 使用 Entity API（而非 Primitive）
- **原因**：Entity API 更高级、更易用，适合快速开发和频繁更新的场景
- **实现方式**：
  ```javascript
  // 卫星实体
  viewer.entities.add({
      position: satellitePositionProperty,
      point: { pixelSize: 20, color: Cesium.Color.YELLOW }
  });
  
  // 地面填充面
  viewer.entities.add({
      polygon: {
          hierarchy: new Cesium.PolygonHierarchy(groundPoints),
          material: new Cesium.Color(0, 1, 0, 0.6)
      }
  });
  
  // 边框线
  viewer.entities.add({
      polyline: {
          positions: borderPositions,
          width: 3,
          material: new Cesium.Color(0, 1, 0, 1.0)
      }
  });
  ```

### 1.2 不使用 Primitive 的原因
- Primitive API 性能更高但代码复杂
- 需要手动管理几何体缓存和更新
- 本场景实体数量少（<100个），Entity API 性能足够

---

## 2. 卫星轨道参数

| 参数 | 数值 | 说明 |
|------|------|------|
| 轨道高度 | 800 km | 近地轨道 |
| 地球半径 | 6,371 km | WGS84标准 |
| 轨道半径 | 7,171 km | 地球半径 + 高度 |
| 轨道倾角 | 51.6° | 类似国际空间站 |
| 运行周期 | 90 分钟 | 一圈 |
| 时间倍速 | 50x | 动画加速 |

### 轨道计算公式
```javascript
const angle = (elapsedSeconds / ORBIT_PERIOD) * 2 * Math.PI;
const x = ORBIT_RADIUS * Math.cos(angle);
const y = ORBIT_RADIUS * Math.sin(angle) * Math.cos(INCLINATION);
const z = ORBIT_RADIUS * Math.sin(angle) * Math.sin(INCLINATION);
```

---

## 3. 初始位置

- **地理位置**：中国四川盆地
- **经度**：104.0° E
- **纬度**：30.0° N
- **选择原因**：周围有青藏高原、横断山脉，地形起伏明显，便于观察投影效果

### 初始视角设置
```javascript
viewer.camera.setView({
    destination: Cesium.Cartesian3.fromDegrees(104.0, 30.0, 150000), // 高度150km
    orientation: { heading: 0, pitch: Cesium.Math.toRadians(-90), roll: 0 } // 正俯视
});
```

---

## 4. 参数实时更新机制

### 4.1 配置面板事件绑定
```javascript
// 监听所有输入控件的变化
document.querySelectorAll('#configPanel input').forEach(input => {
    input.addEventListener('input', updateConfig);
    input.addEventListener('change', updateConfig);
});
```

### 4.2 配置更新函数
```javascript
function updateConfig() {
    // 读取控件值
    config.semiMajor = parseInt(document.getElementById('semiMajor').value) * 1000;
    config.fillColor = hexToRgb(document.getElementById('fillColor').value);
    config.showFill = document.getElementById('showFill').checked;
    // ... 其他参数
    
    // 重新创建投影
    createOrUpdateProjection();
}
```

### 4.3 关键设计
- **即时反馈**：使用 `input` 事件，拖动滑块时实时更新
- **防抖优化**：`change` 事件确保最终值被应用

---

## 5. 锥体投影实时更新

### 5.1 更新触发机制
```javascript
viewer.scene.preUpdate.addEventListener(function() {
    if (viewer.clock.shouldAnimate) {
        createOrUpdateProjection();
    }
});
```

### 5.2 地面椭圆点计算（60个点）
```javascript
function computeGroundPoints(satPos, semiMajor, semiMinor) {
    const points = [];
    for (let i = 0; i < 60; i++) {
        const theta = (i / 60) * 2 * Math.PI;
        // 椭圆参数方程
        const dx = semiMajor * Math.cos(theta);
        const dy = semiMinor * Math.sin(theta);
        // 转换为经纬度偏移
        const deltaLon = Cesium.Math.toDegrees(dx / (EARTH_RADIUS * cosLat));
        const deltaLat = Cesium.Math.toDegrees(dy / EARTH_RADIUS);
        points.push(Cesium.Cartesian3.fromDegrees(centerLon + deltaLon, centerLat + deltaLat, 0));
    }
    return points;
}
```

### 5.3 锥体侧面生成（三角形近似）
```javascript
// 每3个点取一个，生成20个三角形
for (let i = 0; i < 60; i += 3) {
    const next = (i + 3) % 60;
    viewer.entities.add({
        polygon: {
            hierarchy: new Cesium.PolygonHierarchy([
                satPos,           // 卫星位置（顶点）
                groundPoints[i],  // 地面点1
                groundPoints[next] // 地面点2
            ]),
            material: new Cesium.Color(r, g, b, opacity * 0.5)
        }
    });
}
```

---

## 6. 性能优化策略

### 6.1 当前优化
1. **减少三角形数量**
   - 原始：60个三角形（每个点一个）
   - 优化：20个三角形（每3个点一个）
   - 节省：66%的几何体数量

2. **预采样轨道**
   ```javascript
   // 预计算200个轨道点，避免实时计算
   for (let i = 0; i <= 200; i++) {
       satellitePositionProperty.addSample(time, position);
   }
   ```

3. **条件更新**
   - 仅在播放时更新（`viewer.clock.shouldAnimate`）
   - 暂停时不浪费资源

### 6.2 进一步优化建议

#### A. 使用 Primitive API（性能优先）
```javascript
// 替换 Entity，使用 Primitive
const primitive = new Cesium.Primitive({
    geometryInstances: new Cesium.GeometryInstance({
        geometry: new Cesium.PolygonGeometry({
            polygonHierarchy: new Cesium.PolygonHierarchy(positions)
        })
    }),
    appearance: new Cesium.MaterialAppearance({
        material: Cesium.Material.fromType('Color')
    })
});
viewer.scene.primitives.add(primitive);
```
**优势**：批量渲染，GPU 效率更高

#### B. 使用 CallbackProperty（避免重建）
```javascript
// 当前：删除旧实体，创建新实体（开销大）
viewer.entities.remove(groundFillEntity);
groundFillEntity = viewer.entities.add({...});

// 优化：使用 CallbackProperty 动态更新位置
viewer.entities.add({
    polygon: {
        hierarchy: new Cesium.CallbackProperty(function(time) {
            return new Cesium.PolygonHierarchy(computeGroundPoints(time));
        }, false)
    }
});
```
**优势**：实体只创建一次，位置动态计算

#### C. 降低更新频率
```javascript
// 当前：每帧更新（60fps）
viewer.scene.preUpdate.addEventListener(...);

// 优化：每5帧更新一次
let frameCount = 0;
viewer.scene.preUpdate.addEventListener(function() {
    if (frameCount++ % 5 === 0) {
        createOrUpdateProjection();
    }
});
```
**优势**：减少 80% 的更新开销

#### D. 使用 GroundPrimitive（贴地优化）
```javascript
// 当前：普通 Polygon
viewer.entities.add({ polygon: {...} });

// 优化：使用 GroundPrimitive 专门贴地
viewer.scene.groundPrimitives.add(new Cesium.GroundPrimitive({
    geometryInstances: new Cesium.GeometryInstance({
        geometry: new Cesium.PolygonGeometry({...})
    }),
    classificationType: Cesium.ClassificationType.TERRAIN
}));
```
**优势**：专门优化贴地渲染，支持地形遮挡

---

## 7. 架构图

```
Viewer
├── Satellite Entity (SampledPositionProperty)
├── Orbit Line Entity (静态)
└── Projection Entities (动态更新)
    ├── Ground Fill Polygon (60点)
    ├── Ground Border Polyline (61点，闭合)
    └── Cone Side Polygons (20个三角形)

更新循环:
preUpdate Event → createOrUpdateProjection()
    ├── 计算卫星当前位置
    ├── 计算地面60个点
    ├── 移除旧实体
    └── 创建新实体
```

---

## 8. 关键代码片段

### 椭圆参数方程
```javascript
const theta = (i / NUM_POINTS) * 2 * Math.PI;
const dx = semiMajor * Math.cos(theta);  // 长轴方向
const dy = semiMinor * Math.sin(theta);  // 短轴方向
```

### 颜色转换
```javascript
function hexToRgb(hex) {
    const result = /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(hex);
    return {
        r: parseInt(result[1], 16) / 255,
        g: parseInt(result[2], 16) / 255,
        b: parseInt(result[3], 16) / 255
    };
}
```

---

## 9. 总结

- **实现方式**：Entity API（易用性优先）
- **轨道**：800km高度，51.6°倾角，90分钟周期
- **初始位置**：四川盆地（104°E, 30°N）
- **实时更新**：preUpdate 事件 + 删除重建实体
- **优化空间**：CallbackProperty、Primitive API、降低更新频率
