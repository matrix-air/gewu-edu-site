# 格物 · 产品前端网站（工作名，待定）

把 `analysis/ai-education-landscape/` 的 AI 教育战略（数物化 · 场景化 × 集中化）落成一个**可跑、可点、可交互**的产品落地页。不是 PPT、不是文档节选，是一页真能打开的单文件网站。

> **诚实声明（先读这条）**
> - 「格物」是**工作名 / 待定**，页面上已明确标注 **产品原型演示 · 非已上线商业服务**。
> - 页面里的数据分三类，判据不同：
>   - 竞品价格 / 渠道数字：来自 `analysis/ai-education-landscape/` 的联网调研与竞品官网实拍。
>   - 物理数值（打滑/共速、末速度）：**前端 Canvas 现场仿真算出来的真值**，不是写死的文案（见下「物理口径」）。
>   - 8 周验证计划 / 内测招募：**计划，不是已发生的事实**，页面按"计划"表述。
> - 竞品截图是**沙屏无头抓取的官网实拍**，用于"做到 / 没做透"的对照陈述，版权归各厂商；本站仅作教学性对比引用。

---

## 这是什么

面向高中理科（数学 / 物理 / 化学）学生、家长、老师的**场景化 × 集中化**学习产品落地页。

两条主轴（对应战略图 v2.1）：

| 主轴 | 页面锚点 | 一句话 |
|---|---|---|
| 主轴一 · 情境锚定律 | `#scene` | 教学顺序倒过来：**常识 → 交互 → 公式**，而不是"先公式后套题" |
| 主轴二 · 全域图式律 | `#domain` | 不做"搜一题讲一题"，**一个领域一次收齐**，建的是知识图式不是题海 |

页面结构（自上而下）：`hero`（含实时物理演示台）→ `#pain` 谁在痛 → `#scene` 主轴一 → `#domain` 主轴二 → `#vs` 竞品对照 → `#evidence` P0 8 周验证 → `#form` 学科类培训合规硬约束 → `#faq` → `#cta` 内测招募 → `footer`。

## 复用/对接的现有资产

| 资产 | 在本站的作用 |
|---|---|
| `analysis/ai-education-landscape/` | 页面全部主张与竞品口径的来源（战略图 v2.1、L1 竞品矩阵、P0 计划、合规定位） |
| `analysis/gaokao-physics-khanmigo-replica/` | 交互教学页骨架被复刻进本站 hero 演示台；`demo-khanmigo-replica.html` 是同源完整复刻页 |
| `analysis/gaokao-sci-knowledge-tree/` | 「一个领域一次收齐」的 12 集中域口径来自这套 65 章大纲 + 覆盖门 |

## 文件清单

```
ai-education-product-site/
├── index.html                   # 主交付：单文件站点，零依赖零构建
├── demo-khanmigo-replica.html   # 同源交互教学页（Khanmigo 形态复刻，独立可开）
├── assets/                      # 竞品官网实拍（每条 jpg 为页面用图，png 为原图留档）
│   ├── xueersi.jpg  / .png      # 学而思学习机（九章）
│   ├── iflytek.jpg  / .png      # 科大讯飞学习机
│   ├── nobook.jpg   / .png      # NOBOOK / 矩道 虚拟实验
│   ├── khanmigo.jpg / .png      # Khanmigo（Khan Academy）
│   └── squirrelai.jpg / .png    # 松鼠 AI（备选素材）
└── tools/
    └── render-shots.js          # 无头渲染 + 分屏截图 + 物理断言（验收用）
```

## 怎么跑

**推荐：本地静态服务（唯一要求是有 python3）**

```bash
cd ~/Documents/Aurora/analysis/ai-education-product-site
python3 -m http.server 8791
# 浏览器打开 http://127.0.0.1:8791/index.html
```

直接双击 `index.html` 也能开（`file://` 无跨域依赖，资产走相对路径）。

## 怎么复验（验收命令）

带断言的无头渲染：跑一次传送带仿真，验证前端算出的物理值与理论值一致，同时截全页 + 分屏片供视觉验收。

```bash
cd ~/Documents/Aurora/analysis/ai-education-product-site/tools
node render-shots.js http://127.0.0.1:8791/index.html /tmp/edu-site
```

输出 JSON 应满足：

- `errors`: `[]`（零控制台报错）
- `physics.mu03.verdict` ≈ 「共速了…加速 1.33 s、滑行 2.67 m」（理论 `t=1.333s, s=2.667m`，μ=0.30 v=4）
- `physics.mu01.verdict` ≈ 「没追上…末端速度 3.46 m/s」（理论 `vEnd=3.464 m/s`，μ=0.10 v=8，带长 6 m）
- `mobileHorizontalOverflowPx`: `0`（小屏无横向溢出）
- `canvasH` 与 `canvasHAfter` 均为 `496`（Canvas 高度不随仿真漂移）

结构门（SLOP 检查，0 error 才过）：

```bash
python3 ~/Documents/Aurora/tools/aesthetic_lint.py index.html
# 期望：合计: 0 error / 0 warn  →  PASS
```

## 物理口径（hero 演示台）

传送带-行李箱模型，a = μg（仅摩擦驱动，g=10）：

| 场景 | 摩擦 μ | 带速 v | 是否共速 | 加速时间 t = v/(μg) | 加速距离 s = v²/(2μg) |
|---|---|---|---|---|---|
| 默认 | 0.30 | 4 m/s | 是 | 1.333 s | 2.667 m |
| 打滑 | 0.10 | 8 m/s | 否（带长 6 m 追不上） | 8 s¹ | 32 m¹ |

¹ 在 6 m 带长内走不完，故改用末端速度 `vEnd = √(2μgL) = √(2×0.1×10×6) = 3.464 m/s` 表述。

数值由页面内的实时积分仿真产出，`render-shots.js` 用轮询等到仿真真跑完再读结论，不用固定等待——避免"截图早于仿真收敛"的假阴性。

## 已知坑位（改这张页面前必读）

1. **Canvas 高度反射 bug**：早期版本 canvas 的 `height` 属性被仿真每帧回写，导致页面高度膨胀到 1600 万像素。修法：把高度做成模块级常量 `CV_H`，只在初始化读一次。
2. **JS 字符串里的中文嵌套引号**：直接用全角「」以外的成对中文引号会截断字符串 → 语法错误。本页统一用「」。
3. **Playwright 截图不稳定**：1/60s 的 rAF 会让截图捕捉到中间帧。修法：`scrollIntoView` + 截图时 `animations:"disabled"`。
4. **写源文件必须走 Write/Edit**：本机 Mimosa 安全钩子会拦 Bash heredoc/重定向写文件，别用 `cat > file` 那套。
5. **视觉门缺席的降级**：本机 `documents:visual-judge` 子代理供应商不可用时，回退为人眼读截图（本页当前即此状态，已逐页复核）。

## 发布

公网镜像走 `matrix-air/*` GitHub Pages（SSH push，Pages 源 = main 根目录），与 `matrix-air/ai-edu-strategy` 同一配方。发布后以 HTTP 200 为准。
