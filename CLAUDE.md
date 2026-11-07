# CLAUDE.md — 项目速览（会话自动加载入口）

普陀中学摄影社上课平台：纯静态（HTML/JS/CSS），GitHub Pages 托管。
线上 `https://kylinle.github.io/cp/`；仓库 `KylinLe/cp`（公开，main 分支）。

## 与项目主人的协作约定（最高优先）

- 主人不是程序员：先理解意图、方案确认后再动手、沟通用通俗语言、做的假设要明说
- 所有修改只发生在 classplatform 文件夹内
- md 文档要写到其他 AI 接手也看得懂

## 架构速记

- 入口 [index.html](index.html)（密码门：SHA-256 哈希校验 + 教师选择）；教室 [space.html](space.html)（`?t=<账号>`）
- 主页（选择讲解师页）快捷按钮：随机抽人 → [sjcr.html](sjcr.html)；摄影社官网 → 外链 `kylinle.github.io/pcpths`（新标签页）；密码页也有官网入口
- 课件列表三通道降级：会话缓存(5min) → GitHub Contents API → **files.json 清单**（Actions 每次 push 后自动生成，最稳定）→ jsDelivr → 旧缓存/报错；课件数量旁标注数据源
- 配置集中在 [js/app.js](js/app.js) 顶部 `CONFIG`；设计系统在 [css/style.css](css/style.css)（延续 aaa.html 黑白细线风格）
- html 课件新标签页播放；ppt/pptx/pdf/doc/docx 下载；只识别教师文件夹顶层文件

## 当前待办（2026-11-07，完成后更新）

**项目已迁移：`KylinLe/classroom` → `KylinLe/cp`（旧仓库弃用）。**

- [x] 代码文档已全部切换到 `cp`（CONFIG.repo、文档链接、files.json 重新生成）
- [ ] 主人操作：新建公开仓库 `cp` 并开 Pages → 推送本地全部文件 → 网页上传 `.github/workflows` → 验证 `kylinle.github.io/cp/`
- [ ] 旧仓库 classroom：新站验证通过后由主人删除或归档

## 环境事实

- 主人浏览器访问 `api.github.com` 不可靠（疑似系统代理）；实际主力通道是 files.json 清单
- 主人的同步工具跳过隐藏文件；远程与本地存在双向漂移，维护前先核对两边文件列表
- 匿名 GitHub API 只能读不能删；删除远程文件需主人网页端操作（或提供凭据）

## 验证方法

- `node --check js/app.js` 语法检查；node + vm 沙箱测 app.js 顶层函数（stub `fetch`/`sessionStorage`/`document`）
- headless Chrome `--dump-dom` 测页面流程；临时文件统一命名 `_test_*`，用完即删
- 本地预览 `python3 -m http.server 8000`

完整文档：**[README.md](README.md)**（必读）· [测试清单.md](测试清单.md)（给主人的检查手册）
