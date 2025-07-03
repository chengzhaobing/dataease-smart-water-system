# DataEase 水务系统二次开发部署指南

## 概述

本指南详细说明如何将开发的水质热力图组件和水务SCADA连接器集成到实际的DataEase项目中。

## 前置条件

### 系统要求
- Java 8+ (推荐 Java 11)
- Node.js 14+ 
- npm 或 yarn
- MySQL 5.7+ 或 PostgreSQL 10+
- DataEase 1.18+ 版本

### 开发环境
- IDE: IntelliJ IDEA 或 Eclipse
- 前端开发工具: VS Code
- 数据库管理工具: Navicat 或 DBeaver

## 部署步骤

### 1. 获取DataEase源码

```bash
# 克隆DataEase项目
git clone https://github.com/dataease/dataease.git
cd dataease

# 切换到稳定版本
git checkout v1.18.x
```

### 2. 后端集成 - 水务SCADA连接器

#### 2.1 复制Provider文件

```bash
# 将开发的Provider文件复制到DataEase项目中
cp core/core-backend/src/main/java/io/dataease/provider/Provider.java \
   dataease/core/core-backend/src/main/java/io/dataease/provider/

cp core/core-backend/src/main/java/io/dataease/provider/WaterSCADAProvider.java \
   dataease/core/core-backend/src/main/java/io/dataease/provider/
```

#### 2.2 修改DataEase配置

在 `dataease/core/core-backend/src/main/resources/application.yml` 中添加:

```yaml
# 水务SCADA配置
water:
  scada:
    enabled: true
    simulation-mode: true
    default-timeout: 30000
    max-connections: 50
    data-retention-days: 365
```

#### 2.3 注册数据源类型

在 `dataease/core/core-backend/src/main/java/io/dataease/provider/ProviderFactory.java` 中添加:

```java
// 添加水务SCADA提供者
case "WATER_SCADA":
    return new WaterSCADAProvider();
```

#### 2.4 添加数据库依赖

在 `dataease/core/core-backend/pom.xml` 中添加:

```xml
<!-- 水务SCADA相关依赖 -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.33</version>
</dependency>

<dependency>
    <groupId>com.zaxxer</groupId>
    <artifactId>HikariCP</artifactId>
    <version>5.0.1</version>
</dependency>
```

### 3. 前端集成 - 水质热力图组件

#### 3.1 复制Vue组件

```bash
# 复制水质热力图组件
cp core/core-frontend/src/views/dashboard/WaterQualityChart.vue \
   dataease/core/core-frontend/src/views/chart/components/
```

#### 3.2 注册组件

在 `dataease/core/core-frontend/src/views/chart/components/index.js` 中添加:

```javascript
import WaterQualityChart from './WaterQualityChart.vue'

export {
  // ... 其他组件
  WaterQualityChart
}
```

#### 3.3 添加图表类型

在 `dataease/core/core-frontend/src/views/chart/chart/chart.js` 中添加:

```javascript
export const CHART_TYPE = {
  // ... 其他类型
  water_quality_heatmap: 'water-quality-heatmap'
}

export const CHART_TYPE_CONFIGS = {
  // ... 其他配置
  [CHART_TYPE.water_quality_heatmap]: {
    title: '水质热力图',
    icon: 'chart-heatmap',
    category: 'map',
    component: 'WaterQualityChart'
  }
}
```

### 4. 数据库配置

#### 4.1 创建水务SCADA数据库

```sql
-- 创建数据库
CREATE DATABASE water_scada CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

-- 使用数据库
USE water_scada;

-- 创建监测站点表
CREATE TABLE monitoring_stations (
    station_id VARCHAR(50) PRIMARY KEY,
    station_name VARCHAR(100) NOT NULL,
    location VARCHAR(200),
    latitude DECIMAL(10, 8),
    longitude DECIMAL(11, 8),
    station_type VARCHAR(50),
    status VARCHAR(20) DEFAULT '正常',
    install_date DATE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

-- 创建水质数据表
CREATE TABLE water_quality_data (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    station_id VARCHAR(50) NOT NULL,
    parameter_name VARCHAR(50) NOT NULL,
    parameter_value DECIMAL(10, 4),
    unit VARCHAR(20),
    quality_level VARCHAR(20),
    measurement_time TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_station_time (station_id, measurement_time),
    INDEX idx_parameter_time (parameter_name, measurement_time),
    FOREIGN KEY (station_id) REFERENCES monitoring_stations(station_id)
);

-- 创建报警记录表
CREATE TABLE alarm_records (
    alarm_id VARCHAR(50) PRIMARY KEY,
    station_id VARCHAR(50) NOT NULL,
    parameter_name VARCHAR(50) NOT NULL,
    alarm_level VARCHAR(20) NOT NULL,
    alarm_value DECIMAL(10, 4),
    threshold_value DECIMAL(10, 4),
    alarm_time TIMESTAMP,
    status VARCHAR(20) DEFAULT '未处理',
    description TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (station_id) REFERENCES monitoring_stations(station_id)
);
```

### 5. 编译和部署

#### 5.1 后端编译

```bash
cd dataease/core/core-backend
mvn clean package -DskipTests
```

#### 5.2 前端编译

```bash
cd dataease/core/core-frontend
npm install
npm run build
```

#### 5.3 启动服务

```bash
# 启动后端服务
java -jar target/dataease-backend.jar

# 启动前端服务（开发模式）
npm run serve
```

## 配置说明

### 水务SCADA连接器配置

```json
{
  "type": "WATER_SCADA",
  "name": "水务SCADA系统",
  "config": {
    "simulationMode": true,
    "databaseUrl": "jdbc:mysql://localhost:3306/water_scada",
    "username": "scada_user",
    "password": "password",
    "maxConnections": 10,
    "timeout": 30000
  }
}
```

### 水质热力图组件配置

```json
{
  "chartType": "water-quality-heatmap",
  "title": "水质监测热力图",
  "dataSource": "WATER_SCADA",
  "parameters": {
    "parameter": "pH",
    "timeRange": "24h",
    "refreshInterval": 30000
  }
}
```

## 故障排除

### 常见问题

1. **数据源连接失败**
   - 检查数据库连接配置
   - 确认网络连通性
   - 验证用户权限

2. **组件加载失败**
   - 检查组件注册是否正确
   - 确认依赖是否安装
   - 查看浏览器控制台错误

3. **数据显示异常**
   - 检查数据格式是否正确
   - 确认时间范围设置
   - 验证参数映射

### 日志配置

在 `application.yml` 中添加:

```yaml
logging:
  level:
    io.dataease.provider: DEBUG
    io.dataease.chart: DEBUG
```

## 性能优化

### 数据库优化
- 添加适当的索引
- 配置连接池
- 定期清理历史数据

### 前端优化
- 启用图表懒加载
- 配置数据缓存
- 优化渲染性能

## 安全配置

### 数据库安全
- 使用专用数据库用户
- 限制访问权限
- 启用SSL连接

### API安全
- 配置访问控制
- 启用请求限流
- 添加审计日志

---

更多详细信息请参考 [DataEase官方文档](https://dataease.io/docs/)