# dsh-process-fold

把 DSH Web 会话里每一轮的**工具调用过程**（思考 / 工具调用 / 上下文注入 …）套进一个**方框**，框内默认只显示**最新 2 项**，更早的项隐藏；最终文字结果、**用户的对话**、**提问（ask_user_question）**都留在框外、完整显示。每个框下方一个「**展开过程 / 折叠显示**」按钮，点击切换，且切换时锚定按钮位置、下方不动（呈现在框上方涨/收）。

## 安装

```sh
# 先构建（生成 lib/client.js，产物入库）
cd packages/dsh-process-fold && pnpm install && pnpm build

# 安装到 web profile（bundle 层栈，重启生效）
dsh plugin --profile web add file:./packages/dsh-process-fold
# 重启 dsh web，刷新页面
```

## 能力面

| 能力 | 说明 |
|---|---|
| 套框 | 给一轮内的思考/工具调用/上下文注入等过程项统一套方框 |
| 合并 | 相邻过程项（中间无最终文字打断）合并为一个整体框 |
| 折叠 | 框内只显示最新 2 项，更早的项 `display:none` |
| 展开/折叠切换 | 框下方「展开过程」→ 全显示；「折叠显示」→ 收回 |
| 滚动锚定 | 切换时按钮视口位置不动，只在框上方涨/收 |
| 收口 | 最终文字结果留在框外、完整显示 |
| 用户对话/提问不进框 | `user`/`steering`（用户打断重定向）与 `ask_user_question` 提问永远是边界，不框不折叠 |

## 结构

| 文件 | 作用 |
|---|---|
| `package.json` | `dsh.bundle.patch` + `dsh.client`（platform web）+ exports |
| `cordis.patch.yml` | 一行 `insert`，把插件挂进 profile 组合 |
| `index.mjs` | Node half（纯客户端 UI 插件，空 apply） |
| `src/client.ts` | 浏览器半：框 + 折叠 + 展开/折叠切换逻辑 |
| `build.mjs` | esbuild 构建（CJS + `__ModuleLoader__` 包装） |

## 插件管理

已装插件用 plugin-registry 的**薄控制台**管理（浏览器面板）：管理 profile 插件安装态（bundle 层栈 + insert 行 + 启停），无需手改配置。安装：
`dsh plugin --profile web add <plugin-registry>/packages/plugin/console`
