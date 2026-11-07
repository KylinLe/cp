# 普陀中学摄影社 · 上课平台

> 本文件是项目维护文档，面向**接手维护的人或 AI**（包括未来的 Claude / 其他模型），也供项目主人查阅。项目主人不是程序员，与他沟通请用通俗语言、先确认方案再动手。

线上地址：`https://kylinle.github.io/cp/`
仓库：`github.com/KylinLe/cp`（公开，main 分支）

## 项目背景与目标

普陀中学摄影社的上课平台，需求由社团主人（非程序员）分多次提出，要点如下：

- 访问需输入密码 `139572`，通过后从 5 个讲解师账号（`hw` / `lxn` / `lzy` / `swh` / `yyz`）中选择进入，查看对应讲解师的课件
- 课件列表**自动识别**：把课件放进讲解师文件夹并上传 GitHub，列表自动出现，无需改任何代码
- HTML 课件点击**播放**（新标签页）；PPT / PPTX / PDF / DOC / DOCX 课件点击**下载**（原需求只有 PPT，后来补了 PDF/DOC/DOCX）
- 外观延续社团主页 aaa.html 的黑白细线极简风格；社团英文名 **Photography Club of Putuo High School**；品牌图 `logo.png`
- 密码每次浏览器会话只输一次；直接打开讲解师空间页会跳回密码门，输完自动回跳
- 主页快捷入口：「随机抽人」→ `sjcr.html`（课堂抽签工具，原名 sjcq.html）、「摄影社官网」→ 外链 `https://kylinle.github.io/pcpths`（新标签页）；密码页也有官网入口

**术语约定（2026-11-07 起）**：主人将「教室」改称「讲解师」，界面与文档统一用「讲解师」，英文用 Lecturer / Lecturers。请勿再写「教室」。

**历史沿革**：项目最初部署在 `KylinLe/classroom`（kylinle.github.io/classroom），2026-11-07 迁移到 `KylinLe/cp` 并上线。详见「更新日志」。

## 当前状态与待办（⚠️ 接手先看这里，完成后请更新本小节）

截至 2026-11-07 核对：

- [x] **迁移到新仓库 `KylinLe/cp` 完成，网站已上线**（2026-11-07）：全站验证通过（密码门、讲解师页、课件、清单、工作流全部正常）
- [x] **本地已初始化为 git 仓库**并连接 `KylinLe/cp`；本机已安装 GitHub CLI（`~/bin/gh`，已登录 KylinLe 账号，含 workflow 权限）。维护者可直接 `git push` 发布更新，不再依赖主人的同步工具
- [x] **旧英文名已全部改完**（10 个 HTML 共替换 28 处）；test.html、`hw/lzy/swh/yyz` 的 index.html、`swh/早读(3)(2).pptx` 已按主人要求删除——新仓库从干净的本地状态开始
- [ ] **旧仓库 `classroom` 待主人处理**：由主人删除或归档旧仓库（或在旧仓库 Settings 关闭 Pages）。当前只有 lxn 有课件，其余四个讲解师空间显示「暂无课件」属正常

## 技术约束（不可违反）

- **纯静态站点**：只用 HTML、JS、CSS，无后端、无构建步骤，GitHub Pages 托管
- **GitHub Actions 是唯一例外**：`.github/workflows/update-filelist.yml` 是部署侧的自动小助手（每次推送后生成课件清单 files.json），不参与网站运行，网站本身仍是纯静态
- **仓库必须保持公开**：免费 Pages 的前提；主通道匿名接口在私有仓库下会 401
- 本地无任何依赖：直接双击 `index.html` 即可预览（课件列表读线上 GitHub，本地同样能测）

## 站点地图（页面与流程）

```
index.html（入口页，含两个视图）
├─ 密码门 gate-view ── 输入 139572 ──┐
│   └─ 底部入口：摄影社官网（外链新标签页）
└─ 选择讲解师 select-view（解锁后显示）
    ├─ 快捷按钮：随机抽人 → sjcr.html；摄影社官网 → 外链
    └─ 5 个讲解师入口 ──→ space.html?t=<账号>（教师空间页）
                          ├─ 导航：返回首页 / 切换账号
                          ├─ 课件列表（三通道加载，数量旁标注数据源）
                          └─ html 新标签页播放；其余格式下载

sjcr.html  随机抽人工具（独立页面，内联样式，不进讲解师空间流程）
aaa.html   社团主页原稿（设计参考，不在导航流程中）
```

