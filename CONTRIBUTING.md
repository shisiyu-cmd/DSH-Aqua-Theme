# 贡献指南

## 提交前

这个仓库的 `lib/` 是**手工修改的编译产物**，不是从源码构建的。改动前请先看 [README 的开发说明](README.md#development)。

## 报告问题

请附上：

1. **dsh 版本** — `dsh --version`
2. **模式** — 流体 / 照片壁纸 / 视频壁纸，浅色或深色
3. **现象** — 具体哪块看不见、看不清，最好附截图
4. **控制台报错** — 如果按 F12 能看到错误

## 修改 `lib/client.js`

文件里所有 CSS 都塞在几个很长的字符串常量里（`css$1`、`css$2`、`css$3`）。改的时候注意：

- **不要在 CSS 字符串里直接用双引号**，会截断 JS 字符串。属性选择器写成 `[data-x=y]` 这种不带引号的形式，或者用转义。
- 改完**必须**验证语法：

```sh
node -e "new Function('window', require('fs').readFileSync('lib/client.js','utf8'))"
```

- 改完在隔离 profile 里实测，不要直接改用户的 profile：

```sh
$env:DSH_HOME = "<临时目录>"
dsh --profile web --port 52001 --no-open
```

## 已知的坑

这些是开发过程中踩过的，记下来避免重复：

| 现象 | 原因 |
|---|---|
| 装了插件 DSH 打不开 | `package.json` 被写成带 BOM 的 UTF-8，dsh 的 `JSON.parse` 不接受 |
| 视频壁纸盖住整个界面 | `<video>` 被插进 `<html>`，因为 `[data-dsh-aqua-wallpaper]` 同时匹配 `<html>` |
| 改了 CSS 没效果 | 同优先级规则排在后面覆盖了；或者选择器要求了不存在的祖先元素 |
| 图片壁纸下文字消失 | 中栏透明但没有承接层，文字直接压在照片上 |
| canvas 读不到图片像素 | 跨域图片会污染 canvas，`getImageData` 抛异常 |

## 风格

- CSS 改动优先用 `color-mix()` 和 `backdrop-filter`，保持和现有代码一致
- 数值改动请只改数字，不要顺手重构
- 提交信息用中文或英文都可以，说清改了什么、为什么
