---
name: keyword-research
description: Keyword research and analysis skill for any website. Performs systematic keyword discovery, competitive gap analysis, intent classification, difficulty scoring, and priority ranking. Use when the user wants to find new keyword opportunities, analyze which keywords competitors rank for, audit existing keyword coverage, build a keyword map, or plan content around search demand. Works for any domain, language, or niche.
version: 1.0.0
---

# Keyword Research Skill

必须加载 web-access skill 并遵循指引。

## 启动流程

### 第一步：加载域名档案

从用户提供的 URL 中提取域名（如 `sunriselink.sg`），然后读取档案文件：

```
~/.claude/skills/keyword-research/data/{domain}.json
```

- **档案存在** → 直接加载，跳过已知字段的询问，只问"本次分析重点"
- **档案不存在** → 新建，依次询问所有必要信息后保存

如果用户未提供 URL，先询问目标网站 URL，提取域名后再检查档案。

### 第二步：补全缺失信息

只询问档案中尚未记录的字段：

| 字段 | 说明 | 首次必问 | 后续 |
|-----|------|---------|-----|
| `url` | 完整网站 URL | 是 | 如果已知则跳过 |
| `business` | 业务描述（一句话） | 是 | 如果已知则跳过 |
| `region` | 目标地区（如 Singapore） | 是 | 如果已知则跳过 |
| `language` | 目标语言（如 English, Chinese） | 是 | 如果已知则跳过 |
| `competitors` | 竞品网站列表 | 可选 | 如果已知则跳过；用户可随时更新 |
| `tools.gsc` | GSC 访问权限 | 首次遇到 GSC 时问 | 已知，跳过 |
| `tools.ahrefs` | Ahrefs 账户级别 | 首次遇到时问 | 已知，跳过；`"none"` 则跳过所有 Ahrefs 操作 |
| `tools.semrush` | Semrush 账户级别 | 首次遇到时问 | 已知，跳过；`"none"` 则跳过所有 Semrush 操作 |
| `tools.gkp` | 是否有 Google Ads 账户（GKP） | 首次遇到时问 | `false` 则使用 Ahrefs 免费工具替代 |
| `focus` | 本次分析重点 | **每次都问**（任务可变） | 每次都问 |

### 第三步：保存/更新档案

每次运行结束后（或新增信息后），立即写回档案文件，确保下次启动时信息完整。

档案格式（JSON）：

```json
{
  "url": "https://www.sunriselink.sg",
  "business": "MOM-licensed domestic helper agency in Singapore, helping families hire maids from Indonesia, Myanmar, Philippines",
  "region": "Singapore",
  "language": ["English", "Chinese"],
  "competitors": ["nationmaid.com.sg", "universal.com.sg", "firstmaid.com.sg"],
  "tools": {
    "gsc": true,
    "ahrefs": "none",
    "semrush": "free",
    "gkp": true
  },
  "updated": "2026-04-15 10:05:23"
}
```

`tools.ahrefs` 和 `tools.semrush` 的合法值：
- `"paid"` — 有付费账户，解锁全部功能
- `"free"` — 有免费账户，功能受限（见跳过规则）
- `"none"` — 无账户，跳过所有该工具操作

档案目录不存在时先创建：`mkdir -p ~/.claude/skills/keyword-research/data/`

### 工具跳过规则

**Ahrefs**：

| 账户级别 | Step 2B 行为 | Step 3 行为 |
|---------|------------|-----------|
| `"paid"` | 使用 Site Explorer + Content Gap（完整竞品差距分析） | 使用 Keywords Explorer |
| `"free"` | 跳过 Site Explorer/Content Gap；改用 `site:` 搜索推断竞品内容结构 | 使用 ahrefs.com/keyword-generator（无需登录） |
| `"none"` | 跳过所有 Ahrefs 操作，不打开任何 Ahrefs 页面 | 同左 |

**Semrush**：

| 账户级别 | Step 2B 行为 | Step 3 行为 |
|---------|------------|-----------|
| `"paid"` | 使用 Keyword Gap（完整竞品差距分析） | 使用 Keyword Magic Tool（无限查询） |
| `"free"` | 跳过 Keyword Gap；使用 Keyword Magic Tool（10 次/天限额，需注意用量） | 使用 Keyword Magic Tool（注意限额） |
| `"none"` | 跳过所有 Semrush 操作，不打开任何 Semrush 页面 | 同左 |

**其他工具**：
- `tools.gkp: false` → Step 3D 改用 Ahrefs 免费关键词工具，不再引导注册 Ads 账户
- `tools.gsc: false` → Step 1 直接跳过，从 Step 2 开始

用户可随时说"我现在升级 Ahrefs 到付费了"，将 `tools.ahrefs` 从 `"free"` 更新为 `"paid"` 并保存。

---

## 四步分析框架

### Step 1 — 现有词基线（GSC）

**目的**：找"已有曝光但未充分利用"的词，是最快见效的优化机会。

**操作（通过 CDP 自动化）**：

直接导航到以下 URL（替换域名），一步激活28天窗口 + Position 列：

```
https://search.google.com/search-console/performance/search-analytics?resource_id=sc-domain%3A{domain}&num_of_days=28&metrics=CLICKS%2CIMPRESSIONS%2CCTR%2CPOSITION
```

> ⚠️ **必须用28天，不要用默认3个月**：如果网站 SEO 优化时间不长，3个月均值包含大量优化前的低排名数据，结论会严重失真（实测差4倍）。

然后将 rows-per-page 改为500，一次性提取全部数据（操作步骤见 `references/gsc-operations.md`）。

