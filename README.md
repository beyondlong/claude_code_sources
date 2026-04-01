# Claude Code 2.1.88 Source

<p align="center">
  <img src="https://img.shields.io/badge/Version-2.1.88-blue.svg" alt="Version">
  <img src="https://img.shields.io/badge/Status-Recovered-green.svg" alt="Status">
  <img src="https://img.shields.io/badge/Language-TypeScript-blue.svg" alt="Language">
  <img src="https://img.shields.io/badge/UI-Ink%20%2F%20React-orange.svg" alt="UI">
</p>

## 项目说明

背景：2026.03.31，`@anthropic-ai/claude-code@2.1.88` 的 npm 包中曾上传包含源码信息的 `cli.js.map`。本仓库基于该信息恢复出可阅读的 TypeScript 源码目录，方便做结构分析、实现研究与功能考古。

当前仓库更接近“源码恢复现场”，而不是可直接构建的官方工程副本：

- `src/` 中已经恢复出大量 TypeScript/TSX 源码。
- 根目录保留了 `claude-code-2.1.88.tgz` 安装包。
- 仓库内存在 `node_modules/` 与 `vendor/`，便于辅助阅读依赖和原生模块源码。
- 但仓库根目录目前没有恢复出官方工程级的 `package.json`、`bun.lock`、`tsconfig`、lint/build 配置等文件，因此默认不能直接在这里完成官方意义上的构建。

---

## 快速安装（镜像源）

由于 `2.1.88` 版本已从 [npm 官方页面](https://www.npmjs.com/package/@anthropic-ai/claude-code/v/2.1.88?activeTab=code) 下架，直接执行：

```sh
npm install -g @anthropic-ai/claude-code@2.1.88
```

---

## 仓库现状

从当前代码来看，这份恢复结果已经不只是一个入口文件，而是包含了相当完整的 CLI 应用层结构：

- CLI 启动入口位于 `src/entrypoints/cli.tsx`，主程序入口位于 `src/main.tsx`。
- 初始化逻辑位于 `src/entrypoints/init.ts`，包含配置启用、环境变量注入、遥测初始化、代理与证书预处理等流程。
- 命令注册集中在 `src/commands.ts`，可以看到大量内建命令与特性开关。
- 工具注册集中在 `src/tools.ts`，包含 Bash、文件编辑、Web 搜索、MCP、Agent、Todo、Plan Mode、Worktree 等工具。
- `vendor/` 下保留了一部分原生扩展源码，例如音频捕获、图片处理、URL 处理等。

另外，`claude-code-2.1.88.tgz` 包内的 `package/package.json` 依然可读，能确认官方包元信息：

- 包名：`@anthropic-ai/claude-code`
- 版本：`2.1.88`
- 二进制入口：`claude -> cli.js`
- Node 要求：`>=18`
- 打包产物中原本还包含 `bun.lock`、`cli.js`、`cli.js.map` 和若干平台原生二进制

---

## 实际目录结构

下面这份概览是基于当前仓库真实目录整理的，不是推测结构：

- `src/entrypoints/`：CLI、MCP、SDK 等入口与初始化类型定义
- `src/commands/`：命令实现，已能看到 `mcp`、`review`、`tasks`、`plugin`、`agents`、`memory`、`teleport`、`doctor`、`config` 等大量命令
- `src/tools/`：模型可调用工具实现，包括 `BashTool`、`FileEditTool`、`WebSearchTool`、`MCPTool`、`AgentTool`、`TodoWriteTool`、`EnterPlanModeTool` 等
- `src/components/`：基于 React + Ink 的终端 UI 组件
- `src/hooks/`：终端交互、状态同步、剪贴板、IDE 集成、设置监听等 Hook
- `src/services/`：认证、遥测、策略限制、MCP、LSP、PromptSuggestion 等服务层
- `src/utils/`：配置、权限、沙箱、代理、Git、Shell、模型、插件、遥测等基础设施
- `src/tasks/`：本地 Agent、远程 Agent、本地 Shell、DreamTask 等任务执行模型
- `src/skills/`：bundled skills、动态 skills 装载逻辑
- `src/plugins/`：内建插件与插件相关机制
- `src/ink/`：终端渲染基础设施
- `vendor/`：原生模块源码或绑定相关代码

---

## 从源码里能看到什么

这份恢复代码已经足够用来研究 Claude Code 的很多核心设计：

- 命令系统：`src/commands.ts` 展示了命令注册、别名、懒加载与 feature flag 裁剪方式
- Tool 系统：`src/tools.ts` 展示了模型工具的统一注册入口，以及按环境或开关启用工具的机制
- Agent / Task 模型：`src/tasks/`、`src/tools/AgentTool/`、`src/tools/shared/` 提供了多代理与任务编排线索
- MCP 集成：既有 `mcp` 命令，也有 `MCPTool`、`ListMcpResourcesTool`、`ReadMcpResourceTool`
- 终端 UI：`src/components/` + `src/ink/` 展示了复杂终端交互界面的组织方式
- Feature Flags：源码中大量使用 `bun:bundle` 的 `feature(...)` 做构建期裁剪
- 远程能力：可见 bridge、teleport、remote session、remote managed settings 等能力痕迹

---

## 恢复状态评估

如果你的目标是“阅读源码”，这份仓库已经很有价值。

如果你的目标是“本地跑起来”，目前还缺少几块关键拼图：

1. 官方工程根目录的 `package.json`、`bun.lock`、`tsconfig` 和构建配置没有完整落地到仓库。
2. `feature(...)`、`MACRO.VERSION`、`bun:bundle` 等构建期能力需要额外的打包环境。
3. 某些平台相关原生依赖、运行时环境变量、认证配置和内部开关也需要补齐。
4. 现有 `node_modules/` 更适合辅助阅读，不代表已经具备可重复构建条件。

---

## 建议的后续整理方向

如果你想继续把这个仓库整理得更可用，可以按下面顺序推进：

1. 从 `claude-code-2.1.88.tgz` 中提取 `package/package.json` 与 `bun.lock`，补回根目录元信息。
2. 结合源码中的 `bun:bundle`、宏变量和 feature flags，推断原始打包方式。
3. 增补 `tsconfig`、lint 配置和最小化启动脚本，先做到“能解析、能索引、能局部运行”。
4. 再逐步验证 CLI 启动链路、命令装载链路和工具装载链路。

---

## 免责声明

- 本仓库不是 Anthropic 官方仓库，也不代表其立场。
- 原始代码相关版权、商标及其他权利归原权利方所有。
- 本项目更适合作为归档、研究、结构分析与源码阅读材料。
- 如需二次分发、商用或公开传播，请自行评估许可与法律风险。

---

## 致谢

感谢发布包中遗留的 Source Map，让这份复杂 CLI 工程的内部结构得以被重新观察。

---

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=beyondlong/claude_code_sources&type=Date)](https://star-history.com/#beyondlong/claude_code_sources&Date)
