# AI2048

一个用**原生 HTML / CSS / JavaScript** 写的 2048 合成小游戏，零依赖、单文件、可直接双击玩，另附 **Electron 桌面版** 打包方案。

> 玩法致敬 Gabriele Cirulli 的 [2048](https://github.com/gabrielecirulli/2048)（MIT），本项目的实现与视觉为独立重写。

---

## 特性

| 分类 | 说明 |
| --- | --- |
| 棋盘 | 4×4，开局 2 个方块（90% 出 2、10% 出 4） |
| 操作 | `↑↓←→`、`WASD`、鼠标拖动、手机触摸滑动 |
| 合并规则 | 同值碰撞相加；**每块一次移动只合并一次**（不会连锁）；无效移动不生成新块 |
| 计分 | 合并得分累加；最高分写入 `localStorage`（键 `ai2048.best`） |
| 撤销 | 可回退一步（`Z` 键 / 按钮），不额外生成方块 |
| 胜负 | 达成 2048 弹出提示并可继续冲 4096+；无路可走时自动结算 |
| 视觉 | 深色主题，11 档数值配色，2048 金色发光、更高值红色发光 |
| 动画 | 新块缩放淡入、合并块回弹、滑动 `transform` 过渡 |
| 适配 | 棋盘宽度自适应，`touch-action: none` 防止手机滑动时页面跟着滚 |

## 快速开始

### 1. 只想玩（无需任何环境）

双击 `index.html`，或把它拖进任意浏览器。

### 2. 桌面版（Electron）

```bash
npm install
npm start
```

### 3. 打包成 exe

```bash
npm run dist      # 当前平台 → dist/AI2048-win32-x64/AI2048.exe
npm run dist:all  # 全平台（需要对应系统环境）
```

产物是**绿色目录版**：整个 `dist/AI2048-win32-x64/` 文件夹即为应用，拷贝即可运行。

想要**单个 exe 文件**（自解压便携版），推荐用 `electron-builder`：

```bash
npm i -D electron-builder
npx electron-builder --win portable
```

`electron-builder` 的 `portable` 目标会产出一个自解压单文件 exe（约 80–150 MB），首次运行释放到临时目录。

### 下载加速（中国大陆）

Electron 二进制走 npmmirror：

```bash
set ELECTRON_MIRROR=https://npmmirror.com/mirrors/electron/
npm install
```

## 玩法

- 每次移动后，所有方块向该方向压缩；相邻同值合并为它们之和。
- 合并产生的方块该回合不再参与合并。
- 目标：合成 **2048**。之后可以继续挑战 4096 / 8192……
- 棋盘填满且四个方向都无法移动时游戏结束。

## 目录结构

```
AI2048/
├─ index.html      # 游戏本体（HTML + CSS + JS 全部内联，可单独运行）
├─ main.js         # Electron 主进程入口
├─ package.json    # 脚本与依赖
├─ docs/           # 截图
├─ LICENSE         # MIT
└─ README.md
```

游戏逻辑全部在 `index.html` 底部的 IIFE 中，状态（`grid` / `score` / `best` / `snapshot`）与渲染（`render()`）分离：

- `move(dir)` — 单次移动的全部逻辑：遍历、合并、补块、记撤销点
- `render(opts)` — 把 `grid` 映射成绝对定位的方块 DOM，含合并"幽灵块"动画
- `undo()` — 按快照重建棋盘
- `hasMoves()` — 死局判定

## 常见问题

**Q：命令行启动 Electron 版直接闪退，无窗口？**

检查环境变量 `ELECTRON_RUN_AS_NODE` 是否被设置。它会让 Electron 以纯 Node 模式运行，主进程 `require('electron')` 拿不到 `app`，于是直接退出：

```
TypeError: Cannot read properties of undefined (reading 'whenReady')
```

清除该变量即可：`set ELECTRON_RUN_AS_NODE=`（Windows CMD）或 `Remove-Item Env:ELECTRON_RUN_AS_NODE`（PowerShell）。

**Q：`npm install electron` 秒装完但没有二进制？**

部分 npm 版本会跳过 postinstall。手动补：

```bash
node node_modules/electron/install.js
```

## 贡献

欢迎 Issue 与 PR。提交前请确保：

1. 网页版仍然零依赖（不引 CDN、不引第三方库）；
2. 按住方向键快速连按时合并逻辑不重复；
3. 手机端滑动不触发页面滚动。

## 许可证

[MIT](LICENSE) © 2025 cloud-114514
