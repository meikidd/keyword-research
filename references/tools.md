# 关键词研究工具手册

## 目录
1. 免费工具
2. 付费工具
3. 工具组合推荐
4. 常见问题处理

---

## 1. 免费工具

### Google Search Console（GSC）
- **URL**: https://search.google.com/search-console/
- **需要**: 已验证网站所有权的 Google 账户
- **核心用途**: 查看网站已有曝光的词、点击率、排名位置
- **操作路径**: Performance → Search results → 设置日期范围 → 按 Query 排序
- **导出**: 右上角下载图标 → 下载 CSV
- **局限**: 只显示该网站已出现在 SERP 的词；完全未覆盖的词看不到

### Google Keyword Planner（GKP）
- **URL**: https://ads.google.com/home/tools/keyword-planner/
- **需要**: Google Ads 账户（免费注册，无需投放广告）
- **核心用途**: 获取搜索量区间、发现关联词、查看地区数据
- **操作路径**: 发现新关键词 → 输入种子词或网站 URL → 选择国家/语言
- **局限**: 搜索量显示为区间（1K–10K），非精确值；竞争度是广告竞争度而非 SEO 难度
- **登录问题**: 若未登录 Ads 账户，数据更模糊；引导用户登录其 Google Ads 账户

### Google Trends
- **URL**: https://trends.google.com/
- **需要**: 无（完全公开）
- **核心用途**: 对比两个词的相对热度、查看季节性趋势、地区分布
- **操作**: 输入词 → 选择地区和时间范围 → 可对比最多 5 个词
- **局限**: 无搜索量绝对值，只有相对指数

### Google 自动补全（Search Suggest）
- **URL**: https://www.google.com/（直接在搜索框操作）
- **需要**: 无
- **核心用途**: 挖掘真实用户搜索习惯、发现长尾变体
- **操作技巧**:
  - 输入词后加空格，触发后缀建议
  - 词前加字母触发前缀：`a [词]`、`b [词]`……`z [词]`
  - 词中间加下划线触发中段填词：`maid _ singapore`
  - 在无痕模式操作，避免个性化搜索历史影响建议

### AnswerThePublic
- **URL**: https://answerthepublic.com
- **需要**: 免费账户（3 次/天）
- **核心用途**: 生成问题型、介词型、对比型关键词的可视化图谱
- **操作**: 输入核心词 → 选择语言/国家 → 下载 CSV 或截图
- **最有价值的输出**: Questions（Who/What/When/Where/Why/How）→ 直接用于 FAQ 选题
- **超出免费次数**: 等 24 小时重置，或注册付费账户（$99/月）

### AlsoAsked
- **URL**: https://alsoasked.com
- **需要**: 免费（3 次/月，不是每天）
- **核心用途**: 抓取 Google People Also Ask 问题的完整树状展开图
- **操作**: 输入词 → 选择语言/国家 → 查看 PAA 树状图
- **输出用途**: 这些问题是 Google 认为用户想深入了解的相关问题，命中它们意味着 SERP 特征机会
- **超出次数**: 此工具免费次数很有限，优先用在最核心的种子词上

### Ahrefs 免费关键词工具
- **URL**: https://ahrefs.com/keyword-generator
- **需要**: 无（完全公开，无需注册）
- **核心用途**: 输入种子词，获取 100 个关联词 + KD 估分 + 月搜索量估算
- **局限**: 每次只显示 100 个词，无法导出，无竞品对比功能

### Semrush 免费账户
- **URL**: https://www.semrush.com/features/keyword-magic-tool/
- **需要**: 免费注册账户
- **限额**: 每天 10 次关键词查询
- **核心用途**: 关键词搜索量、KD、SERP 特征、竞品词对比
- **操作**: Keyword Magic Tool → 输入词 → 选择国家 → 筛选难度范围

### Bing Webmaster Tools
- **URL**: https://www.bing.com/webmasters/
- **需要**: Microsoft 账户 + 已验证网站
- **核心用途**: 查看 Bing 搜索词数据 + AI Copilot 引用关键词报告（Grounding Queries）
- **特殊价值**: Bing AI Performance 报告可查看内容被 Copilot 检索时使用的关键词，是 GEO/AEO 的独特信号

---

## 2. 付费工具

### Ahrefs
- **价格**: Lite $129/月，Standard $249/月
- **URL**: https://ahrefs.com
- **核心优势**: 外链数据库最权威；关键词数据库大；Site Explorer 竞品分析深度最强
- **关键功能**:
  - Site Explorer：输入竞品域名 → Organic Keywords → 完整排名词列表
  - Content Gap：输入竞品 vs 目标网站 → 得到竞品有、目标没有的词
  - Keywords Explorer：多维度关键词分析、SERP 历史、点击率预测
  - Rank Tracker：监控指定词的排名变化

