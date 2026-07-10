# 个人工作台发布到 Vercel

当前文件夹已经整理成可直接部署的静态 PWA 站点。部署完成后，你会得到一个手机和电脑都能访问的网址，并可以在 Supabase 登录后手动同步数据。

## 文件说明

| 文件 | 用途 |
|---|---|
| `index.html` | 工作台主页面 |
| `config.js` | Supabase 项目配置 |
| `config.example.js` | 配置模板 |
| `manifest.webmanifest` | PWA 安装信息 |
| `sw.js` | PWA 离线缓存 |
| `icon.svg` | 应用图标 |
| `supabase-setup.sql` | Supabase 数据表和权限脚本 |
| `vercel.json` | Vercel 部署配置 |

## 部署前检查

`config.js` 里需要保留你的 Supabase 配置：

```js
window.WORKBENCH_CONFIG = {
  SUPABASE_URL: "https://你的项目引用.supabase.co",
  SUPABASE_ANON_KEY: "你的 anon public key",
  CLOUD_TABLE: "workbench_states"
};
```

`anon public key` 会出现在前端代码里，这是 Supabase 前端应用的常见用法。真正的数据隔离依赖 `supabase-setup.sql` 里的 RLS 策略。

## 方式一：Vercel 拖拽上传

这种方式最快，不需要先创建 GitHub 仓库。

1. 打开 [Vercel](https://vercel.com)。
2. 使用 GitHub、邮箱或其他方式登录。
3. 进入 Dashboard。
4. 找到 `Add New...`。
5. 选择 `Project`。
6. 如果页面提供拖拽上传入口，不要直接选择当前工作台所在目录。先在桌面或下载目录新建一个干净文件夹，例如 `personal-workbench-deploy`，只复制 `index.html`、`config.js`、`manifest.webmanifest`、`sw.js`、`icon.svg`、`vercel.json` 这 6 个文件进去，然后把这个干净文件夹拖进去。
7. Vercel 会识别为静态站点。
8. Framework Preset 选择 `Other`。
9. Build Command 留空。
10. Output Directory 留空或保持默认。
11. 点击 `Deploy`。
12. 部署完成后，复制 Vercel 生成的网址，例如：

```text
https://personal-workbench.vercel.app
```

如果你的 Vercel 页面没有显示拖拽入口，使用下面的 GitHub 导入方式。

## 方式二：GitHub 仓库导入

这种方式更适合长期维护，每次修改后可以自动重新部署。

1. 在 GitHub 新建仓库，例如 `personal-workbench`。
2. 把 `index.html`、`config.js`、`manifest.webmanifest`、`sw.js`、`icon.svg`、`vercel.json` 上传到仓库根目录。
3. 打开 [Vercel](https://vercel.com)。
4. 点击 `Add New...`。
5. 选择 `Project`。
6. 选择刚才的 GitHub 仓库。
7. Framework Preset 选择 `Other`。
8. Build Command 留空。
9. Output Directory 留空或保持默认。
10. 点击 `Deploy`。

以后你修改代码后，只要推送到 GitHub，Vercel 会自动发布新版本。

## Supabase 回调地址

部署完成后，需要把 Vercel 地址填回 Supabase。

1. 打开 Supabase 项目后台。
2. 进入 `Authentication`。
3. 进入 `URL Configuration`。
4. 把 `Site URL` 改成你的 Vercel 地址。
5. 在 `Redirect URLs` 里加入同一个地址。

示例：

```text
https://personal-workbench.vercel.app
```

如果你继续关闭邮箱确认，这一步仍然建议配置，后续恢复邮箱确认或更换登录方式时会用到。

## Supabase 数据表

如果还没有执行过建表脚本：

1. 打开 Supabase 项目后台。
2. 进入 `SQL Editor`。
3. 新建查询。
4. 复制 `supabase-setup.sql` 的全部内容。
5. 点击 `Run`。

脚本会创建 `workbench_states` 表，并限制用户只能访问自己的数据。

## 验证部署

部署完成后，用电脑浏览器打开 Vercel 地址。

1. 打开页面底部的 `本地数据管理`。
2. 在 `PWA 与云端同步` 中登录。
3. 新增一条测试待办。
4. 点击 `上传本地到云端`。
5. 看到上传完成后，换另一个浏览器或手机打开同一个 Vercel 地址。
6. 登录同一个 Supabase 账号。
7. 点击 `从云端拉取`。
8. 如果测试待办出现，说明多端同步正常。

## 手机安装

手机访问 Vercel 地址后：

- iPhone Safari：点击分享按钮，选择 `添加到主屏幕`。
- Android Chrome：打开菜单，选择 `安装应用` 或 `添加到主屏幕`。

## 如果 Vercel 地址打不开

如果手机或电脑打开 `vercel.app` 地址时出现超时、SSL 错误或连接失败，优先尝试备用发布方案：

- 国内访问优先：查看 `EDGEONE_PAGES_DEPLOY.md`，发布到腾讯 EdgeOne Pages。
- 长期维护优先：查看 `GITHUB_PAGES_DEPLOY.md`，发布到 GitHub Pages。

安装后会像普通 App 一样出现在桌面。首次加载需要网络；加载过一次后，PWA 会缓存核心文件，弱网时也能打开最近版本。

## 常见问题

### 登录成功但上传失败

通常是 Supabase RLS 策略没有正确执行，或当前没有有效登录会话。先退出再登录，然后确认 `supabase-setup.sql` 已完整运行。

### 手机和电脑数据不一致

当前是手动同步。电脑修改后需要点 `上传本地到云端`，手机再点 `从云端拉取`。反过来也是一样。

### 更新后手机还是旧页面

PWA 会缓存文件。可以先刷新网页，必要时在浏览器里清除该站点缓存后重新打开。`vercel.json` 已让 `sw.js` 和 `config.js` 不长期缓存，后续更新会更容易生效。

### 是否可以公开仓库

可以。Supabase 的 anon key 是前端公开 key，安全边界在 RLS 策略。不要把 Supabase 的 service role key 放进前端文件或 GitHub 仓库。
