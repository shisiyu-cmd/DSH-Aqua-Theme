# DSH Aqua 主题

[DeepSeek Harness](https://github.com/deepseek-ai/deepseek-harness) Web 界面的毛玻璃主题，支持照片与视频壁纸、磨砂玻璃面板，文字在任何背景上都保持可读。

> **这是 fork。** 原主题来自 [WYH66666666/DSH-Transparent-UI-Plugin](https://github.com/WYH66666666/DSH-Transparent-UI-Plugin)。本 fork 修复了对 dsh `0.2.1-rc.3` 的兼容性，并重做了壁纸模式。详见[本 fork 的改动](#本-fork-的改动)。

## 特性

- **两种背景** — 动态流体面板，或你自己的照片／视频壁纸
- **全界面毛玻璃** — 左栏、顶栏、输入框、消息气泡各自独立控制透明度与模糊
- **任何照片上都可读** — 白字 + 硬黑描边 + 柔和投影，无论亮天空还是暗岩石都能分离出来
- **静态模式** — 可完全关闭动态背景，GPU 占用降到几乎等于不装主题
- **明暗双主题** — 所有模式下都可用

## 安装

需要 dsh `>=0.2.1-rc.0 <0.3.0`。

### 一键安装（推荐）

**Windows PowerShell**

```powershell
Invoke-WebRequest -Uri "https://github.com/shisiyu-cmd/DSH-Aqua-Theme/releases/latest/download/dsh-aqua-theme-1.3.1.tgz" -OutFile dsh-aqua-theme.tgz
dsh plugin --profile desktop add ./dsh-aqua-theme.tgz
```

**Linux / macOS / Git Bash**

```sh
curl -L -o dsh-aqua-theme.tgz https://github.com/shisiyu-cmd/DSH-Aqua-Theme/releases/latest/download/dsh-aqua-theme-1.3.1.tgz
dsh plugin --profile desktop add ./dsh-aqua-theme.tgz
```

安装后重启 DSH。主题入口在 **设置 → 通用 → Aqua**。

### 手动下载

1. 打开 [最新 Release](https://github.com/shisiyu-cmd/DSH-Aqua-Theme/releases/latest)
2. 在 **Assets** 里下载 `dsh-aqua-theme-1.3.1.tgz`
3. 在文件所在目录打开终端，执行：

```sh
dsh plugin --profile desktop add ./dsh-aqua-theme-1.3.1.tgz
```

> `./` 不能省。`dsh plugin add` 会转发给 pnpm，不带路径前缀会被当成 npm 包名去仓库里找。

### 从源码构建

```sh
git clone https://github.com/shisiyu-cmd/DSH-Aqua-Theme.git
cd DSH-Aqua-Theme
pnpm pack
dsh plugin --profile desktop add ./dsh-aqua-theme-1.3.1.tgz
```

## 壁纸模式的层级

启用照片或视频后，各层叠加顺序如下：

| 层 | 元素 | 不透明度 | 模糊 |
|---|---|---|---|
| 0 | 照片／视频 | 100% | — |
| — | 遮罩 | **0%（无）** | — |
| 1 | 中栏（对话区） | 0% | — |
| 2 | 左栏 | 42% | 2px |
| 3 | 顶栏 | 30% | 2px |
| 3 | 输入框 | 38% | 2px |
| 4 | 消息气泡 | 45% | 20px |

中栏全透明，让壁纸完整透出；文字直接压在照片上，靠描边保证对比度。照片和视频共用同一套规则，只有最底层不同。

## 性能

Windows 桌面实测，同一页面在 Edge 中渲染：

| 模式 | GPU（渲染进程） |
|---|---|
| 不装主题 | 6.6% |
| Aqua，流体背景动画中 | 14.7% |
| Aqua，关闭动态背景 | 约 6.7% |

开销来自那块持续动画的全屏 canvas：只要还有任何一个动画在跑，合成器就无法空闲，所以部分关闭没有效果。需要低占用就整体关掉动态背景。

视频壁纸还会额外产生视频解码开销，这与 CSS 无关。

## 本 fork 的改动

上游包面向较旧的 dsh，本 fork 修复了：

1. **`cordis.patch.yml` 没被打进发布包** — 上游声明了 `dsh.bundle.patch`，却把这个文件排除在 `files[]` 之外，导致 `dsh plugin add` 装完后无法启动。
2. **`@deepseek-ai/dsh-client-runtime` 已不存在** — `defineStore` 现在来自 `@deepseek-ai/dsh-client-store`。
3. **`IconCheckOutline16` → `IconCheckOutlineMedium`** — 图标名加上了尺寸后缀。
4. **`settings.plugin.item` → `settings.plugins.tab`** — 插件设置页改成了标签列表，每项需要 `label`。
5. **`peerDependencies` 换成 `engines.dsh`** — 旧的 peer 范围永远无法满足 `0.2.1-rc.3`，会让 dsh 在启动时禁用插件。
6. **壁纸模式重做** — 原实现让中栏透明却没有任何东西承接，视频元素还被插进了 `<html>`（盖住整个界面）。两者均已修复。
7. **开关按钮对比度** — 开启态原本是 `#6E9BE8` 压在 `#1D2C44` 上（约 2.4:1）。现改为实心强调色配白字。
8. **浅色模式整片发蓝** — 壁纸层自带深蓝底且无条件创建，同时 `html` 引用了只定义在 `body` 上的变量导致透明。两者均已修复。

## 开发说明

仓库里的 `lib/` 是编译产物。上游源码在原始仓库中；本 fork 直接修改编译后的 bundle，因此 `lib/client.js` 是手工编辑的，不是构建生成。

如果你要从源码重新构建，需要重新应用上述改动。

## 许可证

MIT。原创部分 © John Wu，本 fork 的修改以相同许可证发布。