## 文件结构

```
classplatform/
├── index.html          # 入口页：密码门 + 讲解师选择 + 快捷按钮
├── space.html          # 讲解师空间页：space.html?t=<账号>
├── css/style.css       # 设计系统（共享样式）
├── js/app.js           # 共享脚本：CONFIG 配置 + 密码校验 + 三通道文件列表
├── logo.png            # 社团 Logo（2000×2000，网页中缩放显示）
├── files.json          # 课件清单（Actions 自动生成，勿手动修改）
├── .github/workflows/  # Actions 工作流（隐藏文件夹）
├── .gitignore          # 忽略 .DS_Store 等
├── README.md           # 本文件：维护总文档
├── CLAUDE.md           # Claude Code 会话自动加载的速览入口
├── 测试清单.md          # 给主人的检查手册（部署 / 加课件后照着查）
├── sjcr.html           # 随机抽人工具页（独立页面，原名 sjcq.html）
├── aaa.html            # 社团主页原稿（设计参考，不属于平台流程）
├── hw/ lxn/ lzy/ swh/ yyz/   # 讲解师课件文件夹（只放顶层，不支持子文件夹）
└── .git/               # git 仓库元数据（origin = KylinLe/cp；空文件夹不进入版本控制）
```

## 核心机制：三通道文件列表

```
space.html?t=hw
   │
   ├─ ① 会话缓存 sessionStorage ─────── 5 分钟内直接命中，不发请求
   ├─ ② GitHub Contents API ─────────── 即时；个别网络环境连不上会降级
   ├─ ③ files.json 清单（与网站同源）── 推送后 1-2 分钟出现，最稳定 ★
   ├─ ④ jsDelivr CDN 接口 ───────────── 最后兜底；有 CDN 缓存，新课件会延迟
   ├─ ⑤ 旧缓存兜底 → 都不行才报错
   └─ 课件数量旁标注数据源：GitHub / 文件清单 / 备用通道 / 缓存
```

- ②主通道失败后本会话会记住（`cp_api_down`），后续直接走③，避免每次等待连接超时；点「刷新」会重新尝试②
- ③files.json 由 Actions 在**每次 push 到 main 后自动生成**：扫描所有顶层文件夹，把支持格式的课件名和大小写入清单，commit 回仓库（提交信息带 `[skip ci]` 防止循环触发）
- 代码路径：`js/app.js` 的 `fetchTeacherFiles`（链）→ `fetchFromGitHub` / `fetchFromManifest` / `fetchFromJsdelivr`

**sessionStorage 键一览**（均按浏览器会话隔离，关浏览器即清）：

| 键 | 用途 |
|---|---|
| `cp_unlocked` | 本会话已通过密码（值为 `'1'`），刷新/切换讲解师不再询问 |
| `cp_files_<teacher>` | 该讲解师列表缓存（`{ts, files}`），5 分钟内直接命中 |
| `cp_api_down` | GitHub 接口本会话不可用标记（值为 `'1'`），避免反复超时等待 |

支持格式与行为：

| 扩展名 | 图标 | 点击行为 |
|---|---|---|
| html | HTML | 新标签页直接播放 |
| ppt / pptx | PPT / PPTX | 直接下载 |
| pdf | PDF | 直接下载 |
| doc / docx | DOC / DOCX | 直接下载 |

下载走本站相对路径（如 `hw/课件.pptx`）并带 `download` 属性，中文文件名正常保留。

## 访问控制

1. 打开 `index.html` → 输入密码 `139572`（代码只存 SHA-256 哈希，无明文）。
2. 通过后进入「选择讲解师」，点账号进入 `space.html?t=<账号>`。
3. 解锁状态存 `sessionStorage`（键 `cp_unlocked`）：同会话内刷新、切换讲解师不用重输；直接开 `space.html` 未解锁会跳回密码门（带 `next` 参数），输完后自动跳回原空间；新会话重新问。
4. **改密码**：终端 `printf '新密码' | shasum -a 256`，把输出替换 `CONFIG.passwordHash`，推送。
5. 定位说明：纯前端校验是「挡路人」级别，挡不住懂技术的人；仓库公开时课件在 GitHub 上本身可见。

## 配置一览（维护只改 `js/app.js` 顶部 CONFIG）

