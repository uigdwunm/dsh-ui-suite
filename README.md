# DSH UI Suite（暂定名，待定）

一套 **DeepSeek Harness (DSH) Web UI 插件集合**。每个功能是**一个独立的 npm 插件包**，
可单独安装、单独发布、单独进入插件社区列表；聚合包只负责"一次装齐"。

> ⚠️ 项目名未定，`dsh-ui-suite` 仅为工作目录占位名。确定后统一改名并建公开仓库。

## 设计原则

**每个插件独立列出，而不是作为一套。** 具体含义：

1. **独立 npm 包**：`packages/*` 下每个目录都是一个独立 npm 包（独立 `name`、独立版本号、
   独立 `dsh.bundle` manifest），像 `dsh-web-archive`、`@linxin666/dsh-live-stats` 那样
   独立发布到 npm。
2. **独立进社区列表**：awesome-dsh-plugin 等社区列表按 npm 包收录，每个插件一条独立条目、
   独立 README、独立描述——用户只装自己需要的那个。
3. **可独立安装**：`dsh plugin --profile web add <单个包名>` 只装一个功能；
   `packages/*-all` 聚合包（可选）一次装齐所有。
4. **独立演进**：每个包独立版本、独立发布节奏，改一个不影响其他。

## 结构

```
dsh-ui-suite/
├── packages/
│   ├── dsh-process-fold/       ← 第 1 个插件：执行过程折叠
│   ├── dsh-conversation-nav/  ← 第 2 个插件：对话快速导航
│   └── dsh-xxx/               ← 后续逐个加（每个独立包）
├── shared/                  ← 跨插件共享代码（可选）
└── pnpm-workspace.yaml
```

## 插件开发模式（每个包的标准构成）

参考 [dsh-web-archive](https://github.com/renat3u/dsh-web-archive) 的轻量模式：

| 文件 | 作用 |
|---|---|
| `package.json` | `dsh.bundle.patch`（挂载层）+ `dsh.client`（浏览器半声明，`platform: "web"`）+ `exports["./client"]` |
| `cordis.patch.yml` | 一行 `insert`，把插件挂进 profile 树 |
| `src/index.ts` | node 半入口（纯 UI 插件可为空 `apply`） |
| `src/client.ts` | 浏览器半入口：`export function apply(ctx)`，用 `ctx.effect()` 注册/清理 |
| `build.mjs` | esbuild 构建（node 半 + 浏览器半） |

## 快速开始（第一个插件）

```sh
pnpm install
pnpm --filter dsh-process-fold build
pnpm --filter dsh-conversation-nav build

# 按需安装一个独立插件
dsh plugin --profile web add file:./packages/dsh-process-fold
dsh plugin --profile web add file:./packages/dsh-conversation-nav
# 重启 dsh web，刷新页面
```

## 发布到社区（每个插件独立执行）

1. `npm publish` 发布独立包（每个包独立版本号）；
2. 给 [awesome-dsh-plugin](https://github.com/awesome-dsh-plugin/awesome-dsh-plugin) 提 PR，
   每个插件一条独立条目（类别：UI Enhancements / Themes 等）。

## 开发环境

- Node ≥ 20，pnpm ≥ 9
- 需要本机已安装 DSH（`dsh` 命令可用）
