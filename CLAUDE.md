# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目概述

Wuhr AI VRAM Calculator 是一个专业的GPU VRAM（显存）计算工具，用于AI模型的训练、推理、微调等场景的内存需求计算。

## 常用开发命令

### 基本开发流程
```bash
# 安装依赖
npm install

# 启动开发服务器（使用 Turbopack 加速构建）
npm run dev

# 构建生产版本
npm run build

# 启动生产服务器
npm start
```

### 测试和验证
```bash
# 运行代码检查
npm run lint

# 测试 MCP 协议
npm run test:mcp

# 验证 MCP SDK
npm run mcp:validate
```

### Docker 部署
```bash
# 构建 Docker 镜像
npm run docker:build

# 运行 Docker 容器
npm run docker:run
```

### 中国网络环境
```bash
# 使用中国镜像源安装依赖
npm run install:cn
```

## 核心架构特点

### 技术栈
- **前端框架**: Next.js 15.3 + React 19 + TypeScript 5.0
- **状态管理**: Zustand 5.0.5（轻量级，支持持久化）
- **UI 组件**: Tailwind CSS + Radix UI + Framer Motion
- **构建工具**: Turbopack（开发模式），standalone 输出（生产模式）
- **PWA 支持**: next-pwa 5.6.0，支持离线使用

### 状态管理架构
- **CalculatorStore** (`src/store/calculator-store.ts`): 21KB 的中央状态管理
- **双层Tab系统**: Primary (NLP/Multimodal/Advanced) + Secondary (Training/Inference等)
- **持久化存储**: 使用 localStorage 中间件，历史记录和对比功能
- **防抖计算**: 避免频繁重新计算，优化性能

### 组件架构模式
- **动态导入**: 所有计算器组件都采用懒加载（React.lazy）
- **模块化设计**: 每个计算模式都有独立的组件
- **共享UI组件**: `/components/ui/` 下的可复用组件库
- **Context Providers**: 主题和语言作为全局上下文

### 核心计算引擎
- **统一LLM框架**: `src/utils/memory-formulas.ts` 实现所有VRAM计算公式
- **公式结构**: `Total VRAM = Model Weights + Optimizer States + Gradients + Activations + Overheads`
- **六种计算模式**: Inference, Fine-tuning, Training, GRPO, Multimodal, Advanced Fine-tuning
- **参数级控制**: 支持 modelSize, hiddenSize, layers 等架构参数精确控制

## 重要文件和目录结构

### 关键文件（按重要性排序）
- `src/app/page.tsx`: 主入口页面，复杂的双层Tab导航系统
- `src/store/calculator-store.ts` (21KB): 全局状态管理，支持6种计算器状态
- `src/components/calculators/advanced-fine-tuning-calculator.tsx` (87KB): 最复杂的计算器，支持4种模型类型
- `src/contexts/language-context.tsx` (60KB): 国际化支持，包含完整翻译数据
- `src/lib/models-data.ts` (34KB): 130+ 模型数据库，智能分类系统
- `src/utils/memory-formulas.ts`: 核心VRAM计算算法
- `src/mcp/server.ts` (12KB): MCP协议服务器实现

### 目录结构说明
```
src/
├── app/                    # Next.js App Router
│   ├── page.tsx           # 主页面（双层Tab导航）
│   ├── layout.tsx         # 根布局（主题/语言提供者）
│   └── api/               # API路由
├── components/
│   ├── calculators/       # 6个计算器组件（动态导入）
│   ├── ui/               # 可复用UI组件
│   └── *.tsx             # 功能组件（GPU推荐、历史面板等）
├── contexts/             # 全局上下文（主题、语言）
├── hooks/                # 自定义Hooks（防抖、错误处理、性能监控等）
├── lib/                  # 数据和工具库
├── mcp/                  # Model Context Protocol 实现
├── store/                # Zustand 状态管理
├── types/                # TypeScript 类型定义
└── utils/                # 工具函数（计算公式、验证等）
```

## 开发注意事项

### 构建配置特殊设置
- **TypeScript错误忽略**: `typescript.ignoreBuildErrors: true` - 为了Docker兼容性
- **ESLint警告忽略**: `eslint.ignoreDuringBuilds: true` - 专注于运行时功能
- **Standalone输出**: 优化的Docker部署格式
- **PWA缓存策略**: 1天缓存有效期，NetworkFirst策略

### 性能优化要点
- **首次加载优化**: 178KB（高度优化）
- **代码分割**: 计算器组件按需加载
- **防抖计算**: 避免过度计算
- **Web Workers**: 后台计算不阻塞UI

