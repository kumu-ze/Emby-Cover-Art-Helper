# Emby 封面下载 & 查看助手

Tampermonkey/Greasemonkey 用户脚本，给 Emby 媒体详情页与 Action Sheet 弹出菜单自动添加：

1. 查看封面 (新标签打开原图)
2. 下载封面 (自动识别文件名与后缀)

## 一键安装

[➡️ 点击这里安装 / 更新 (GreasyFork)](https://greasyfork.org/zh-CN/scripts/545363-emby-cover-art-helper)

说明：
1. 跳转到 GreasyFork 脚本页面后，点击 “安装此脚本” 按钮。
2. 若弹出脚本代码页，再次点击右上角 “安装”。
3. 安装后刷新 Emby 页面即生效。
4. GreasyFork 会自动提供更新；Tampermonkey 默认会定期检查，也可手动在 Dashboard -> “Check for updates”。

备用 Raw（仅当 GreasyFork 无法访问时使用）：
https://raw.githubusercontent.com/kumu-ze/Emby-Cover-Art-Helper/main/Emby-Cover-Art-Helper.js

## 特性 (v1.0)

- 去抖 MutationObserver，减少性能开销
- 避免重复注入，多次页面切换稳定工作
- 自动根据浏览器语言显示中文或英文文案
- 优先使用 `GM_download`，否则回退到 `GM_xmlhttpRequest`
- 自动从响应头尝试推断图片后缀
- 代码结构模块化，方便后续扩展（例如增加“复制链接”按钮）

## 安装 (手动方式)

如果“一键安装”失败，可按下面手动：

1. 安装 Tampermonkey 扩展。
2. 打开仓库文件：`Emby-Cover-Art-Helper.js` (Raw 模式或本地打开)。
3. 复制全部代码。
4. Tampermonkey 图标 -> Dashboard -> Utilities -> “Create a new script”。
5. 删除默认模板，粘贴代码，保存 (Ctrl+S)。
6. 访问 Emby：`http(s)://<你的域名或IP>/web/index.html`。
7. 打开任意媒体详情页验证是否出现按钮。
8. 不出现：强制刷新 (Ctrl+F5) / 检查控制台 `[CoverHelper]` 日志。

## 手动部署（自托管 Raw）

如果你想在自己服务器上托管以加速国内访问：

1. 将 `Emby-Cover-Art-Helper.js` 放到任意可静态访问的位置（如 Nginx / Git Pages / Cloudflare R2 静态）。
2. 确保响应头 `Content-Type: text/javascript; charset=utf-8`（不必须但推荐）。
3. 修改脚本头部 `@downloadURL` 与 `@updateURL` 为你的完整 HTTPS 地址。
4. 访问该新地址测试是否能触发 Tampermonkey 安装弹窗。
5. 以后更新：
   - 递增 `@version` 值。
   - 覆盖同路径文件。
   - 手动在 Tampermonkey 中 “Check for updates” 验证生效。

## 客户端（无油猴）直接引入模式

脚本已内置降级：在没有 GM_* API 的环境下仍可工作（使用 fetch + 原生 <style> 注入）。

使用步骤：
1. 将 `Emby-Cover-Art-Helper.js` 置于可被 Emby 前端访问的静态目录（例如反向代理的 /branding/ 目录）。
2. 在自定义注入（或修改 index.html 模板）中加入：
   `<script src="/branding/Emby-Cover-Art-Helper.js" defer></script>`
3. 清理浏览器缓存（Ctrl+F5），打开 Emby 详情页验证按钮。
4. 如果仍无效果，打开开发者工具 Console 检查是否有 404 或 CSP 拒绝脚本的报错。

差异说明：
- 无 GM_download 时使用 fetch + Blob 方案下载。
- 其它功能保持一致。

## 自动更新说明

脚本头部已包含 @downloadURL 与 @updateURL：

1. 默认 Tampermonkey 会定期检测更新。
2. 也可手动：Tampermonkey 图标 -> “Check for userscript updates”。
3. 若未检测到：确认脚本仍显示 version=1.0（若你本地做了修改，需手动同步）。

## 可配置项

在脚本顶部 `CONFIG` 对象可以调整：


| 选项                | 说明                          | 默认 |
| ------------------- | ----------------------------- | ---- |
| debounceMs          | DOM 变化去抖间隔(ms)          | 120  |
| enableActionSheet   | 是否注入 Action Sheet 按钮    | true |
| enableDetailButtons | 是否注入详情页按钮            | true |
| preferGMDownload    | 有 GM_download 时是否优先使用 | true |
| openInNewTab        | 查看封面是否使用新标签        | true |

## FAQ

Q: 按钮不出现？
A: 确认页面是媒体详情/已打开弹出菜单；或检查是否有其它脚本冲突。可在控制台查看 `[CoverHelper]` 日志。

Q: 下载后缀不对？
A: 少数情况下服务器未返回 Content-Type，会退回 .jpg，可手动改名。

## 许可证

MIT (可按需修改/再分发)。当前代码标注版本为 1.0。

欢迎提交 issue 或 PR 改进。
