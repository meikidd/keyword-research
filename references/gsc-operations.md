# GSC 网页操作指南（CDP 自动化）

> 适用场景：通过 CDP proxy（默认 localhost:3456）自动化操作 Google Search Console

---

## 一、正确的 GSC 起始 URL

**不要**从 GSC 首页导航，直接用带完整参数的 URL，一步到位解决时间窗口和列显示问题：

```
https://search.google.com/search-console/performance/search-analytics?resource_id=sc-domain%3A{domain}&num_of_days=28&metrics=CLICKS%2CIMPRESSIONS%2CCTR%2CPOSITION
```

将 `{domain}` 替换为目标域名，例如 `example.com`：

```
https://search.google.com/search-console/performance/search-analytics?resource_id=sc-domain%3Aexample.com&num_of_days=28&metrics=CLICKS%2CIMPRESSIONS%2CCTR%2CPOSITION
```

**URL 参数说明**：

| 参数 | 值 | 作用 |
|-----|---|-----|
| `resource_id` | `sc-domain%3A{domain}` | 指定 GSC 属性（域名属性用 `sc-domain:`，URL 属性直接用 URL） |
| `num_of_days` | `28` | 28天时间窗口（见下方说明） |
| `metrics` | `CLICKS%2CIMPRESSIONS%2CCTR%2CPOSITION` | 激活 Clicks + Impressions + CTR + Position 列（%2C = 逗号） |

> ⚠️ **关于时间窗口**：默认的3个月均值适合长期稳定的网站。如果目标网站 SEO 优化开始时间较短（不足3个月），3个月均值会包含大量优化前的低排名数据，导致结论严重失真。具体使用哪个时间窗口，参考各项目的 AGENTS.md 说明。

---

## 二、提取数据前的验证步骤

导航到 URL 后，**先验证再提取**，避免读到缓存旧数据：

### 2.1 验证表头（确认 Position 列已激活）

```javascript
(function(){
  var table = document.querySelectorAll("table")[1];
  var headers = table.querySelectorAll("thead th");
  var cols = [];
  for(var i=0; i<headers.length; i++) cols.push(headers[i].textContent.trim());
  return cols.join(" | ");
})()
```

预期输出：`Top queries | Clicks | Impressions | CTR | Position`

如果只有4列（缺 Position），说明 metrics 参数未生效，需手动点击 "Average position" 指标卡（见 2.3）。

### 2.2 验证时间窗口（确认是28天数据）

截图查看页面顶部的日期选择器，确认显示"28 days"或具体28天日期范围，而不是"3 months"。

```javascript
// 快速检查：第一行数据是否与预期吻合（可与上次已知数据对比）
(function(){
  var table = document.querySelectorAll("table")[1];
  var row1 = table.querySelector("tbody tr");
  var cells = row1 ? row1.querySelectorAll("td") : [];
  var data = [];
  for(var i=0; i<cells.length; i++) data.push(cells[i].textContent.trim());
  return data.join(" | ");
})()
```

### 2.3 手动激活 Position 列（URL 参数失效时的备用方案）

```javascript
// 找到 Average position 指标卡并点击（使其变橙色）
(function(){
  var cards = document.querySelectorAll("[data-metric], .nnLLaf, button");
  // 也可以通过坐标找到指标卡区域的元素
  var el = document.elementFromPoint(1060, 250);
  el.click();
  return "clicked: " + el.textContent.trim().substring(0,50);
})()
```

---

## 三、修改 Rows-per-page 为 500

默认每页10行，必须改为500才能一次性提取全部数据。这是**两步操作**：

### 第一步：找到 rows-per-page 下拉并点击触发

```javascript
// 找到当前显示的行数值（如"10"），点击触发下拉
(function(){
  var el = document.elementFromPoint(1291, 855);
  el.click();
  return "clicked: " + el.textContent.trim();
})()
```

> 注意：坐标 `1291, 855` 适用于 1615×963 视口。如视口不同需调整。
> 也可以搜索包含 "Rows per page" 文本的容器：

```javascript
(function(){
  var all = document.querySelectorAll("div");
  for(var i=0; i<all.length; i++){
    if(all[i].textContent.trim().startsWith("Rows per page")){
      var r = all[i].getBoundingClientRect();
      if(r.width > 0 && r.height > 0){
        all[i].click();
        return "clicked rows selector at x=" + Math.round(r.x) + " y=" + Math.round(r.y);
      }
    }
  }
  return "not found";
})()
```

### 第二步：在已展开的下拉中点击 "500" 选项

