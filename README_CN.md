# 3Dto2Dshape - 3D 到 2D 形状渲染管线

一个基于 Three.js 的 3D 模型到 2D 形状转换工具，支持 MMD 模型加载、动画播放和部件分割。

## 功能特性

- 🎭 **MMD 模型支持**：加载 PMX 格式的 3D 模型
- 🎬 **动画播放**：支持 VMD 格式的动画文件
- ✂️ **部件分割**：自动将模型按材质分割成独立部件
- 🎨 **2D 投影**：生成模型的 2D 轮廓和投影
- 🖱️ **交互操作**：点击选择部件，实时预览效果

## 快速开始

### 1. 安装依赖

```bash
npm install
```

### 2. 准备模型文件

项目需要 MMD 格式的模型文件。请按以下结构放置文件：

```
public/
└── models/
    ├── Corin/                    # 模型文件夹（以模型名命名）
    │   ├── Corin.pmx            # PMX 模型文件
    │   └── textures/            # 纹理文件夹（如果有）
    │       ├── texture1.png
    │       └── texture2.png
    └── vmd/                      # VMD 动画文件夹
        ├── Aerial.vmd
        └── 我最可爱的地方_30s免费版.vmd
```

**重要说明**：
- `public/models/` 是模型文件的根目录
- 每个模型单独放在一个子文件夹中
- PMX 模型文件必须放在对应的模型文件夹内
- VMD 动画文件统一放在 `public/models/vmd/` 目录

### 3. 启动开发服务器

```bash
npm run dev
```

浏览器打开 `http://localhost:5173` 即可看到效果。

## 使用指南

### 界面布局

界面分为三个区域：
- **左侧控制面板**：动画控制、部件选择、投影设置
- **中间视口**：3D 模型预览
- **右侧结果面板**：2D 投影结果

### 模型操作

#### 加载模型
1. 将 PMX 模型文件放入 `public/models/模型名/` 目录
2. 修改 `src/App.tsx` 中的模型加载路径（约第 619 行）：

```typescript
loader.load(
    `${import.meta.env.BASE_URL}models/你的模型名/你的模型文件.pmx`,
    // ... 其他代码
);
```

#### 添加动画
1. 将 VMD 动画文件放入 `public/models/vmd/` 目录
2. 在 `src/App.tsx` 中添加动画选项（约第 32-45 行）：

```typescript
const VMD_ANIMATION_OPTIONS = [
    {
        label: '初始姿势',
        value: INITIAL_POSE_ANIMATION_VALUE,
    },
    {
        label: '你的动画名称',
        value: '你的动画文件名.vmd',
    },
    // ... 添加更多动画
];
```

### 交互操作

#### 鼠标操作
- **左键点击**：选择部件（高亮显示）
- **右键点击**：显示三角形调试信息（控制台输出）
- **鼠标拖拽**：旋转视角
- **滚轮**：缩放视图

#### 动画控制
- **Play/Pause**：播放/暂停动画
- **-1/+1**：前进/后退一帧
- **-S/+S**：前进/后退指定帧数
- **Frame Step**：调整步进帧数（1-8 帧）

#### 投影设置
- **Overlay**：启用/禁用 2D 投影叠加
- **Contours**：显示/隐藏轮廓线
- **Simplify**：简化精度（值越大，轮廓越简单）
- **Min Triangles**：最小三角形数量阈值

### 部件选择

左侧面板显示模型的所有部件（按材质分割）：
- 点击部件名称可高亮显示该部件
- 点击 "All" 显示所有部件
- 部件数量显示三角形数量
- 支持折叠/展开子部件

## 项目结构

```
src/
├── App.tsx                    # 主应用组件
├── main.tsx                   # 入口文件
├── styles.css                 # 全局样式
├── components/
│   ├── PartPanel.tsx          # 部件选择面板
│   └── ProjectionOverlay.tsx  # 2D 投影覆盖层
└── lib/
    ├── modelParts.ts          # 模型部件分割逻辑
    ├── 2DRenderPipeline/      # 2D 渲染管线
    ├── 2DRenderShared/        # 共享数据和类型
    └── 2DRenderStages/        # 渲染阶段
        ├── meshProjection/    # 网格投影
        ├── partRasterization/ # 部件光栅化
        ├── partShaping/       # 部件形状生成
        ├── partFiltering/     # 部件过滤
        └── composition/       # 最终合成
```

## 技术细节

### 模型分割算法

项目使用基于材质的自动分割算法：
1. 读取 PMX 模型的所有材质
2. 根据材质属性（颜色、纹理）自动分组
3. 生成部件层次结构
4. 支持自定义部件命名

### 2D 渲染管线

渲染管线包含以下阶段：
1. **网格投影**：将 3D 网格投影到屏幕空间
2. **部件光栅化**：生成部件的遮罩
3. **部件形状**：从遮罩提取轮廓
4. **部件过滤**：简化和过滤轮廓
5. **最终合成**：生成最终的 2D 渲染结果

## 常见问题

### Q: 模型加载失败怎么办？
A: 检查以下几点：
- 模型文件路径是否正确
- PMX 文件是否完整（包含纹理引用）
- 浏览器控制台是否有错误信息

### Q: 动画不播放怎么办？
A: 确认：
- VMD 文件路径正确
- 动画文件与模型骨骼兼容
- 已选择正确的动画选项

### Q: 如何添加自己的模型？
A: 步骤：
1. 准备 PMX 格式的模型文件
2. 放入 `public/models/你的模型名/` 目录
3. 修改 `App.tsx` 中的模型加载路径
4. 重启开发服务器

### Q: 如何调整渲染效果？
A: 在左侧面板调整：
- **Simplify**：控制轮廓简化程度
- **Min Triangles**：过滤小部件
- **Stroke Width**：调整线条粗细（需要修改代码）

## 开发命令

```bash
# 启动开发服务器
npm run dev

# 构建生产版本
npm run build

# 预览构建结果
npm run preview
```

## 依赖说明

- **Three.js**：3D 渲染引擎
- **React**：UI 框架
- **Vite**：构建工具
- **TypeScript**：类型安全

## 许可证

本项目仅供学习和研究使用。使用的模型和动画文件请遵守其原始许可证。

## 相关资源

- [Three.js 文档](https://threejs.org/docs/)
- [MMD 格式说明](http://www.mikumikudance.com/)
- [PMX 模型规范](https://gist.github.com/felixjones/f8a06bd48f9da1c14f5739b1b4e2f5e6)
