# AFTER SCHOOL · 深蓝时刻

一个个人博客站点，深蓝配青色的 P3 风格主题。仓库里有两种互不影响的运行方式：

- **自托管版**（Express 服务端渲染）：可以登录后台在线写作，文章存在 SQLite 里。
- **静态版**（Vercel）：`npm run build` 生成纯静态页面，只读发布，不需要数据库和密码。

## 技术栈

Node.js 24 + Express 5 + EJS 做服务端渲染；SQLite 用 Node 内置的 `node:sqlite`（`DatabaseSync`），没有外部数据库；Markdown 由 markdown-it 渲染、highlight.js 高亮，上传图片用 sharp 转成 WebP；安全相关用 helmet、express-rate-limit、multer。

## 本地运行（自托管版）

需要 Node.js 24.15 或更新的 24.x（仓库里有 `.nvmrc`）。

```sh
npm ci
npm run setup     # 只在首次运行：生成 .env，并写入一个随机管理员密码
npm start         # 打开 http://127.0.0.1:3000
```

后台在 `/admin`，密码是 `.env` 里的 `ADMIN_PASSWORD`。`.env` 不会提交到仓库，换机器时记得另行保管。开发时用 `npm run dev`（Node `--watch` 自动重启）。

## 静态版

```sh
npm run build     # 生成到 dist/
npm run preview   # 本地预览 http://127.0.0.1:4173
```

`vercel.json` 已配好构建命令、输出目录与无 `.html` 后缀的网址，把仓库导入 Vercel 即可部署。静态版里的 `/admin` 只是一页写作说明。

## 测试

```sh
npm test
```

当前 15 项，覆盖文章导入、发布权限、草稿隐藏与发布、并发修改拒绝、图片上传、RSS 与 sitemap、`BASE_PATH` 子路径部署等。测试使用临时数据库，不会改动 `data/` 里的真实文章。

## 目录

| 路径 | 内容 |
| --- | --- |
| `src/` | 服务与数据层：`app.mjs` 路由、`store.mjs` SQLite、`content.mjs` 校验与 Markdown |
| `views/` | EJS 模板 |
| `public/` | 样式、交互脚本、字体、图片与背景素材 |
| `content/` | 初始文章的 Markdown 与 `posts.json` 清单 |
| `scripts/` | 构建、预览、初始化与备份脚本 |
| `test/` | 集成测试 |
| `docs/` | 接口文档、验证记录、页面调整记录 |
| `inspiration/` | 另做的静态设计项目「博客灵感集」，与本站构建无关 |

## 文档

- [使用与部署.md](使用与部署.md)：Vercel / 服务器 / Docker 部署、备份与恢复、声音开关、维护记录
- [docs/API.md](docs/API.md)：后台 JSON 接口 v1、限流与字段上限
- [docs/验证记录.md](docs/验证记录.md)：历次验收记录及验证范围
- [docs/STYLE_REFRESH_20260930.md](docs/STYLE_REFRESH_20260930.md)：页面调整的逐项提交与回退方法

## 几点说明

- `inspiration/` 是独立静态项目，它的 `serve.mjs` 把端口写死为 4173，与 `npm run preview` 冲突，不要同时开（详情见 `使用与部署.md` 的「本地预览端口冲突」）。
- 游戏相关的背景与音效沿用原型素材，来源页保留原出处；没有声明为自有或可商用素材。
- 后台写接口要求同源：`Origin` 必须与 `SITE_URL` 一致，否则返回 403。