| 字段 | 说明 |
|---|---|
| `repo` / `branch` | 仓库 owner/name 与分支。当前 `KylinLe/cp` + main；换仓库改这里，换分支还要同步改工作流里的 `branches: [main]` 和 `git push origin HEAD:main` |
| `passwordHash` | 密码 SHA-256 哈希（改法见上） |
| `teachers` | 讲解师账号数组（`hw/lxn/lzy/swh/yyz`），与仓库中同名文件夹一一对应 |
| `fileTypes` | 支持格式 + 图标 + 播放/下载行为 |
| `apiCacheMs` | 列表缓存时长（默认 5 分钟） |
| `unlockKey` | 会话解锁标记的键名（`cp_unlocked`） |

## 部署与发布

**发布链路**：本机 git 仓库 → `git push` → GitHub main → Actions 自动刷新 files.json → Pages 自动构建（1-2 分钟生效）。

- **本机 GitHub CLI**：`~/bin/gh`（官方 zip 手动安装，本机无 Homebrew），已登录 KylinLe 账号（OAuth 含 workflow 权限，2026-11-07 经设备码流程授权）。git 的凭据由它提供（`gh auth setup-git` 已配置）。
- **本地 git 仓库**：classplatform 文件夹就是工作区（origin = KylinLe/cp，main 分支）。
- **发布（由维护者/AI 执行）**：
  ```
  git add -A
  git commit -m "说明这次改了什么"
  git push
  ```
  主人自行放进文件夹的课件会以未提交状态出现，`git add -A` 会一并包含，无需区分。
- **网络注意**：本机访问 `github.com` 会**间歇性**连接失败（TCP 超时约 75 秒，非配置问题，重试即可）；`api.github.com` 相对稳定。推送失败直接重试。
- **Pages 设置**：仓库 Settings → Pages → main 分支根目录（已配好，勿动）。
- 首次部署（2026-11-07）即由 AI 按上述方式完成：初始化 git、合并远程初始 README、推送 15 个文件、开启 Pages、验证上线。

## 已知环境事实（直接影响维护判断，接手必读）

1. **主人的浏览器访问 `api.github.com` 不可靠**（浏览器侧失败、终端 curl 正常，疑似系统代理拦截浏览器流量）。实际主力通道是 files.json 清单。诊断时不要默认「GitHub 接口可达」。
2. **终端侧（curl / git / gh）访问 GitHub 基本可用，但 `github.com` 间歇性抽风**（75 秒超时，重试即好；2026-11-07 部署当天多次遇到）。`api.github.com` 稳定。推送失败属网络抖动时直接重试，别急着改代码。
3. **主人的同步工具会跳过隐藏文件/文件夹**（历史事实，旧仓库因此漏传过 `.github`）。当前仓库的工作流已通过 git 推送，无此问题；核对远程时仍建议先确认 `.github/workflows` 存在。
4. **GitHub 匿名接口 60 次/小时/IP**，校园网全校共享；触顶会自动降级不影响使用，但别把它误判为故障。
5. 主人不是程序员：需求可能模糊或有专业性错误，先以产品经理思维理解意图。

## 日常维护

- **加课件**：放文件到讲解师文件夹 → `git add -A && git commit && git push` → 完成。Actions 自动刷新 files.json（约 1-2 分钟），网页点「刷新」即出现。
- **加讲解师**：① 仓库根目录新建同名文件夹 ② `CONFIG.teachers` 加账号 ③ 推送。入口页自动多一个入口，清单自动包含（空文件夹在 git 中不存在，至少放一个课件）。
- **换仓库**：改 `CONFIG.repo`，并在 GitHub 上把 Pages 指向新仓库。
- **更新代码后**：浏览器强制刷新（`Cmd + Shift + R`）；GitHub Pages 生效等 1-2 分钟。
- **不要动**：`.github/workflows/`、自动生成的 `files.json`、讲解师文件夹名（须与 `CONFIG.teachers` 一致）。

## 验证与测试（改代码后必做）

- **逻辑测试**（node + vm，无需浏览器）：
  - 语法：`node --check js/app.js`
  - 把 app.js 读进 vm 沙箱（stub 掉 `fetch` / `sessionStorage` / `document`）后直测顶层函数：`sha256Hex`（用标准向量 abc / 空串校验）、`fetchTeacherFiles`（模拟 403 降级、清单 404、全通道失败、缓存命中等场景）。临时测试文件用完即删
