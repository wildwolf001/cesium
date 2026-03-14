# Cesium 3D 地图应用
基于 Vue 3 + Cesium 的 3D 地图可视化应用，提供位置导航和地图查询功能。
## 主要功能
- 3D 地球展示与交互
- 位置导航功能（支持经纬度和高度设置）
- 自定义地图图层
- 响应式控制面板
## 配置要求
- **Vue 3.5.30**  https://github.com/vuejs/core/releases/tag/v3.5.30
- **Cesium 1.139.1** https://github.com/CesiumGS/cesium/releases/tag/1.139.1
- **TypeScript** https://www.typescriptlang.org/
- **Vite 8.0.0** https://github.com/vitejs/vite/releases/tag/v8.0.0
- **Vue TSC** https://github.com/znck/vue-tsc/releases/tag/v0.40.2
- **node 18.16.0** https://nodejs.org/en/download/releases/
- **npm 9.6.3** https://github.com/npm/cli/releases/tag/v9.6.3
- 

## 本地部署

### 安装步骤
1. 克隆项目到本地
```bash
git clone [项目地址]
cd cesium
```
2. 安装依赖
```bash
npm install
```
3. 启动开发服务器
```bash
npm run dev
```
4. 构建生产版本
```bash
npm run build
```
5. 预览生产构建
```bash
npm run preview
```
## 项目结构

```
cesium/
├── public/          # 静态资源
│   └── map/        # 地图瓦片数据
├── src/
│   ├── components/ # Vue 组件
│   ├── views/     # 页面视图
│   ├── assets/    # 资源文件
│   ├── App.vue    # 根组件
│   ├── main.ts    # 入口文件
│   └── style.css  # 全局样式
├── index.html      # HTML 模板
├── package.json    # 项目配置
└── vite.config.ts # Vite 配置
```
## 注意事项
- 确保 `public/map/` 目录下有正确的地图瓦片数据
- 如需使用 Cesium Ion 服务，请在代码中配置访问令牌

