# 个人工作台发布到腾讯 EdgeOne Pages

EdgeOne Pages 更适合解决国内访问 `vercel.app` 不稳定的问题。你的个人工作台是纯静态网页，发布时只需要上传网页运行必需文件。

## 准备发布文件

在桌面或下载目录新建一个干净文件夹，例如：

```text
personal-workbench-deploy
```

只复制下面 6 个文件进去：

```text
index.html
config.js
manifest.webmanifest
sw.js
icon.svg
vercel.json
```

`vercel.json` 不是 EdgeOne Pages 必需文件，但保留它通常不会影响静态网页运行。为了减少干扰，也可以不上传它。

不要上传这些文件：

```text
VERCEL_DEPLOY.md
GITHUB_PAGES_DEPLOY.md
EDGEONE_PAGES_DEPLOY.md
supabase-setup.sql
config.example.js
.gitignore
.uploads
```

## 方式一：直接上传

1. 打开腾讯云控制台。
2. 搜索 `EdgeOne` 或 `EdgeOne Pages`。
3. 进入 EdgeOne Pages。
4. 新建项目。
5. 如果页面提供上传文件夹或上传静态文件的入口，选择刚才创建的 `personal-workbench-deploy` 文件夹。
6. 框架或构建类型选择静态站点、静态文件或无框架。
7. 构建命令留空。
8. 输出目录留空，或按页面提示选择根目录。
9. 发布后复制生成的网址。

如果控制台要求填写构建配置，可以这样填：

```text
Framework Preset: Other / Static
Build Command: 留空
Output Directory: 留空或 .
Root Directory: 留空或 .
```

## 方式二：连接 GitHub 或 Gitee

如果你后续希望每次修改后自动发布，可以先把这 6 个文件上传到 GitHub 或 Gitee 仓库根目录，再在 EdgeOne Pages 里连接仓库。

推荐仓库根目录文件：

```text
index.html
config.js
manifest.webmanifest
sw.js
icon.svg
vercel.json
```

连接仓库后，构建配置仍然按静态站点处理：

```text
Build Command: 留空
Output Directory: .
```

## Supabase 回调地址

发布完成后，需要把 EdgeOne Pages 生成的网址填回 Supabase。

1. 打开 Supabase 项目后台。
2. 进入 `Authentication`。
3. 进入 `URL Configuration`。
4. 把 `Site URL` 改成 EdgeOne Pages 地址。
5. 在 `Redirect URLs` 里加入同一个地址。

示例：

```text
https://你的项目地址
```

如果你当前关闭了邮箱确认，这一步仍然建议配置，后续恢复邮箱确认或换登录方式时会用到。

## 绑定自定义域名

如果你有自己的域名，可以绑定到 EdgeOne Pages 上，而不是一直用平台生成的默认地址。

### 域名来源

常见选择：

- 阿里云万网
- 腾讯云 DNSPod
- Cloudflare
- 其他域名注册商

### 绑定步骤

1. 进入 EdgeOne Pages 控制台，找到你的项目。
2. 进入 `域名配置` 或 `Custom Domain` 页面。
3. 点击 `添加自定义域名`。
4. 输入你想绑定的域名，例如：

```text
workbench.yourdomain.com
```

5. 平台会提示你添加一条 DNS 解析记录。
6. 去你的域名服务商（如 DNSPod、阿里云、Cloudflare）控制台，添加对应的 CNAME 记录：

```text
记录类型: CNAME
主机记录: workbench（或你填写的子域名前缀）
记录值: EdgeOne Pages 提供的 CNAME 目标地址
```

7. 等待 DNS 生效（通常几分钟到半小时）。
8. 回到 EdgeOne Pages 控制台，确认域名状态变为 `已生效` 或 `Active`。
9. 平台通常会自动配置 HTTPS 证书。

### 国内站备案提示

如果你使用的是腾讯云中国站（`console.cloud.tencent.com`），绑定的自定义域名需要已经通过 ICP 备案。如果域名没有备案，可能无法绑定或无法访问。

国际站通常没有备案要求，但访问速度在国内可能不如中国站。

### 绑定后记得更新 Supabase

域名生效后，去 Supabase 后台把 `Site URL` 和 `Redirect URLs` 改成你的自定义域名。

示例：

```text
https://workbench.yourdomain.com
```

## 验证发布

1. 用电脑打开 EdgeOne Pages 地址。
2. 页面能正常显示个人工作台。
3. 打开底部 `本地数据管理`。
4. 登录 Supabase。
5. 新增一条测试待办。
6. 点击 `上传本地到云端`。
7. 用手机打开同一个地址。
8. 登录同一个 Supabase 账号。
9. 点击 `从云端拉取`。
10. 如果测试待办出现，说明发布和多端同步都正常。

## 手机安装

手机访问 EdgeOne Pages 地址后：

- iPhone Safari：点击分享按钮，选择 `添加到主屏幕`。
- Android Chrome：打开菜单，选择 `安装应用` 或 `添加到主屏幕`。

如果手机提示无法安装，通常是浏览器没有识别到 PWA。先确认 `manifest.webmanifest`、`sw.js`、`icon.svg` 都已经上传到网站根目录。