- **浏览器实测**（headless Chrome）：
  - 页面流程：`--headless=new --virtual-time-budget=9000 --dump-dom <url>`；在测试页注入脚本把结果写进 `document.title`，再 grep 标题
  - 解锁技巧：复制 space.html 为临时页，在 app.js 脚本前注入 `sessionStorage.setItem("cp_unlocked","1")`；或临时页 `location.replace('index.html')` 后 dump
- **接口核对**：`curl api.github.com/repos/KylinLe/cp/contents/<folder>`（看 `x-ratelimit-remaining` 头）、`curl data.jsdelivr.com/v1/package/gh/KylinLe/cp@main/flat`
- 临时文件统一命名 `_test_*`，测完删除；本地预览 `python3 -m http.server 8000`
- 线上验证：curl 各关键路径（首页 / space.html / files.json / 课件文件）看 HTTP 200

## 故障速查

| 现象 | 原因 / 处理 |
|---|---|
| 新课件不出现 | 看数据源标注：`文件清单` → 等 1-2 分钟再刷新；`备用通道` → CDN 延迟；`缓存` → 点「刷新」 |
| 数据源一直是「备用通道」 | 清单通道不可用：确认 `.github/workflows` 存在、files.json 存在 |
| 「次数超过限制」 | 匿名接口触顶，自动降级，等恢复即可 |
| 「无法连接课件文件接口」 | 网络问题；若列表仍正常显示（清单接管）则无需处理 |
| git push 失败（75 秒超时） | 网络间歇抽风，直接重试 |
| 更新代码后页面没变化 | 强制刷新浏览器（Cmd + Shift + R）；Pages 生效有 1-2 分钟延迟 |
| 更详细的手册 | 见 `测试清单.md`（给主人的检查手册） |

## 设计系统

延续 `aaa.html` 的视觉语言：配色 `--ink:#0f0f0f` / `--paper:#ffffff` / `--muted:#6e6e73` / `--faint:#a1a1a6` / `--line:#e3e3e5`（细线）；1px 细线分隔、大字号标题、宽字距、黑白极简、缓动 `cubic-bezier(.22,.61,.24,1)`；品牌 `logo.png`；favicon 为内联 SVG 六边形光孔；列表行 hover 右移、滚动显现动画、移动端适配、`prefers-reduced-motion` 降级。

## 已知限制

- 密码门是「挡路人」级别，不是安全机制；仓库公开时课件在 GitHub 上本身可见
- 讲解师文件夹只支持顶层文件，不支持子文件夹；不支持的格式自动忽略
- 空文件夹在 git 中不存在：新讲解师文件夹至少放一个课件后才会进入清单
- 清单通道依赖 Actions：工作流缺失时无自动更新，退化为主/备用通道（新课件可能出现延迟）

## 更新日志

| 日期 | 变更 |
|---|---|
| 2026-11-07 | 迁移至 `KylinLe/cp` 并上线（kylinle.github.io/cp）；安装 gh CLI、本地初始化 git 仓库，改为直接 git push 发布；主页新增「随机抽人」「摄影社官网」按钮、密码页新增官网入口；sjcq.html 改名 sjcr.html；修复全部课件旧英文名（28 处）；删除 test.html、讲解师文件夹内 index.html 副本、17MB PPTX；「教室」统一改「讲解师」（Lecturer/Lecturers）；文档详细化 |
| 2026-10-31 | README 重写 + 新增 CLAUDE.md（面向维护者的文档体系）；社团英文名更新为 Photography Club of Putuo High School |
| 2026-10-29 | 旧仓库 classroom 的自动清单工作流上传并首次跑通（files.json 自动生成） |
| 2026-10-26 | 初版上线旧地址：密码门 + 讲解师选择 + 三通道课件列表 + aaa 风格设计系统 |

## 与项目主人协作注意点

- 主人不是程序员，需求可能模糊、不准确、甚至包含专业性错误：先理解意图再动手，方案用通俗语言讲清，做的假设要明说
- 重要改动先给方案、确认后再动手
- 所有修改只发生在 classplatform 项目文件夹内（主人明确要求；装 gh CLI 等系统级操作需先征得同意）
- 术语用「讲解师」；主人表达偏好会随时间变化，以此文件 + 会话记忆为准
- 本文件与 CLAUDE.md 是未来维护者的上下文来源，架构或状态有变化时请同步更新并 git push
