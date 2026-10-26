# 碳名局 · Carbon Name Lab

一个无需构建、无需外部依赖的静态 IUPAC 命名练习网站。每轮随机抽出一个碳数小于 10 的链状或环状有机物，可以「看结构选英文 IUPAC 名」，也可以「看英文名选结构」，每次 4 选 1 或 5 选 1。

线上地址：<https://diqie6.github.io/>

本地预览：<http://127.0.0.1:8787/>

![碳名局预览](assets/preview-desktop.png)

## 功能

- 随机题库：279 个精选结构，全部为 C1–C9，包含链状、环状与芳香环分子。
- 双向模式：Structure → Name 和 Name → Structure；侧栏 QUIZ MODE 随时切换，两种模式共用计分。
- 4 选 1 / 5 选 1：右上角随时切换，立即抽取下一题；反向模式生成 4/5 个真实结构骨架。
- 一键分享：点击 Share 复制当前题目的挑战链接（手机优先调用系统分享面板，桌面复制链接）；链接会带回题目、模式和选项数量，对方打开即是同一道题。链接使用当前访问域名，所以本地 `127.0.0.1` 预览链接只在本机有效；部署到公网或局域网地址后即可直接发送。
- 复杂取代基：新增了多卤代、卤代胺、卤代氨基酸、卤代酰胺/腈/醚、N-甲基和环状胺等题目，仍严格保持少于 10 个碳。
- 题型覆盖面：烷烃、烯烃、炔烃、醇、醛、酮、羧酸、酯、醚、胺、卤代烃、酰胺、腈、硫醇、酰卤、环烷烃/环烯烃/环醇/环酮、环胺、芳香烃/芳香酸，以及多官能团优先级题。
- 链 / 环筛选：侧栏可切换 Everything、Chains only、Rings only。
- 作答反馈：正确/错误着色、正确答案、该题对应的命名规则；答对触发轻量彩带。
- 计分：正确数、作答数、正确率、当前连续答对、最佳连续答对。
- 键盘操作：`A`–`E` 选择选项，答题后按 `Enter` 下一题。
- 命名规则面板：按你提供的 CHEM1006 reading assignment 对齐，包含完整官能团优先级表、最低位次规则和 alphabetization 规则。
- 题库中收录了 assignment 的结构题，例如 2-chloro-3-methylpentane、3,4-dimethylhexane、3-hydroxybutanoic acid、2-methylpropan-1-amine、3-ethyl-2-methylpentane、2-chloroethan-1-ol、but-2-en-1-ol、N-methylethanamide、3-hydroxybutanal，以及 Part A #10 的 3-chloro-4-methylbenzoic acid。
- 响应式布局：桌面双栏、平板和 320px 以上手机宽度均可用。

## 线上部署

已推送到 GitHub Pages：<https://github.com/DIQIE6/diqie6.github.io>，访问地址 <https://diqie6.github.io/>。

更新线上版本时，把 `index.html`、`styles.css`、`js/`、`assets/`、`README.md` 和 `.nojekyll` 同步到 `D:\DeepseekHarness\diqie6-deploy`，然后在该目录提交并 `git push origin main`。

## 本地运行

只需要一个静态文件服务器：

```powershell
cd D:\DeepseekHarness\iupac-quest
python -m http.server 8787 --bind 127.0.0.1
```

然后打开 <http://127.0.0.1:8787/>。也可以使用任何等价的静态服务器（例如 VS Code Live Server）；不要直接双击 `index.html`，因为浏览器会拦截 ES module 的本地加载。

## 化学正确性

- 题库中的 279 个结构均经过 SMILES 解析、价键检查、分子式检查和碳数检查。
- 测试使用精确图同构算法检查重复结构，避免把同一个结构的两个名称同时放进正确/干扰选项。
- 干扰选项来自题库中其他真实分子的 IUPAC 名称；它们与正确结构不同，不会出现“两个正确答案”。
- 只考察构造命名（constitutional nomenclature），不考察 E/Z 或 R/S；涉及 `but-2-en-1-ol` 等结构时，名称不指定立体化学。
- 命名规则与 2026 Fall CHEM1006《IUPAC Nomenclature of Organic Compounds》reading assignment 的优先级顺序和 alphabetization 规则一致。

## 验证

```powershell
node tests/logic.test.mjs
```

该测试会检查：

- 279 条记录、名称/id 唯一、全部 C1–C9；
- 每个原子不超价、分子式可计算；
- 同分子式内没有重复图结构；
- 全部 279 个结构 × 4/5 选项均能生成唯一选项，且只有一个正确答案；反向模式的结构选项也来自同一真实题库。

浏览器交互测试见 `tests/lead-qa.mjs`、`tests/qa-reverse.mjs`、`tests/qa-share.mjs`（需要本机 Edge 和 `D:\DeepseekHarness\diffeq-lab\node_modules\puppeteer-core`，仅为开发环境使用）。结构总览页：<http://127.0.0.1:8787/tests/gallery.html>。完整审核记录与最终哈希见 [`tests/final-verification.md`](tests/final-verification.md)。

## 文件结构

```text
iupac-quest/
├─ index.html              页面骨架
├─ styles.css              编辑风视觉、响应式布局
├─ assets/
│  ├─ favicon.svg
│  └─ preview-desktop.png
├─ js/
│  ├─ app.js               随机出题、作答、计分、键盘、主题交互
│  ├─ chem.js              小型 SMILES 解析、分子式、2D 骨架布局
│  ├─ molecules.js         279 个精选分子与命名规则
│  ├─ quiz.js              抽题、真实分子干扰项、选项生成
│  └─ render.js            无外部依赖的 SVG 骨架式渲染
└─ tests/
   ├─ logic.test.mjs       化学与选项逻辑测试
   ├─ lead-qa.mjs          Puppeteer 结构→名称交互/响应式测试
   ├─ qa-reverse.mjs       Puppeteer 名称→结构、计分和响应式测试
   ├─ qa-share.mjs         Puppeteer 分享链接、剪贴板和深链测试
   └─ gallery.html         全部结构可视化总览
```

没有 `npm install`，没有构建步骤，也没有 CDN 依赖；离线也能运行。

---

## 线上地址

GitHub Pages：<https://diqie6.github.io/IUPAC/>

纯静态站点（`index.html` + `styles.css` + `js/` + `assets/`），无需构建。
更新线上版本：在本仓库提交并推送到 `main` 分支，Pages 会自动重新发布。