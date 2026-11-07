# CLAUDE.md — 项目速览（会话自动加载入口）

普陀中学摄影社上课平台：纯静态（HTML/JS/CSS），GitHub Pages 托管。
线上 `https://kylinle.github.io/cp/`；仓库 `KylinLe/cp`（公开，main 分支）。

## 与项目主人的协作约定（最高优先）

- 主人不是程序员：先理解意图、方案确认后再动手、沟通用通俗语言、做的假设要明说
- 所有修改只发生在 classplatform 文件夹内（系统级操作如安装软件需先征得同意）
- md 文档要写到其他 AI 接手也看得懂
- **术语**：用「讲解师」（Lecturer/Lecturers），不要写「教室」（2026-11-07 起）

## 架构速记

- 入口 [index.html](index.html)（密码门：SHA-256 哈希校验 + 讲解师选择）；空间页 [space.html](space.html)（`?t=<账号>`）；[sjcr.html](sjcr.html) 随机抽人独立页
- 主页快捷按钮：随机抽人 → sjcr.html；摄影社官网 → 外链 `kylinle.github.io/pcpths`（新标签页）；密码页也有官网入口
- 课件列表**双源**：平台文件夹三通道降级（会话缓存(5min) → GitHub Contents API → **files.json 清单**（Actions 每次 push 后自动生成，最稳定）→ jsDelivr → 旧缓存/报错；数量旁标注数据源）+ **讲解师个人仓库** `cp-<账号>` 的 files.json（同源直接读，失败静默跳过）；合并后同名以平台文件夹为准，每行右侧标注「本平台 / 讲师仓库」
- 5 个讲解师个人仓库（KylinLe/cp-hw|lxn|lzy|swh|yyz）：根目录放课件 + 各自的工作流生成各自 files.json + 各自开 Pages(main 根目录)
- sessionStorage 键：`cp_unlocked`（已解锁）/ `cp_files_<teacher>`（列表缓存，含两源合并结果）/ `cp_api_down`（接口不可用记忆）
- 配置集中在 [js/app.js](js/app.js) 顶部 `CONFIG`（含 `teacherRepos` 映射与 `pagesBase`）；设计系统在 [css/style.css](css/style.css)（延续 aaa.html 黑白细线风格）
- html 课件新标签页播放；ppt/pptx/pdf/doc/docx 下载；两处来源都只识别顶层文件；讲解师 = `hw/lxn/lzy/swh/yyz`

## 常用任务（速查）

- **发布更新**：`git add -A && git commit -m "..." && git push`（本机已装 `~/bin/gh` 并登录，凭据自动可用；主人自行放入的课件用 `git add -A` 一并提交）
- **加课件**：小文件放进讲解师文件夹 → 推送本仓库；大文件（几十 MB 级）放进 `cp-<账号>` 仓库根目录 → 推送该仓库。各自工作流 1-2 分钟自动刷新清单
- **改密码**：`printf '新密码' | shasum -a 256` → 替换 `CONFIG.passwordHash` → 推送
- **加讲解师**：新建同名文件夹 + `CONFIG.teachers` 加账号 → 推送；要个人仓库的话：建 `cp-<账号>`（拷贝任一讲师仓库工作流 + 开 Pages）+ `CONFIG.teacherRepos` 加映射
- **测试**：`node --check js/app.js`；node + vm 沙箱测顶层函数；headless Chrome `--dump-dom` 测页面；临时文件 `_test_*` 用完即删。注意：**GitHub Pages（Jekyll）不发布下划线开头的文件**，需要推到线上做验证的临时页不要以下划线开头

## 当前状态（2026-11-07）

**项目已迁移到 `KylinLe/cp` 并上线**：`https://kylinle.github.io/cp/`（全站验证通过）。旧仓库 `classroom` 弃用，待主人删除或归档。

- [x] **讲解师个人课件仓库方案上线**：5 个仓库 cp-hw/lxn/lzy/swh/yyz 已建好（公开 + Pages 开启 + 各自清单工作流），平台已支持双源扫描与「本平台 / 讲师仓库」标注
- [ ] 旧仓库 classroom 由主人处理（删除 / 归档 / 关闭 Pages）

## 环境事实

- 主人浏览器访问 `api.github.com` 不可靠（疑似系统代理）；实际主力通道是 files.json 清单
- 终端侧 curl/git/gh 基本可用，但 `github.com` 会**间歇性**连接失败（约 75 秒超时）——推送失败直接重试，别改代码；`api.github.com` 稳定
- 主人不是程序员；历史上有过网页端上传与本地不一致的双向漂移（迁移后已消除），维护前先核对两边状态
- 匿名 GitHub API 只能读不能删；删除远程文件需主人网页端操作（或提供凭据）

完整文档：**[README.md](README.md)**（必读，含背景/站点地图/部署/更新日志）· [测试清单.md](测试清单.md)（给主人的检查手册）
