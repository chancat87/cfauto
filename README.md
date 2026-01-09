
---

# Cloudflare Worker 多项目部署管理器 (CF-Worker-Manager)

这是一个运行在 Cloudflare Worker 上的**中控面板**。它允许你通过一个简单的网页界面，批量管理、更新和部署多个 Cloudflare Worker 节点。

特别针对 **CMliu (EdgeTunnel)** 和 **Joey (CFNew)** 两个项目进行了深度适配，支持账号聚合管理、代码自动拉取和变量隔离配置。

## ✨ 主要功能

* **🚀 自动化部署**：直接从 GitHub 拉取最新的项目源代码（beta2.0 / main），无需手动复制粘贴。
* **🛡️ 智能代码修复**：针对 Joey 项目的混淆代码，自动注入 `window` 补丁，彻底解决 `ReferenceError: window is not defined` 报错。
* **🗂️ 账号聚合管理**：添加一次账号（Account ID + Token），即可同时管理该账号下的 CMliu Worker 和 Joey Worker，无需重复添加。
* **🔄 项目分流隔离**：
* **CMliu 模式**：仅更新红色框内配置的 Worker，管理 UUID、PROXYIP 等全量变量。
* **Joey 模式**：仅更新蓝色框内配置的 Worker，主打代码更新。


* **☁️ 云端存储**：使用 KV 存储所有配置，支持跨设备同步，刷新页面不丢失数据。
* **⚙️ 智能变量补全**：自动检查并补全项目所需的默认变量（如 PROXYIP, ADMIN 等），防止配置丢失。

## 🛠️ 部署教程

### 1. 准备工作

* 拥有一个 Cloudflare 账号。
* 获取 **Account ID** (账户 ID)：在 Cloudflare 首页右下角获取。
* 获取 **API Token** (API 令牌)：
* 进入 `My Profile` -> `API Tokens`。
* 创建一个新令牌，使用模板 **"Edit Cloudflare Workers" (编辑 Cloudflare Workers)**。
* **注意**：不要使用 Global API Key！



### 2. 创建 KV 命名空间 (必须)

为了保存你的账号和变量配置，你需要创建一个 KV。

1. 进入 Cloudflare 控制台 -> **Workers & Pages** -> **KV**。
2. 点击 **Create a Namespace**。
3. 命名为 `CONFIG_KV` (或者任意你喜欢的名字)，点击添加。

### 3. 部署中控 Worker

1. 创建一个新的 Worker (例如命名为 `manager`)。
2. 点击 **Edit Code**，将本项目提供的 `worker.js` 代码全量粘贴进去。
3. **绑定 KV** (关键步骤)：
* 回到 Worker 的 **Settings (设置)** -> **Variables (变量)**。
* 找到 **KV Namespace Bindings**。
* 点击 **Add binding**。
* **Variable name** 填入 `CONFIG_KV` (必须与代码一致)。
* **KV Namespace** 选择你刚才创建的那个 KV。
* 点击 **Save and deploy**。



### 4. 设置访问密码 (可选)

为了防止他人通过网址修改你的节点：

1. 在 Worker 的 **Settings** -> **Variables** -> **Environment Variables** 中。
2. 添加一个变量：
* **Variable name**: `ACCESS_CODE`
* **Value**: `你的密码`


3. 保存部署。访问时需要在网址后加上 `?code=你的密码`，登录一次后会自动记录 Cookie。

---

## 📖 使用指南

### 界面介绍

面板分为左右两部分：

* **左侧 (账号管理)**：用于添加 Cloudflare 账号和分配 Worker。
* **右侧 (变量配置)**：用于设置 UUID、IP 等参数，并执行更新。
* **右上角 (项目切换)**：在 **🔴 CMliu** 和 **🔵 Joey** 之间切换。

### 添加节点

1. 在左侧填写 **备注**、**Account ID** 和 **API Token**。
2. **Worker 分流填写**：
* **🔴 CMliu Workers**：填入部署了 EdgeTunnel 的 Worker 名称，多个用逗号隔开 (例: `vless-hk, vless-sg`)。
* **🔵 Joey Workers**：填入部署了 CFNew 的 Worker 名称 (例: `joey-01`)。


3. 点击 **保存配置**。

### 批量更新

1. 在右上角选择你要操作的项目（例如 **CMliu**）。
2. 右侧会自动加载该项目的变量（如 `UUID`, `PROXYIP`）。
3. 修改变量值（点击 `🎲 刷新` 可生成新 UUID）。
4. 点击底部的 **🔄 立即执行更新**。
* 系统会自动拉取 GitHub 最新代码。
* 系统会自动将变量打包。
* 系统会**只更新**你在左侧对应颜色框里填写的 Worker。



---

## ❓ 常见问题 (FAQ)

**Q: 为什么提示 "API拒绝: Unable to authenticate"？**
A: 你的 **API Token** 填错了，或者使用了 Global API Key。请务必使用 **Edit Cloudflare Workers** 模板创建的 Token。

**Q: 为什么更新 Joey 项目时没有报错了？**
A: 本工具内置了自动修复功能。在部署 Joey 项目时，会自动在代码头部注入 `var window = globalThis;`，欺骗混淆器，解决 Cloudflare 的环境兼容问题。

**Q: 我切换项目后，变量不见了？**
A: 变量是按项目隔离存储的。CMliu 的变量存在 `VARS_cmliu`，Joey 的存在 `VARS_joey`。切换项目会自动加载对应的变量配置，互不干扰。

**Q: 点击更新后，Worker 变成了 404？**
A: 如果 Worker 之前不存在，脚本会自动创建它。Cloudflare 新建 Worker 后 DNS 生效可能需要 1-2 分钟，请稍等片刻。

---

## ⚠️ 免责声明

本项目仅用于技术学习和服务器管理交流。请勿用于非法用途。使用本工具产生的任何后果由使用者自行承担。