### Semrush
- **价格**: Pro $139.95/月，Guru $249.95/月
- **URL**: https://www.semrush.com
- **核心优势**: 功能最全面（40+ 工具）；本地 SEO 模块强；广告关键词数据独特
- **关键功能**:
  - Keyword Magic Tool：最强大的关键词扩展工具
  - Keyword Gap：竞品差距分析
  - Position Tracking：排名追踪
  - Local SEO 工具：适合本地服务业

### Moz Pro
- **价格**: Starter $49/月，Standard $99/月
- **URL**: https://moz.com/pro
- **核心优势**: DA（Domain Authority）评分是行业标准；界面友好；适合入门
- **关键功能**: Keyword Explorer、Rank Tracker、On-Page Grader

### KWFinder（Mangools）
- **价格**: Basic $29/月（年付），Premium $44/月
- **URL**: https://kwfinder.com
- **核心优势**: 性价比高；专注关键词研究；界面简洁；适合小团队
- **关键功能**: 关键词难度评分清晰直观，SERP 分析，自动补全词挖掘

### Ubersuggest（Neil Patel）
- **价格**: Individual $29/月，Business $49/月
- **URL**: https://neilpatel.com/ubersuggest/
- **核心优势**: 价格最低；功能基础但够用；更新活跃
- **局限**: 数据准确性不如 Ahrefs/Semrush；竞品分析较浅

### Surfer SEO
- **价格**: Essential $99/月，Scale $219/月
- **URL**: https://surferseo.com
- **核心优势**: 关键词聚类功能（Keyword Surfer）；内容优化评分；Topic Map 建立内容架构
- **适用场景**: 需要系统规划内容聚类时首选；偏内容优化而非关键词发现

### LowFruits
- **价格**: $29.90/月（Pay-as-you-go 也可用）
- **URL**: https://lowfruits.io
- **核心优势**: 专门挖掘低竞争关键词（KD 低但有搜索量）；适合新网站找突破口
- **适用场景**: 预算有限、新站，需要快速找到可排名的词

---

## 3. 工具组合推荐

### 零预算方案
```
GSC（现有词） + GKP（验证搜索量） + Google 自动补全（长尾扩展）
+ AnswerThePublic（FAQ 词） + Ahrefs 免费工具（竞品词预览）
```
覆盖范围：完整，但竞品分析深度有限

### 小预算方案（$30–50/月）
```
GSC + KWFinder ($29/月) 或 Ubersuggest ($29/月)
```
覆盖范围：加上精确 KD 评分、竞品词基础分析

### 标准方案（$100–130/月）
```
GSC + Ahrefs Lite ($129/月)
```
覆盖范围：完整竞品分析、Content Gap、外链数据

### 全功能方案（$140+/月）
```
GSC + Semrush Pro ($139.95/月)
```
覆盖范围：最全面，含本地 SEO、广告词数据、所有竞品工具

---

## 4. 常见问题处理

### GSC 登录墙
- 用户需要在 Chrome 中已登录 Google 账户，且该账户已通过 GSC 的网站所有权验证
- 所有权验证方式：HTML 文件上传、DNS TXT 记录、Google Analytics 关联（最常见）
- 引导语："请在你的 Chrome 中打开 search.google.com/search-console，确认能看到你的网站数据后告诉我"

### Google Ads 账户要求
- GKP 需要 Google Ads 账户，但不需要投放广告
- 注册 Ads 账户时会要求填写账单信息，引导用户跳过或填写后不投放广告
- 替代方案：使用 Ahrefs 免费工具或 Semrush 免费账户

### 工具限额用尽（AnswerThePublic / AlsoAsked）
- AnswerThePublic 免费版 3 次/天：告知用户明天继续，或改用 Google 自动补全手动挖掘
- AlsoAsked 3 次/月：优先用在核心种子词，次要词改用 PAA 手动查看（直接 Google 搜索观察）

### 付费工具无账户
1. 先用免费工具完成基础分析
2. 列出"若有付费工具可进一步确认的数据点"，告知用户
3. 建议用户注册免费试用（Ahrefs 7 天试用、Semrush 7 天试用）

### 数据在工具间不一致
- 这是正常现象，各工具使用不同的数据源和算法
- 原则：搜索量用 GKP 数据作参考基准（来自 Google 本身），KD 和竞品词用 Ahrefs/Semrush
- 不要过度纠结精确数字，关键是相对排序（哪些词相比之下更有机会）

### SERP 被个性化搜索污染
- 用无痕模式（Incognito）进行 Google 搜索，避免历史记录和登录状态影响 SERP 结果
- 若需要查看特定地区 SERP，使用 `&gl=sg&hl=en` 等 URL 参数或使用 VPN
