# 个人工作台发布到 GitHub Pages

GitHub Pages 适合长期维护这个工作台。你把静态文件放进 GitHub 仓库后，可以通过 Pages 生成一个公开网址，后续每次更新仓库文件，网站也可以跟着更新。

## 准备文件

推荐上传到 GitHub 仓库根目录的文件：

```text
index.html
config.js
manifest.webmanifest
sw.js
icon.svg
.nojekyll
```

可以一起上传但不是 GitHub Pages 必需的文件：

```text
vercel.json
VERCEL_DEPLOY.md
EDGEONE_PAGES_DEPLOY.md
GITHUB_PAGES_DEPLOY.md
```

不要上传：

```text
supabase-setup.sql
config.example.js
.uploads
```

`supabase-setup.sql` 是建表脚本，不需要发布到公网。`config.js` 会包含 Supabase 的 `anon public key`，这是前端应用常见做法；真正的数据隔离依赖 Supabase 的 RLS 策略。

## 创建 GitHub 仓库

1. 打开 GitHub。
2. 登录账号。
3. 点击右上角 `+`。
4. 选择 `New repository`。
5. 仓库名可以填：

```text
personal-workbench
```

6. 如果使用免费账号，建议选择 `Public`。
7. 勾选 `Add a README file` 也可以，不勾选也可以。
8. 点击 `Create repository`。

## 上传文件

1. 进入刚创建的仓库。
2. 点击 `Add file`。
3. 选择 `Upload files`。
4. 把准备好的文件拖进去。
5. 确认 `index.html` 在仓库第一层，不要放进子文件夹。
6. 页面底部点击 `Commit changes`。

仓库根目录看起来应该类似这样：

```text
personal-workbench/
  index.html
  config.js
  manifest.webmanifest
  sw.js
  icon.svg
  .nojekyll
```

## 开启 GitHub Pages

1. 进入仓库页面。
2. 点击 `Settings`。
3. 左侧找到 `Pages`。
4. 在 `Build and deployment` 里选择：

```text
Source: Deploy from a branch
Branch: main
Folder: /root
```

5. 点击 `Save`。
6. 等待 1 到 10 分钟。
7. 页面上方会出现网站地址，通常类似：

```text
https://你的用户名.github.io/personal-workbench/
```

## Supabase 回调地址

发布完成后，把 GitHub Pages 地址填回 Supabase。

1. 打开 Supabase 项目后台。
2. 进入 `Authentication`。
3. 进入 `URL Configuration`。
4. 把 `Site URL` 改成 GitHub Pages 地址。
5. 在 `Redirect URLs` 里加入同一个地址。

示例：

```text
https://你的用户名.github.io/personal-workbench/
```

注意末尾有没有 `/` 都可以，但建议和浏览器地址栏里显示的一致。

## 验证发布

1. 用电脑打开 GitHub Pages 地址。
2. 页面能正常显示个人工作台。
3. 打开底部 `本地数据管理`。
4. 登录 Supabase。
5. 新增一条测试待办。
6. 点击 `上传本地到云端`。
7. 用手机打开同一个地址。
8. 登录同一个 Supabase 账号。
9. 点击 `从云端拉取`。
10. 如果测试待办出现，说明发布和同步都正常。

## 常见问题

### 打开后是 404

通常是 Pages 还没发布完成，或者 `index.html` 不在仓库根目录。等几分钟后再刷新，并检查 `Settings` → `Pages` 里的分支和目录是否是 `main` / `/root`。

### 页面打开但样式或 PWA 不正常

确认这些文件都在仓库根目录：

```text
manifest.webmanifest
sw.js
icon.svg
```

### 登录后不能同步

先确认 `config.js` 里的 Supabase 配置正确，再检查 Supabase 的 `Site URL` 和 `Redirect URLs` 是否已经加入 GitHub Pages 地址。

### 不想公开仓库

GitHub Pages 对私有仓库发布能力和账号套餐有关。为了少踩坑，第一次建议先用公开仓库，但不要上传私人数据、SQL 文件或不需要公开的说明。