**重点筛选**：
- 曝光 ≥ 30 且点击率 0% → **Meta 修复机会**（排名已好但无人点击，标题/描述有问题）
- 平均排名 11–20 → **首页冲刺机会**（最近页面1的词）
- 曝光 ≥ 30 且排名 > 20 → **需要新建页面/加深内容**
- GSC 中完全不出现的词 → 完全未覆盖，见 Step 2

**遇到登录墙**：告知用户需要在 Chrome 中以已验证所有权的账户登录 GSC，完成后继续。若用户无 GSC 数据（新网站），直接跳 Step 2。

**注意**：GSC 只显示网站已出现过的词。完全未覆盖的词在这里看不到，需要 Step 2–3 发现。

**CDP 操作详细指南**：见 `references/gsc-operations.md`。

---

### Step 2 — 竞品词差距分析

**目的**：找竞品排名但目标网站完全缺席的词——这是最高价值的未覆盖机会。

**A. 确定竞品**（若用户未提供）：
- 在 Google 搜索目标网站的 2–3 个核心业务词
- 记录 SERP 有机结果前 5 的域名 → 这些是真实 SEO 竞品（而非用户主观认为的竞品）

**B. 竞品分析（付费工具 Ahrefs/Semrush）**：
- Ahrefs: https://ahrefs.com/site-explorer → 输入竞品域名 → Organic Keywords
- Semrush: https://www.semrush.com/analytics/organic/overview → 输入竞品域名
- 使用 Content Gap / Keyword Gap 功能：输入竞品 vs 目标网站 → 得到竞品有、目标网站没有的词

**B. 竞品分析（免费替代方案）**：
- Ahrefs 免费关键词工具：https://ahrefs.com/keyword-generator（无需注册，每次 100 个词）
- Semrush 免费账户：https://www.semrush.com/features/keyword-magic-tool/（10 次/天）
- 对每个竞品域名搜索 `site:competitor.com` → 了解其内容结构和主题覆盖
- Google 搜索竞品品牌词，观察其 SERP 结果标题和描述 → 推断其关键词策略

**遇到付费墙**：询问用户是否有账户；若无，使用免费方案，或引导用户截图/复制数据后分享。

---

### Step 3 — 关键词扩展与发现

**目的**：从种子词出发，系统挖掘长尾词、问题型词、本地化词。

**A. Google 自动补全**（完全免费，最真实的用户搜索习惯）：
- 对每个核心词，在 Google 搜索框输入以下变体并记录所有下拉建议：
  - `[词]`、`[词] how`、`[词] what`、`[词] why`
  - `best [词]`、`[词] vs`、`[词] cost`、`[词] near me`
  - 词中间加空格触发中段填词：`maid _ singapore`

**B. AnswerThePublic（免费 3 次/天）**：
- 访问 https://answerthepublic.com
- 输入核心词，选择目标语言/地区
- 导出 Questions 和 Prepositions 类词 → 直接用于 FAQ 和博客选题

**C. AlsoAsked（免费 3 次）**：
- 访问 https://alsoasked.com
- 输入核心词 → 获取 Google PAA 问题树状图
- PAA 词是 SERP 特征机会（出现在 People Also Ask box）

**D. Google Keyword Planner（免费，需 Google Ads 账户）**：
- 访问 https://ads.google.com/home/tools/keyword-planner/
- "发现新关键词" → 输入种子词或目标网站 URL
- 筛选目标地区，查看搜索量区间和竞争度
- 注意：未投放广告时搜索量显示宽泛区间（如 1K–10K），登录有投放记录的账户后更精确

**遇到 Google Ads 登录要求**：Ads 账户免费创建，无需实际投放广告。引导用户注册后再使用。

---

### Step 4 — 意图分类与优先级排序

对收集的词按用户意图分类，结合难度和转化潜力排定优先级。

**意图分类**（每个词必须归类）：

| 代码 | 意图类型 | 典型特征词 | 内容策略 |
|-----|---------|----------|---------|
| I | 信息型 | how/what/why/guide/tips | 博客文章、FAQ |
| C | 商业调研型 | best/vs/compare/review/top | 对比页、列表文章 |
| T | 交易型 | buy/hire/contact/book/price | 服务页、落地页 |
| N | 导航型 | 品牌词、网站名 | 品牌保护、主页 |

**优先级评分**：

```
优先级得分 = 意图契合度(1–5) × 转化潜力(1–5) ÷ 竞争难度(1–5)
```

- **意图契合度**：词的搜索意图是否与网站业务完全吻合（完全吻合=5，勉强相关=1）
- **转化潜力**：T=5，C=4，I=2–3，N=1
- **竞争难度（KD）**：0–20=1，21–40=2，41–60=3，61–80=4，81–100=5

优先执行：得分 ≥ 10 的词。

详细方法论和理论来源见 `references/methodology.md`。
工具完整使用手册见 `references/tools.md`。
GSC 网页 CDP 操作指南（含常见陷阱）见 `references/gsc-operations.md`。

---

## 输出格式

```markdown
## 关键词分析报告 — [网站名]
分析日期：YYYY-MM-DD | 目标地区：XX | 目标语言：XX

### 一、GSC 现有词优化机会
| 关键词 | 当前排名 | 曝光量 | 点击率 | 优化建议 |
|--------|---------|-------|-------|---------|

### 二、竞品差距词（最高价值）
| 关键词 | 意图 | 月搜索量 | KD | 竞品有 / 我方缺 | 优先级得分 |
|--------|-----|---------|-----|--------------|---------|

### 三、未覆盖长尾词
| 关键词 | 意图 | 月搜索量 | KD | 对应内容建议 |
|--------|-----|---------|-----|-----------|

### 四、优先执行清单（Top 10）
按优先级得分降序，每条附：建议动作（新建页面/优化现有页/写博客）+ 目标 URL 建议
```
