# AI 食品卡路里分析应用 - 实施计划

## 产品概述

**NutriScan AI** - 智能食品卡路里分析应用，用户上传食物图片，AI 自动识别并展示卡路里和营养信息，支持云端历史记录。

---

## 核心功能

1. **图片上传** - 拖拽/点击上传食物图片，支持预览
2. **AI 智能分析** - 识别食物、估算卡路里和营养成分（蛋白质、碳水、脂肪）
3. **结果可视化** - 卡路里圆环图 + 营养成分进度条
4. **用户认证** - Supabase Auth 登录/注册
5. **历史记录** - 云端保存分析记录，查看每日摄入汇总

---

## 设计方案

**配色**：清新绿色主题
- 主色：`hsl(142, 71%, 45%)` - 健康绿
- 背景：`hsl(138, 76%, 97%)` - 极浅绿
- 营养素颜色：蛋白质(蓝)、碳水(橙)、脂肪(紫)、纤维(绿)

---

## 数据库表结构 (Supabase)

```sql
-- 1. 食物分析记录表
CREATE TABLE food_analysis_records (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id UUID REFERENCES auth.users(id) NOT NULL,
  image_url TEXT NOT NULL,
  food_name TEXT NOT NULL,
  calories DECIMAL(10, 2) NOT NULL,
  protein DECIMAL(10, 2) NOT NULL,
  carbohydrates DECIMAL(10, 2) NOT NULL,
  fat DECIMAL(10, 2) NOT NULL,
  fiber DECIMAL(10, 2),
  serving_size TEXT,
  health_tips TEXT,
  analyzed_at TIMESTAMPTZ DEFAULT NOW()
);

-- 2. Storage bucket for food images
INSERT INTO storage.buckets (id, name, public) VALUES ('food-images', 'food-images', true);
```

---

## 需创建的文件

```
src/
├── index.css                          # [修改] 绿色主题变量
├── App.tsx                            # [修改] 添加路由
├── pages/
│   ├── Index.tsx                      # [重写] 首页-图片上传与分析
│   └── History.tsx                    # [新建] 历史记录页
├── components/
│   ├── layout/
│   │   └── Header.tsx                 # 顶部导航
│   ├── auth/
│   │   └── AuthModal.tsx              # 登录/注册弹窗
│   └── food-analyzer/
│       ├── ImageUploader.tsx          # 图片上传组件
│       ├── AnalysisResult.tsx         # 分析结果展示
│       ├── CalorieCircle.tsx          # 卡路里圆环图
│       └── NutritionBar.tsx           # 营养成分进度条
├── hooks/
│   ├── useAuth.ts                     # 认证 hook
│   └── useFoodAnalysis.ts             # 分析 hook
├── lib/
│   └── supabase.ts                    # Supabase 客户端
└── types/
    └── food.ts                        # 类型定义

supabase/functions/
└── analyze-food/index.ts              # AI 分析 Edge Function
```

---

## 实施步骤

### Step 1: 设计系统配置
- 更新 `index.css` 添加绿色主题变量
- 定义营养素颜色变量

### Step 2: 集成 Supabase
- 使用 `supabase-integration` skill
- 创建数据库表和 Storage bucket
- 配置认证

### Step 3: 集成 AI 服务
- 使用 `ai-integration` skill
- 创建 `analyze-food` Edge Function
- 配置食物识别 prompt

### Step 4: 创建核心组件
- ImageUploader (拖拽上传)
- AnalysisResult (结果展示)
- CalorieCircle (卡路里圆环)
- NutritionBar (营养进度条)

### Step 5: 实现首页
- 整合上传和分析组件
- 添加加载状态和错误处理

### Step 6: 实现认证
- AuthModal 登录/注册弹窗
- Header 用户状态显示

### Step 7: 实现历史记录
- History 页面
- 历史记录列表和每日汇总

---

## 验证方案

1. ✅ 上传一张食物图片（如苹果、汉堡）
2. ✅ 验证 AI 返回正确的食物识别结果
3. ✅ 检查卡路里和营养数据显示
4. ✅ 登录后保存记录到云端
5. ✅ 在历史页面查看保存的记录
6. ✅ 测试移动端响应式布局
