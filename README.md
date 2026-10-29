# DSH Aqua Theme

A glassmorphism theme for the [DeepSeek Harness](https://github.com/deepseek-ai/deepseek-harness) Web UI, with photo and video wallpapers, frosted-glass panels, and text that stays readable on any background.

> **This is a fork.** The original theme is [WYH66666666/DSH-Transparent-UI-Plugin](https://github.com/WYH66666666/DSH-Transparent-UI-Plugin). This fork fixes compatibility with dsh `0.2.1-rc.3` and rebuilds wallpaper mode. See [Changes](#changes-in-this-fork).

## Features

- **Two backdrop modes** 鈥?a live fluid board, or your own photo / video wallpaper
- **Frosted glass everywhere** 鈥?sidebar, header, composer and message bubbles each get their own translucency and blur
- **Readable on any photo** 鈥?white text with a hard dark outline plus a soft drop shadow, so glyphs separate from both bright skies and dark rock
- **Static mode** 鈥?the animated backdrop can be turned off entirely; GPU use drops to roughly the level of running no theme at all
- **Light and dark palettes** 鈥?both work in every mode

## Install

Requires dsh `>=0.2.1-rc.0 <0.3.0`.

### One command (recommended)

**Linux / macOS / Git Bash**

```sh
curl -L -o dsh-aqua-theme.tgz https://github.com/shisiyu-cmd/DSH-Aqua-Theme/releases/latest/download/dsh-aqua-theme-1.3.1.tgz
dsh plugin --profile desktop add ./dsh-aqua-theme.tgz
```

**Windows PowerShell**

```powershell
Invoke-WebRequest -Uri "https://github.com/shisiyu-cmd/DSH-Aqua-Theme/releases/latest/download/dsh-aqua-theme-1.3.1.tgz" -OutFile dsh-aqua-theme.tgz
dsh plugin --profile desktop add ./dsh-aqua-theme.tgz
```

Restart DSH afterwards. The theme appears under **Settings → General → Aqua**.

### Manual download

1. Open the [latest release](https://github.com/shisiyu-cmd/DSH-Aqua-Theme/releases/latest)
2. Download `dsh-aqua-theme-1.3.1.tgz` from **Assets**
3. Open a terminal in the folder that contains the file and run:

```sh
dsh plugin --profile desktop add ./dsh-aqua-theme-1.3.1.tgz
```

> The `./` matters. `dsh plugin add` forwards to pnpm, which treats a bare name as a registry package.

### Build from source

```sh
git clone https://github.com/shisiyu-cmd/DSH-Aqua-Theme.git
cd DSH-Aqua-Theme
pnpm pack
dsh plugin --profile desktop add ./dsh-aqua-theme-1.3.1.tgz
```

## Wallpaper mode

When a photo or video is active, the layers stack like this:

| Layer | Element | Opacity | Blur |
|---|---|---|---|
| 0 | photo / video | 100% | 鈥?|
| 鈥?| scrim | **0%** (none) | 鈥?|
| 1 | centre column (chat) | 0% | 鈥?|
| 2 | sidebar | 42% | 2px |
| 3 | header | 30% | 2px |
| 3 | composer | 38% | 2px |
| 4 | message bubbles | 45% | 20px |

The centre column is fully transparent so the wallpaper reads through; text sits directly on the photo and relies on its outline for contrast. Photo and video share one rule set 鈥?only the bottom layer differs.

## Performance

Measured on a Windows desktop, rendering the same page in Edge:

| Mode | GPU (renderer) |
|---|---|
| No theme | 6.6% |
| Aqua, fluid backdrop animating | 14.7% |
| Aqua, animated backdrop disabled | ~6.7% |

The cost is the continuously animating full-screen canvas: any single animation keeps the compositor busy, so partial removal does not help. Turn the backdrop off for the low-GPU mode.

Video wallpapers additionally cost video-decode time, independent of CSS.

## Changes in this fork

The upstream package targets an older dsh. This fork fixes:

1. **`cordis.patch.yml` was missing from the published tarball** 鈥?upstream listed `dsh.bundle.patch` but omitted the file from `files[]`, so `dsh plugin add` installed a package that could not boot.
2. **`@deepseek-ai/dsh-client-runtime` no longer exists** 鈥?`defineStore` now comes from `@deepseek-ai/dsh-client-store`.
3. **`IconCheckOutline16` 鈫?`IconCheckOutlineMedium`** 鈥?icons gained size suffixes.
4. **`settings.plugin.item` 鈫?`settings.plugins.tab`** 鈥?the plugins settings page became a tab list and each contribution needs a `label`.
5. **`peerDependencies` replaced by `engines.dsh`** 鈥?the old peer ranges never satisfied `0.2.1-rc.3`, which made dsh disable the plugin at startup.
6. **Wallpaper mode rebuilt** 鈥?the previous implementation left the chat column transparent with nothing behind it, and the video element was appended to `<html>` (covering the entire UI). Both fixed.
7. **Toggle button contrast** 鈥?the ON state was `#6E9BE8` on `#1D2C44` (鈮?.4:1). Now a filled accent pill with white text.

## Development

The shipped `lib/` is compiled output. The upstream source lives in the original repository; this fork patches the built bundle directly, so `lib/client.js` is hand-edited rather than generated.

If you rebuild from source, re-apply the changes above.

## License

MIT. Original work 漏 John Wu; fork modifications released under the same licence.
