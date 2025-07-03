# DataEase智慧水务系统

基于DataEase开源BI工具的智慧水务监测与管理平台

## 🌊 项目概述

本项目是一个完整的智慧水务系统，集成了实时数据监测、3D可视化、数据分析等功能。采用前后端分离架构，支持多种数据源接入和丰富的可视化展示。

## ✨ 核心功能

### 🗺️ 3D态势感知
- **3D地球仪显示**：基于ECharts GL的3D地球仪
- **监测站点标记**：实时显示监测站点位置和状态
- **站点连线**：可视化站点间的关联关系
- **状态着色**：根据水质状况进行颜色区分

### 📊 数据可视化
- **实时水质监测**：pH值、溶解氧、浊度、氨氮等参数
- **流量趋势分析**：历史数据趋势图表
- **异常报警**：自动检测和报警机制
- **数据流监控**：实时数据传输状态

### 🔧 技术特性
- **前后端分离**：Java后端 + HTML5前端
- **实时数据**：支持API数据和模拟数据
- **响应式设计**：适配不同屏幕尺寸
- **模块化架构**：易于扩展和维护

## 🚀 快速开始

### 环境要求
- Java 8+
- Python 3.7+
- Maven 3.6+
- 现代浏览器（支持WebGL）

### 安装运行

1. **克隆项目**
```bash
git clone https://github.com/chengzhaobing/dataease-smart-water-system.git
cd dataease-smart-water-system
```

2. **启动后端服务**
```bash
cd core/core-backend
java -cp src/main/java io.dataease.WaterSystemApplication
```

3. **启动前端服务**
```bash
python -m http.server 3000
```

4. **访问系统**
```
http://localhost:3000/web_dashboard.html
```

## 📁 项目结构

```
dataease-smart-water-system/
├── 📁 core/                     # 核心代码
│   ├── 📁 core-backend/         # Java后端
│   └── 📁 core-frontend/        # 前端组件
├── 📄 web_dashboard.html        # 主仪表板
├── 📄 data_pipeline_*.py        # 数据处理脚本
├── 📄 *.json                    # 配置文件
└── 📄 *.md                      # 文档
```

## 🛠️ 技术栈

### 后端技术
- **Java 8+**: 核心开发语言
- **Maven**: 项目管理和构建
- **HTTP Server**: 内置HTTP服务器
- **JSON**: 数据交换格式

### 前端技术
- **HTML5**: 页面结构
- **JavaScript ES6+**: 交互逻辑
- **ECharts**: 2D图表库
- **ECharts GL**: 3D可视化
- **CSS3**: 样式和动画

### 数据处理
- **Python**: 数据分析和处理
- **Apache NiFi**: 数据流水线
- **JSON**: 配置和数据格式

## 📈 功能演示

### 智慧水务3D态势感知
- ✅ 5个监测站点实时显示
- ✅ 3D地球仪交互操作
- ✅ 水质状态颜色编码
- ✅ 实时数据更新

### 核心指标监控
- ✅ 水质综合指数：85.2%
- ✅ 在线监测站点：5个
- ✅ 异常报警：2个
- ✅ 数据更新频率：实时

## 📚 文档

- [技术指南](项目技术指南.md) - 详细的技术说明和环境搭建
- [部署指南](DEPLOYMENT_GUIDE.md) - 生产环境部署说明
- [数据流水线指南](PIPELINE_GUIDE.md) - 数据处理流程

## 🤝 贡献

欢迎提交Issue和Pull Request来改进项目！

## 📄 许可证

本项目基于Apache 2.0许可证开源。

---

**开发者**: chengzhaobing  
**最后更新**: 2025年7月
