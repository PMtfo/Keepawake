# KeepAwake for macOS

KeepAwake 是一款轻量级的 macOS 菜单栏防休眠工具。点击菜单栏的 ☕ 图标即可切换防休眠状态，也可以自定义防休眠时长。

本仓库是**打包产物分发库**：只提交已构建好的 `KeepAwake.app`，不含 Electron 源码工程。

## ✨ 核心功能

* **一键防休眠**：点击菜单栏的 ☕ 图标，即可切换防休眠状态。
* **定时防休眠**：支持自定义防休眠时间（以小时为单位，支持小数如 1.5 小时）。
* **合盖防休眠**：在**接通电源**的情况下，合上 Mac 盖子依然保持唤醒（系统级限制：使用电池时合盖必定休眠）。
* **倒计时显示**：在菜单栏直观显示剩余的防休眠时间。

## 🚀 快速开始

1. 将 `KeepAwake.app` 拖入到你的 **应用程序 (Applications)** 文件夹中。
2. 双击运行 `KeepAwake.app`。

### ⚠️ 首次运行提示（重要）

由于本程序未经过 Apple 开发者签名，首次打开时 macOS 可能会提示“无法打开，因为无法验证开发者”。
请按照以下步骤操作：

1. 在弹出的警告窗口中点击 **“取消”**。
2. 打开 macOS 的 **“系统设置”** -> **“隐私与安全性”**。
3. 向下滚动，找到安全性部分，你会看到一条关于 `KeepAwake.app` 被阻止的提示。
4. 点击 **“仍要打开”**。
5. 再次确认打开，之后就可以正常使用了。

## 💻 适用系统

* 架构：**Apple Silicon (M1/M2/M3/M4)** (ARM64)
* 系统：macOS 11.0 或更高版本（`LSMinimumSystemVersion` = 11.0）

## 🛠 技术细节

本工具基于 Electron 构建，底层调用 macOS 原生命令 `/usr/bin/caffeinate -i -d -s` 实现系统级防休眠。

应用元信息（取自 `KeepAwake.app/Contents/Info.plist`）：

| 项 | 值 |
|---|---|
| Bundle ID | `com.keepawake.app` |
| 版本 | `1.0.0` |
| 分类 | `public.app-category.utilities` |
| 构建 SDK | macOS 14.5 |

## 📦 仓库结构

```
.
├── KeepAwake.app/            # 已签好包的应用产物（约 87 MB，116 个文件）
│   └── Contents/
│       ├── Info.plist
│       ├── MacOS/KeepAwake           # 可执行入口
│       ├── Frameworks/               # Electron Framework 等，占绝大部分体积
│       ├── Resources/
│       │   ├── app.asar              # 应用逻辑打包文件
│       │   └── electron.icns
│       └── _CodeSignature/
├── SHA256SUMS                # 全量文件校验清单
├── .gitattributes            # Electron Framework 二进制走 Git LFS
├── .gitignore
└── README.md
```

### Git LFS

`KeepAwake.app/Contents/Frameworks/Electron Framework.framework/Versions/A/Electron Framework` 通过 Git LFS 跟踪。克隆前请确保已安装 LFS：

```bash
git lfs install
git clone git@github.com:PMtfo/Keepawake.git
```

若未装 LFS，该文件会被拉成指针文本，应用无法启动。

### 完整性校验

```bash
shasum -a 256 -c SHA256SUMS
```

`Info.plist` 中的 `ElectronAsarIntegrity` 另外记录了 `Resources/app.asar` 的 SHA256，Electron 启动时会自行校验，替换 `app.asar` 会导致启动失败。

## 备注

* 应用不需要任何网络权限即可工作。`Info.plist` 里的 `NSAppTransportSecurity` 本地例外与摄像头/麦克风/蓝牙用途描述均来自 Electron 打包模板的默认项，本工具功能上不使用这些能力。
* 只提交产物、不提交源码，因此升级方式是在构建机重新打包后整体替换 `KeepAwake.app/` 并重新生成 `SHA256SUMS`。
