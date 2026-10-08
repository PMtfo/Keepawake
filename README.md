# KeepAwake for macOS

**Agent 待机电脑** —— 轻量级 macOS 菜单栏防休眠工具。点菜单栏 ☕ 图标即可切换防休眠，也可以自定义时长。

> 关键词：agent 待机电脑

## 它解决什么问题

跑长任务的时候，Mac 一睡，Agent 就断线了 —— 远端连接掉、后台脚本卡住、下载中断、SSH 会话超时。系统自带的「防止显示器休眠」不够用：合盖照样睡，定时也没有。

KeepAwake 就是给这类场景用的常驻小工具：

- **一键防休眠** —— 点菜单栏 ☕ 图标即可切换。
- **定时防休眠** —— 支持自定义时长（小时为单位，支持小数，如 `1.5` 表示 1 小时 30 分钟）。
- **合盖防休眠** —— **接通电源**时合上盖子仍保持唤醒（系统级限制：仅用电池时合盖必定休眠）。
- **倒计时显示** —— 菜单栏直接显示剩余时间，比如 `☕ 1:29:58`。

## 快速开始

1. 把 `KeepAwake.app` 拖进 **应用程序（Applications）** 文件夹。
2. 双击运行。

### 首次运行提示（重要）

应用未经过 Apple 开发者签名，首次打开 macOS 会提示「无法验证开发者」：

1. 在弹出的警告窗口点 **「取消」**。
2. 打开 **系统设置 → 隐私与安全性**。
3. 向下滚动到安全性部分，能看到一条关于 `KeepAwake.app` 被阻止的提示。
4. 点 **「仍要打开」**，再次确认。

之后就能正常使用了。

## 使用

| 操作 | 效果 |
| --- | --- |
| 单击菜单栏 ☕ | 开启 / 关闭无限期防休眠 |
| 右键 → 「无限期防休眠」 | 一直保持唤醒，直到手动关闭 |
| 右键 → 「定时防休眠…」 | 弹出输入框，填小时数（支持小数） |
| 右键 → 「退出 KeepAwake」 | 关闭防休眠并退出 |

菜单顶部会实时显示状态：`✅ 保持唤醒中 剩余 1:29:58` 或 `⬜ 当前允许正常休眠`。

## 适用系统

| 项 | 值 |
| --- | --- |
| 架构 | **Apple Silicon（M1/M2/M3/M4）**，arm64 |
| 系统 | macOS 11.0 或更高 |
| Bundle ID | `com.keepawake.app` |
| 版本 | 1.0.0 |

## 技术细节

基于 Electron 构建，底层调用 macOS 原生命令实现系统级防休眠：

```bash
/usr/bin/caffeinate -i -d -s
```

- `-i` 阻止空闲时系统休眠
- `-d` 阻止显示器休眠
- `-s` 阻止合盖休眠（**仅接通电源时有效**）

定时模式会额外追加 `-t <秒数>`。

应用不需要任何网络权限。`Info.plist` 里的 `NSAppTransportSecurity` 本地例外与摄像头 / 麦克风 / 蓝牙用途描述均来自 Electron 打包模板的默认项，本工具功能上不使用这些能力。

## 仓库结构

本仓库是**打包产物分发库**：只提交构建好的 `KeepAwake.app`，不含 Electron 源码工程。

```
.
├── KeepAwake.app/            已签名的应用产物
│   └── Contents/
│       ├── Info.plist
│       ├── MacOS/KeepAwake           可执行入口
│       ├── Frameworks/               Electron Framework 等（占绝大部分体积）
│       ├── Resources/
│       │   ├── app.asar              应用逻辑打包
│       │   └── electron.icns
│       └── _CodeSignature/
├── SHA256SUMS                全量文件校验清单
├── .gitattributes            Electron Framework 二进制走 Git LFS
└── README.md
```

### Git LFS

`KeepAwake.app/Contents/Frameworks/Electron Framework.framework/Versions/A/Electron Framework` 由 Git LFS 跟踪，克隆前请先装 LFS：

```bash
git lfs install
git clone https://github.com/PMtfo/Keepawake.git
```

没装 LFS 的话该文件会被拉成指针文本，应用无法启动。

### 完整性校验

```bash
shasum -a 256 -c SHA256SUMS
```

`Info.plist` 里的 `ElectronAsarIntegrity` 另外记录了 `Resources/app.asar` 的 SHA256，Electron 启动时会自行校验 —— 替换 `app.asar` 会导致启动失败。

## 升级方式

只提交产物、不提交源码，所以升级是在构建机重新打包后整体替换 `KeepAwake.app/` 并重新生成 `SHA256SUMS`。

## 安全说明

- 应用完全离线运行，不发起任何网络请求。
- 仓库内只有构建产物与校验清单，**不含任何源码工程、凭据或个人数据**。
- 应用使用 ad-hoc 签名（非 Apple 开发者签名），所以首次打开需要手动放行。

## License

MIT