### 状态管理最佳实践
```typescript
// CalculatorStore 主要状态结构
{
  // Tab 管理
  primaryTab: 'nlp' | 'multimodal' | 'advanced',
  activeTab: 'training' | 'inference' | 'finetuning' | 'grpo',

  // 6种计算器配置
  trainingConfig, inferenceConfig, fineTuningConfig,
  grpoConfig, multimodalConfig, advancedFineTuningConfig,

  // 计算结果
  trainingResult, inferenceResult, fineTuningResult,
  grpoResult, multimodalResult, advancedResult,

  // 历史记录和对比
  history: CalculationHistory[],
  compareList: MemoryBreakdown[]
}
```

### MCP协议集成
- **双重实现**: 嵌入式（API路由）+ 独立npm包
- **端点**: `/api/mcp` 用于AI助手集成
- **功能**: Resources（只读数据）、Tools（可调用函数）、Prompts（模板建议）
- **健康检查**: `/api/health` 端点

## 数据库和模型管理

### 模型数据库 (`src/lib/models-data.ts`)
- **130+ NLP/LLM模型**: Qwen, DeepSeek, LLaMA, ChatGLM, Yi等
- **22+ 多模态模型**: Qwen2.5-VL, LLaVA, Whisper等
- **12+ 向量模型**: Qwen3-Embedding, Reranker系列
- **智能分类**: 基于架构的分组（transformer, multimodal, moe）
- **GPU规格**: 20+ GPU从RTX 3090到RTX 6000 Ada

### 模型特性
```typescript
interface ModelInfo {
  id: string;           // 模型标识
  name: string;         // 显示名称
  params: number;       // 参数量（十亿）
  architecture: 'transformer' | 'multimodal' | 'moe' | 'vector';
  defaultConfig: {};    // 默认配置
  quantization: boolean; // 量化支持
}
```

## API接口设计

### 健康检查
```http
GET /api/health
```
返回服务状态、版本信息、运行时间

### MCP协议端点
```http
POST /api/mcp
Content-Type: application/json
```
支持Model Context Protocol标准，用于AI助手集成

### 性能分析
```http
POST /api/analytics
```
收集计算性能指标和用户交互数据

## 核心计算公式架构

### 统一LLM框架
```
Total VRAM = Model Weights + Optimizer States + Gradients + Activations + Overheads
```

### 关键差异参数
- **P_train**: 可训练参数数量
  - Inference: P_train = 0
  - Fine-tuning: P_train << P_total (PEFT方法)
  - Training: P_train = P_total
  - GRPO: P_train = 1% × P_total + k倍激活值

### 计算模式特点
1. **Inference**: 量化模型权重 + KV缓存 + 最小激活值
2. **Fine-tuning**: 支持PEFT方法（LoRA, QLoRA, Prefix）
3. **Training**: 完整参数训练，支持梯度检查点
4. **GRPO**: k倍激活值，其中k为偏好组大小
5. **Multimodal**: 文本+图像+音频+视频token序列长度计算
6. **Advanced**: 4种模型类型的参数级控制

## 特殊功能实现

### 键盘快捷键支持
- 计算器间快速切换
- 主题和语言切换
- 历史记录和设置访问

### 性能监控
- 实时性能指标
- 交互行为追踪
- 计算耗时监控

### 错误处理机制
- 集中式错误管理
- 优雅错误显示
- 错误恢复建议

### PWA功能
- 离线功能
- 可安装为应用
- 1天缓存策略

## 开发调试技巧

### 状态调试
Zustand store在开发环境下支持Redux DevTools扩展

### MCP协议测试
```bash
# 测试MCP连接
npm run test:mcp

# 验证MCP工具
curl -X POST http://localhost:3000/api/mcp \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","method":"initialize","params":{},"id":1}'
```

### 计算公式验证
主要计算逻辑集中在 `src/utils/memory-formulas.ts`，可通过单元测试验证

## 部署相关配置

### Docker优化
- 多阶段构建
- Node.js 20基础镜像
- 健康检查端点
- Standalone输出格式

### 环境变量
- `NODE_ENV=production`: 生产模式
- PWA在开发环境下禁用

### 网络配置
- CORS头配置用于API路由
- 图片格式优化（AVIF, WebP）
- 压缩启用

这个项目是一个专业级的Web应用，结合了现代React/Next.js架构、高级状态管理、复杂的数学计算、专业的UI/UX设计和AI集成功能，适合AI/ML专业人员和普通用户使用。