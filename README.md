# NIGHTMARE

![NIGHTMARE](https://i.ibb.co/5h9sQtSq/abnner.png)

> SillyTavern 第三方扩展 · 模块化的视觉 / 提示词 / 系统增强套件

NIGHTMARE 是一个面向 [SillyTavern](https://github.com/SillyTavern/SillyTavern) 的浏览器侧扩展，将主题美化、变量引擎、自动总结、通知系统等能力组合到同一个设置面板下，以模块化方式按需启用。

本仓库只发布**构建产物**，源代码不开源。二进制在 AGPL-3.0 下分发，详见 [LICENSE](./LICENSE)。

---

## 功能一览

| 分类 | 模块 | 说明 |
| --- | --- | --- |
| 视觉 | **主题美化** | 可切换的 NIGHTMARE 主题，叠加在 ST 原主题之上，不破坏 SmartTheme 变量 |
| 视觉 | **隐藏楼层指示器** | 在聊天中显示已隐藏的楼层数，支持一键恢复所有隐藏楼层 |
| 视觉 | **聊天搜索** | 聊天内全文搜索，点击结果跳转到对应消息并高亮显示，支持快捷键 |
| 视觉 | **水印** | 聊天上方信息条，可自定义显示角色名、Token 数、变量数、游玩时间等段落 |
| 提示词 | **变量引擎** | 从 AI 消息的 `<nm-data>` 标签解析 `setChatVar` / `incChatVar` 等函数调用，维护每聊天持久化的变量存储。支持提示词注入与独立 API 分析两种来源 |
| 提示词 | **变量查看器** | 实时查看当前聊天的所有变量，支持搜索与手动编辑 |
| 提示词 | **宏替换** | 在提示词、角色卡、世界书中使用 `{{nmvar::name}}` / `{{nmvar_json::name}}` / `{{nmvar_all}}` 等宏读取变量 |
| 提示词 | **提示词预设** | 存储常用提示词片段，发送时将启用的片段注入生成上下文，支持自定义注入位置与角色 |
| 系统 | **自动总结** | 基于 Token 阈值监控聊天上下文，达到阈值后自动调用独立 API 生成滚动摘要并写入世界书 |
| 系统 | **更新检查** | 启动时自动比对 GitHub Releases 最新版本，发现新版仅在面板头部显示 NEW 徽章，不强制更新 |
| 杂项 | **通知系统** | 统一承接插件内部事件，支持分类过滤、历史记录、标签去重，可隐藏 ST 原生 toast |

---

## 安装

### 在 SillyTavern 里安装（推荐）

1. 打开 SillyTavern，顶部菜单点击 **Extensions（拼图图标）**
2. 点击 **Install extension**，粘贴本仓库 URL：

   ```
   https://github.com/jsh135790/NIGHTMARE-PRO
   ```

3. 确认安装即可。ST 会把仓库 clone 到 `public/scripts/extensions/third-party/NIGHTMARE-PRO/`
4. 刷新页面 → 在设置面板底部会出现 **NIGHTMARE** 折叠卡

### 更新

`manifest.json` 已声明 `auto_update: true`，SillyTavern 启动时会自动 `git pull` 本仓库。你也可以在扩展面板里手动点击 **Update** 按钮。

插件内部的更新检查模块会另外监控 GitHub Releases tag —— 发现新 tag 时在 NIGHTMARE 面板标题右侧显示 NEW 徽章，点击跳转到发布页。不会自动下载或强制升级。

### 前置要求

- SillyTavern `1.12.x+`（推荐 `1.16.x` 及以上）
- 推荐使用最新 staging 分支以获得最完整的 API 兼容

---

## 首次使用

安装完刷新后，设置抽屉里会出现 NIGHTMARE 折叠面板。展开后可以看到四个顶层分类：

- **视觉 Visuals** —— 主题美化 / 隐藏楼层指示器 / 聊天搜索 / 水印
- **提示词 Prompt** —— 变量引擎 / 变量查看器 / 宏 / 提示词预设
- **系统 System** —— 自动总结 / 更新检查
- **杂项 Misc** —— 通知系统

每个模块彼此独立，关掉某个模块不影响其它模块运行。

### 最小配置建议

- **只想体验主题**：仅启用"主题美化"
- **想让 AI 自动记账状态**：启用"变量引擎"（来源选 `提示词注入` 是最省事的方式），配合"变量查看器"观察效果
- **长对话压缩上下文**：启用"自动总结"，先在该模块面板配置一个 OpenAI 兼容的 API 端点

---

## 外部 API

启用**变量引擎**后，NIGHTMARE 会在 `window.NIGHTMARE` 下暴露一个只读命名空间，供其它扩展 / Quick Reply / 自定义脚本调用：

```js
window.NIGHTMARE.setChatVar('chars.Alice.hp', 80);
window.NIGHTMARE.getChatVar('chars.Alice.hp');   // => 80
window.NIGHTMARE.incChatVar('day');
window.NIGHTMARE.getAllChatVars();
window.NIGHTMARE.version;                         // 当前插件版本
```

支持的操作与对应的 `<nm-data>` 标签函数一一对应：`setChatVar` / `getChatVar` / `addChatVar` / `incChatVar` / `decChatVar` / `delChatVar` / `hasChatVar` / `pushChatVar` / `unshiftChatVar` / `popChatVar` / `shiftChatVar` / `removeAtChatVar` / `removeChatVar` / `getAllChatVars` / `clearChatVars`。

变量名支持点号嵌套（如 `chars.Alice.hp`）。完整语义见[开发者文档](https://nightmare-wiki.vercel.app/developer)。

---

## 反馈与问题

- Bug / 功能建议：提 [Issue](https://github.com/jsh135790/NIGHTMARE-PRO/issues)
- 发布公告：关注 [Releases](https://github.com/jsh135790/NIGHTMARE-PRO/releases)

> 源代码不在本仓库公开。本仓库仅作为构建产物的分发通道，用于 SillyTavern 自动更新机制 (`git pull`)。

---

## License

本仓库以 [GNU AGPL-3.0](./LICENSE) 分发。

简单来说：

- 你可以免费使用、修改、自部署
- 如果你基于本项目做了修改并以任何形式分发（包括以网络服务的形式提供给他人使用），你必须以相同或兼容的协议公开你的修改
- 本软件不提供任何担保

完整条款以 [LICENSE](./LICENSE) 为准。
