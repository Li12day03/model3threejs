# Model3ThreeJsExpo

基于 **Three.js** 的特斯拉 Model 3 互动三维展示项目。通过 WebGL 场景、模型动画与页面交互，可视化展示 Model 3 的展厅外观、Autopilot 辅助驾驶与 FSD 完全自动驾驶等概念。

| 资源     | 链接 |
| -------- | ---- |
| 在线预览 | -    |
| GitHub   | -    |

---

## 功能概览

页面底部导航可在三个场景间切换：

1. **首页**  
   展厅环境中的 Model 3 三维模型，含反射地面、环境贴图、加速流光与相机抖动等效果。
2. **Autopilot**  
   道路场景中演示辅助驾驶感知：距离预警线、护盾效果与车辆动画。
3. **FSD**  
   城市道路场景中演示完全自动驾驶：行驶预测路线与镜头运镜动画。

资源加载完成后会出现 Loading 结束过渡，随后进入可交互的 3D 场景。

---

## 技术栈

| 类别        | 技术                        |
| ----------- | --------------------------- |
| 语言 / 构建 | TypeScript、Vite            |
| 三维引擎    | Three.js                    |
| 场景封装    | kokomi.js                   |
| 动画        | GSAP、TWEEN                 |
| 音频        | Howler                      |
| 后处理      | postprocessing              |
| 着色器      | GLSL（`vite-plugin-glsl`）  |
| 模型格式    | GLTF / GLB、FBX，Draco 压缩 |

---

## 环境要求

- **Node.js**：建议 18+（推荐 20 / 22）
- **包管理器**：推荐 [pnpm](https://pnpm.io/)，也可使用 npm / yarn
- **浏览器**：Chrome / Edge / Firefox 等支持 WebGL 的现代浏览器

---

## 快速开始

### 1. 进入项目目录

```sh
cd Model3ThreeJsExpo-main
```

### 2. 安装依赖

```sh
pnpm i
```

使用 npm 时：

```sh
npm install
```

### 3. 启动本地开发

```sh
pnpm run dev
```

或：

```sh
npm run dev
```

默认开发地址为：

```text
http://localhost:3000
```

启动成功后应看到黑色背景 + Loading，随后进入 Model 3 三维场景。

### 4. 构建生产包

```sh
pnpm run build
```

产物输出到 `dist/`。

### 5. 预览构建结果

```sh
pnpm run preview
```

---

## 正确运行时的表现

| 状态 | 说明                                                          |
| ---- | ------------------------------------------------------------- |
| 正常 | 全屏黑底、Loading 动画 → 3D 展厅 / 车辆模型出现，底部导航可用 |
| 异常 | 白底、仅「首页 / Autopilot / FSD」等 HTML 文字、无画布与样式  |

若出现「异常」表现，通常是 **没有走 Vite 开发服务**，或 **JS 初始化失败**。请看下方「常见问题」。

---

## 项目结构

```text
.
├── index.html                 # 页面入口（导航、文案 DOM）
├── style.css                  # 页面 UI 样式
├── package.json               # 脚本与依赖
├── vite.config.ts             # Vite 配置（含 GLSL 插件）
├── public/                    # 静态资源（直接可访问）
│   ├── audio/                 # 背景音乐
│   ├── draco/                 # Draco 解码器
│   ├── font/                  # 字体
│   ├── mesh/                  # 车辆 / 道路 / 展厅等模型
│   └── texture/               # AO、法线、HDR 环境贴图等
└── src/
    ├── main.ts                # 应用入口，挂载 Experience
    ├── style.css              # 画布 / Loading 相关样式
    └── Experience/
        ├── Experience.ts      # 场景总控（相机、资源、后处理）
        ├── resources.ts       # 资源清单
        ├── Postprocessing.ts  # 后期效果
        ├── Debug.ts           # 调试面板
        ├── Shaders/           # 自定义着色器
        └── World/             # 各业务场景模块
            ├── World.ts       # 世界调度与场景切换
            ├── Car.ts         # 车辆
            ├── StartRoom.ts   # 起始展厅
            ├── Speedup.ts     # 加速流光
            ├── CameraShake.ts # 相机抖动
            ├── Road.ts        # Autopilot 道路 / 预警线
            ├── City.ts        # FSD 城市道路 / 预测路线
            └── ...
```

---

## 场景与代码对应

| 页面      | 主要逻辑                                                 | 说明                         |
| --------- | -------------------------------------------------------- | ---------------------------- |
| 首页      | `StartRoom.ts`、`Car.ts`、`Speedup.ts`、`CameraShake.ts` | 展厅、车辆、流光、镜头抖动   |
| Autopilot | `Road.ts`                                                | 道路模型、预警护盾、车辆动画 |
| FSD       | `City.ts`                                                | 城市道路、行驶预测管道路线   |

场景切换与时间轴动画集中在 `World.ts`。

首页加速流光与相机抖动可参考：[alphardex 相关文章](https://juejin.cn/post/7352762271003017252)。

---

## 关键概念

### Catmull-Rom 样条曲线

使用 `THREE.CatmullRomCurve3`，由一组控制点插值出平滑三维曲线，常用于相机路径或轨迹线。

本项目中用于 **Autopilot** 距离预警线等效果。

### 管道几何体（TubeGeometry）

沿一条路径生成有厚度的管状几何体，适合表现道路、隧道、预测轨迹等。

本项目中用于 **FSD** 行驶预测路线等效果。

---

## npm scripts

| 命令               | 作用                              |
| ------------------ | --------------------------------- |
| `pnpm run dev`     | 启动 Vite 开发服务器（端口 3000） |
| `pnpm run build`   | TypeScript 检查 + 生产构建        |
| `pnpm run preview` | 预览 `dist` 构建结果              |

---

## 常见问题

### 1. 打开后只有白底文字，没有 3D

常见原因：

1. **直接用浏览器打开了 `index.html`**，或用普通静态服务器打开了源码目录，而没有启动 Vite。  
   → 请务必执行 `pnpm run dev`，再访问终端打印的本地地址。
2. **JS 报错导致 Experience 未初始化**。  
   → 打开浏览器开发者工具（F12）→ Console，查看红色报错。
3. **端口开错 / 开的是别的项目**。  
   → 确认访问的是本项目 `dev` 命令输出的地址（默认 `http://localhost:3000`）。

### 2. 依赖安装失败

- 确认 Node 版本 ≥ 18
- 删除 `node_modules` 后重新安装：

```sh
rm -rf node_modules
pnpm i
```

### 3. 模型或贴图加载很慢 / 黑屏很久

首次加载会拉取 `public/mesh`、`public/texture` 等较大资源，请等待 Loading 结束。网络或磁盘较慢时可能需要更长时间。

### 4. 构建失败

先保证本地 `pnpm run dev` 可正常打开场景，再执行 `pnpm run build`。若 TypeScript 报错，按终端提示修复后再构建。

---

## 技术

- 三维交互基于 Three.js / kokomi.js 等开源库
