# BTC 5分钟方向提示器 · 网页版（GitHub + Cloudflare Pages 部署仓库）

本仓库包含网页版前端 + 目标价采集云函数（`/api/target`）。
**同时兼容两种部署**：Cloudflare Pages（推荐）和 Netlify（`netlify/` 目录）。

## 文件结构

```
functions/api/target.js     # Cloudflare Pages 云函数（/api/target，服务器端直连 Gate）
netlify.toml                # Netlify 构建配置（备用）
netlify/functions/target.js # Netlify 云函数（备用）
public/index.html           # 网页版主页面
public/manifest.json        # PWA 配置（添加到手机主屏幕）
public/sw.js                # Service Worker
public/icons/               # 应用图标
```

## 第一步：上传到 GitHub

1. 打开 github.com，登录后点右上角 `+` → `New repository`
2. 名称随便起（如 `btc5m-web`），Public 或 Private 均可 → `Create repository`
3. 在新仓库页面点 **uploading an existing file**
4. 把本文件夹里的**全部内容**（functions 文件夹、netlify 文件夹、netlify.toml、public 文件夹、README.md）拖进上传区 → `Commit changes`

## 第二步：Cloudflare Pages 部署（免费额度大，推荐）

1. 打开 [dash.cloudflare.com](https://dash.cloudflare.com)，注册/登录（免费套餐即可）
2. 左侧菜单 **Workers & Pages** → **Create** → 选 **Pages** 标签 → **Connect to Git**
3. 授权 GitHub → 选择刚上传的仓库 → **Begin setup**，配置如下：
   - Project name：随意（会作为 `xxx.pages.dev` 域名前缀）
   - Production branch：`main`
   - Framework preset：**None**
   - Build command：**留空**
   - Build output directory：`public`
4. 点 **Save and Deploy**，约 1 分钟完成
5. **验证云函数**：浏览器打开 `https://你的项目名.pages.dev/api/target`
   - 返回类似 `{"price":69xxx.x,"start":...,"end":...,"source":"gate-..."}` 即成功

> 部署后首次访问 /api/target 若返回 502，等几秒刷新一次（期次切换边界）。

## 以后如何更新

- 在 GitHub 仓库网页上直接点文件 → 铅笔图标编辑 → `Commit changes`
- 每次提交后 Cloudflare **自动重新部署**，无需手动操作

## 桌面端对接说明

桌面端 V5.exe 的目标价采集链路（TargetFeed.cs）：

1. 首选：`https://你的域名/api/target`（云函数，服务器在海外可直连 Gate）
2. 兜底1：本机直连 Gate 三个域名（gate.io / gate.com / gatewebsite.com）
3. 兜底2：Jina 阅读器中转

> 国内网络下 2、3 通常被 Gate WAF（腾讯云 EdgeOne 567）或墙拦截，云函数是主通道。

## 备用：Netlify 部署

若要用 Netlify：仓库已含 `netlify.toml`（publish=public、functions=netlify/functions、
`/api/target` 重定向）。在 Netlify 选 `Import an existing project → GitHub` 即可，
无需额外配置。注意 Netlify 免费额度用尽后部署会被暂停。
