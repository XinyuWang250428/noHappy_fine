# 意遇重生 - 智能心理健康评估平台

## 基于 <a href="https://www.shadcn-vue.com/" target="_blank">Shadcn-Vue</a> + <a href="https://vuejs.org/" target="_blank">Vue.js</a> + <a href="https://www.typescriptlang.org/" target="_blank">TypeScript</a> + <a href="https://tailwindcss.com/" target="_blank">Tailwind CSS</a> 构建

![意遇重生概念图](./public/概念.png)

基于AI技术的多模态数字疗法抑郁症智能评估平台，提供心理量表、心电信号、情绪识别、基因筛查等综合评估服务。

## 主要功能模块

- [x] 导航栏 (Navbar)
- [x] 移动端侧边栏 (Sidebar)
- [x] 主页展示 (Hero)
- [x] 赞助商展示 (Sponsors)
- [x] 平台优势 (Benefits)
- [x] 核心功能 (Features)
- [x] 服务介绍 (Services)
- [x] 工作流程 (HowItWorks)
- [x] **心理量表评估** (QuestionnaireAssessment)
- [x] **心电信号筛查** (ECGScreening)
- [x] **基因辅助分析** (GeneScreening)
- [x] **情绪表情识别** (EmotionRecognition)
- [x] 评估报告 (Report)
- [x] 专业团队 (Team)
- [x] 联系我们 (Contact)
- [x] 常见问题 (FAQ)
- [x] 页脚 (Footer)

## 技术特性

- [x] 完全响应式设计
- [x] 用户友好的导航体验
- [x] 深色/浅色主题切换
- [x] SEO优化的Meta标签
- [x] 多模态心理健康评估系统
- [x] 实时AI情绪识别功能
- [x] Face-API.js 人脸检测集成
- [x] TensorFlow.js 机器学习支持
- [x] Chart.js 数据可视化
- [x] Canvas动态心电图渲染
- [x] RequestAnimationFrame平滑动画

## 核心评估功能

### 🧠 1. 心理量表评估 (PSY)
- **PHQ-9抑郁量表评分**：标准化抑郁症状评估
- **GAD-7焦虑量表评分**：焦虑水平综合测评
- **PSS-10压力量表评分**：压力感知程度分析
- **社会支持评估**：社会支持网络强度测量
- **权重系数**：40% (w₁ = 0.4)

### 💓 2. 心电信号分析 (ECG)
- **动态心电图显示**：实时滚动心电波形展示
- **数据自动过滤**：自动过滤异常值（<20000）
- **数据标准化处理**：[0,1]范围标准化确保准确性
- **5秒动态窗口**：平滑滚动显示效果
- **心率变异性分析**：自主神经功能综合评估
- **权重系数**：25% (w₂ = 0.25)

### 😊 3. 情绪表情识别 (EMO)
- **情绪表达能力指数**：面部表情识别准确度
- **情绪转换效率**：情绪状态切换速度分析
- **悲伤情绪干扰度**：负面情绪持续时间评估
- **面部表情活跃度**：表情变化频率统计
- **权重系数**：25% (w₃ = 0.25)

### 🧬 4. 基因筛查分析 (GEN)
- **抑郁症相关基因位点变异**：遗传风险基因检测
- **遗传风险评分（GRS）**：综合遗传风险量化
- **药物代谢基因分型**：个性化用药指导
- **表观遗传标记物分析**：环境因素影响评估
- **权重系数**：10% (w₄ = 0.1)

## 综合评分算法

### 风险等级判定标准
```javascript
总分 = 0.4×PSY_Score + 0.25×ECG_Score + 0.25×EMO_Score + 0.1×GEN_Score

风险等级：
- 低风险：总分 ≥ 80
- 轻度风险：60 ≤ 总分 < 80  
- 中度风险：40 ≤ 总分 < 60
- 高度风险：总分 < 40
```

## 安装与运行

### 环境要求
- Node.js (v16 或更高版本)
- npm/pnpm/yarn 包管理器
- 现代浏览器支持（Chrome 80+, Firefox 72+, Safari 13+, Edge 80+）

### 安装步骤

1. **克隆项目**
```bash
git clone <项目仓库地址>
```

2. **进入项目目录**
```bash
cd ultra-nohappy/shadcn-vue-landing-page
```

3. **安装依赖**
```bash
npm install
# 或者
pnpm install
```

4. **启动开发服务器**
```bash
npm run dev
# 或者
pnpm dev
```

5. **构建生产版本**
```bash
npm run build
# 或者
pnpm build
```

## 项目结构

```
shadcn-vue-landing-page/
├── src/
│   ├── components/          # 业务组件
│   │   ├── ui/             # UI基础组件（基于Radix-Vue）
│   │   ├── ECGScreening.vue    # 心电图筛查组件（动态显示+数据过滤）
│   │   ├── EmotionRecognition.vue # 情绪识别组件
│   │   ├── GeneScreening.vue   # 基因筛查组件
│   │   └── QuestionnaireAssessment.vue # 问卷评估组件
│   ├── assets/             # 静态资源
│   ├── icons/              # 图标组件
│   └── lib/                # 工具函数
├── public/                 # 公共资源
│   ├── models/             # AI模型文件
│   ├── 图标.svg            # 项目图标
│   └── 概念.png            # 概念展示图
└── demo/                   # 演示页面
    ├── face-emotion-recognition-master/ # 人脸情绪识别Demo
    └── js/                 # JavaScript工具
```

## 技术栈

### 前端框架
- **Vue 3** (^3.5.12) - 渐进式JavaScript框架
- **TypeScript** (^5.4.5) - 类型安全的JavaScript超集
- **Vite** (^7.0.0) - 下一代前端构建工具

### UI组件库
- **Shadcn/Vue** - 基于Radix-Vue的组件库
- **Tailwind CSS** (^3.4.4) - 原子化CSS框架
- **Lucide Vue Next** - 美观的图标库

### AI与机器学习
- **TensorFlow.js** - 浏览器端机器学习
- **Face-API.js** - 人脸识别与表情分析
- **Chart.js** (^4.5.0) - 数据可视化图表库

### 开发工具
- **VueUse** (^11.1.0) - Vue组合式API工具集
- **Vee-validate** (^4.13.2) - 表单验证库
- **Zod** (^3.23.8) - TypeScript模式验证

## 许可证

本项目基于 MIT 许可证开源。详见 [LICENSE](./LICENSE) 文件。

## 贡献指南

欢迎提交问题和功能请求！在贡献代码之前，请阅读我们的贡献指南。

## 联系我们

- **项目团队**：意遇重生开发团队
- **技术支持**：如有技术问题请提交 Issue
- **商务合作**：请通过联系页面获取联系方式

---

**注意事项**：
1. 本平台仅供辅助评估使用，不能替代专业医疗诊断
2. 人脸识别和情绪分析数据仅在本地处理，保护用户隐私
3. 定期更新依赖包，确保安全性和稳定性
