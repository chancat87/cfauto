# 版本更新日志 (Changelog)

> 倒序排列，最新版本在前。

---

## V10.8.0 (2026-02-20)

### 🚫 新功能：ECH 禁用 workers.dev 域名开关

* ECH 配置卡片新增「🚫 禁用默认 \*.workers.dev 域名」复选框（默认不勾选）。
* 勾选后部署成功，自动调用 CF API 将该 Worker 的 workers.dev 子域名禁用。
* 不勾选则保持启用（默认行为）。
* 工作台日志显示 `🚫 默认域名已禁用` 或 `🌐 默认域名已启用` 的操作结果。

---

## V10.7.0 (2026-02-20)

### 🔑 新功能：ECH Token 鉴权开关

* ECH 配置卡片底部新增虚线框区域，包含 Toggle 开关和 Token 输入框。
* **填写 Token + 开启开关** → 部署时将 Token 注入 `ech.js` 的 `const token = '...'`，启用 WebSocket 鉴权。
* **不填 / 关闭开关** → `const token = ''`（无鉴权，默认行为），即使输入框有内容也不会注入。
* Token 值同时保存到 `VARS_ech` 中，方便下次预填。

---

## V10.6.0 (2026-02-16)

### 🚀 新功能

* 工作台改为底部弹窗显示，主页添加「📋 工作台」按钮。
* 批量部署新增「📦 采用已保存变量 (VARS)」复选框（默认开启）。
* 修复 1101 实时步骤打印到工作台。
* 一键修复 1101 新增自定义域名恢复流程：记录变量 → 删除 Worker → 随机改子域名 → 相同名称重建 → 恢复所有变量 + 域名绑定。

### 🗑️ 移除

* 删除所有混淆功能（serverSideObfuscate、JavaScriptObfuscator CDN、自动混淆开关、批量部署混淆）。

### ✨ 改进

* 所有操作日志统一输出到工作台弹窗。
* `DEPLOY_CONFIG` 更新不再依赖 SHA，修复本地时间不更新。

---

## V10.3.3 (2026-02-16)

### 🐛 修复

* 重写 `serverSideObfuscate`：仅用头部随机注释+尾部 var 声明，修复 cmliu 1101。
* 子域名修改改为 DELETE+PUT 两步操作，解决 "Account already has an associated subdomain" 错误。

---

## V10.3.2 (2026-02-16)

### 🐛 修复

* 手动部署改回服务端反指纹混淆，修复 `JavaScriptObfuscator` 对 edgetunnel 代码过于激进导致 Workers 1101 错误。
* `JavaScriptObfuscator` 仅保留给批量部署使用。

---

## V10.3.1 (2026-02-16)

### 🔐 反指纹混淆

* 重写 `serverSideObfuscate`：注入大量随机死代码，每次部署指纹完全不同，防止 CF 特征码匹配。

### 📋 文档重构

* 新增 `CHANGELOG.md` 独立版本记录文件（倒序排列）。
* `worker.js` 和 `README.md` 仅保留当前版本日志，历史版本移至本文件。

---

## V10.3.0 (2026-02-16)

### 🚀 手动部署前端混淆

* 点击「🚀 部署更新」时，若开启自动混淆，在浏览器端完整混淆后再部署。
* `coreDeployLogic` 新增 `customCode` 参数，支持接收前端预混淆代码。

---

## V10.2.3 (2026-02-16)

### 🐛 Bug 修复

* 重写 `serverSideObfuscate`，移除危险的注释删除正则（误删模板字面量中 HTML/URL 内容导致 `SyntaxError`）。

---

## V10.2.2 (2026-02-16)

### 🐛 Bug 修复

* DEPLOY_CONFIG 仅在至少一个 Worker 成功部署后才更新 SHA。
* 手动部署读取「自动混淆」开关。

---

## V10.2.1 (2026-02-16)

### 🐛 关键 Bug 修复

* 修复 `coreDeployLogic` 中 `targetSha='latest'` 被当作 git ref 导致自动更新失败。
* 修复部署后 deploy config 被错误锁定为 `fixed` 模式。
* 修复历史版本「Always Latest」部署触发 URL 构造错误。

---

## V10.2.0 (2026-02-14)

### 🌌 暗黑星空主题

* 新增暗黑星空模式 / 明亮模式主题切换。
* Canvas 动态星空背景（闪烁星星 + 流星 + 星云光晕）。
* 卡片毛玻璃半透明效果，全组件暗黑模式适配。
* 主题选择通过 localStorage 持久化。

---

## V10.1.0 (2026-02-14)

### 🌐 子域名管理

* 查看/修改 workers.dev 子域名前缀。
* 安全二次确认 + 格式校验。
* 新增 `/api/get_subdomain`、`/api/change_subdomain` 接口。

---

## V10.0.0 (2026-02-14)

### 🔐 安全加固

* 登录改为 POST 提交，Cookie 增加 Secure 标志。
* API 方法校验、CSRF 防护、统一错误响应。

### 🐛 缺陷修复

* 修复混淆正则误删 URL、checkUpdate 变量冲突、编辑账号 stats 重置。

### ⚡ 改进

* 熔断/自动更新动态化，compatibility_date 动态化。
* 前后端数据消除重复，由后端动态注入。