```javascript
// 找所有可见的、文本完全为"500"的元素并点击
(function(){
  var all = document.querySelectorAll("div, li, span");
  for(var i=0; i<all.length; i++){
    if(all[i].textContent.trim() === "500"){
      var r = all[i].getBoundingClientRect();
      if(r.width > 0 && r.height > 0){
        all[i].click();
        return "clicked 500 at x=" + Math.round(r.x) + " y=" + Math.round(r.y);
      }
    }
  }
  return "500 option not found";
})()
```

> ⚠️ 不要跳过第一步直接执行第二步——下拉未展开时 "500" 元素的 width=0，无法命中。

---

## 四、提取全部数据

确认 rows-per-page 已变为500后，提取全部行。

### 4.1 确认可见表是哪个

GSC 页面通常有两个 table：`table[0]` 是隐藏的旧数据（x=0,y=0），`table[1]` 是可见的当前数据。

```javascript
(function(){
  var tables = document.querySelectorAll("table");
  var info = [];
  for(var i=0; i<tables.length; i++){
    var r = tables[i].getBoundingClientRect();
    var rows = tables[i].querySelectorAll("tbody tr");
    info.push("table["+i+"]: x="+Math.round(r.x)+" y="+Math.round(r.y)+" rows="+rows.length);
  }
  return info.join("\n");
})()
```

预期：`table[1]` 可见（x>0，y>0），行数 = 该网站的总查询词数。

### 4.2 提取全部数据

```javascript
(function(){
  var table = document.querySelectorAll("table")[1];
  var rows = table.querySelectorAll("tbody tr");
  var data = [];
  for(var i=0; i<rows.length; i++){
    var cells = rows[i].querySelectorAll("td");
    if(cells.length >= 5){
      data.push(
        cells[0].textContent.trim() + "\t" +   // keyword
        cells[1].textContent.trim() + "\t" +   // clicks
        cells[2].textContent.trim() + "\t" +   // impressions
        cells[4].textContent.trim()            // position（index 4，不是 3！CTR 在 index 3）
      );
    }
  }
  return data.join("|||");
})()
```

> ⚠️ **列索引注意**：5列顺序为 `keyword(0) | clicks(1) | impressions(2) | CTR(3) | position(4)`。Position 是 index **4**，不是3。

用 Python 解析响应：

```python
import json, sys
resp = json.load(sys.stdin)
val = resp.get('value', '')
lines = val.split('|||')
print(f"Total: {len(lines)} rows")
for line in lines[:10]:
    print(line)
```

---

## 五、CDP 操作规范

### 5.1 按坐标点击

CDP proxy 的 `/click` 接口只接受 CSS 选择器，**不接受坐标**。按坐标点击必须用 `/eval`：

```bash
curl -s -X POST "http://localhost:3456/eval?target={tabId}" \
  -d 'document.elementFromPoint(X, Y).click()'
```

**不要这样做**（会报错）：
```bash
# 错误：/click 不接受坐标
curl -X POST "http://localhost:3456/click?target=..." -d '{"x": 100, "y": 200}'
```

### 5.2 复杂 JS 用 IIFE 包裹

避免变量污染和作用域问题：

```javascript
// 正确：用 IIFE 包裹
(function(){
  var result = [];
  // ... 你的代码
  return result.join("\n");
})()

// 避免：裸 var 声明在顶层 eval 偶发报错
var result = [];
```

### 5.3 避免箭头函数

部分 GSC 页面环境对箭头函数（`=>`）支持不稳定，用传统 `function` 关键字更安全。

### 5.4 切换过滤后验证数据已刷新

切换时间窗口或过滤条件后，**不要立即提取数据**——DOM 可能还是旧数据。先截图或用快速校验脚本确认数据已更新：

```bash
curl -s "http://localhost:3456/screenshot?target={tabId}&file=/tmp/verify.png"
```

---

## 六、常见陷阱速查

| 陷阱 | 现象 | 解决方法 |
|------|------|---------|
| Position 列缺失 | 表格只有4列（无排名） | URL 加 `metrics=CLICKS%2CIMPRESSIONS%2CCTR%2CPOSITION` |
| 提取到缓存旧数据 | 过滤切换后数据未变 | 截图确认页面已更新再提取 |
| Position 列索引错误 | 提取的是 CTR 而不是排名 | Position 是 index 4（不是3） |
| 下拉500选项点不到 | 返回 "500 option not found" | 先点击触发下拉，再找可见"500"元素 |
| `/click` 报错 | 传了坐标给 `/click` 接口 | 改用 `/eval` + `document.elementFromPoint` |
