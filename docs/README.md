# XPhoto 下载哪个安装包？

> **一句话：Windows 电脑选 `win-x64`，苹果电脑选和芯片对应的 `dmg`，Linux 选 `AppImage`；拿不准就按下面的表对一次。**

适用于 **v1.1.10**。所有安装包在同一页：<https://github.com/autoaiai/XPhoto/releases/latest>

## 对照表

| 你的设备 | 怎么判断 | 下载这个文件 |
| --- | --- | --- |
| Windows 电脑（绝大多数） | 普通台式机或笔记本，处理器不是骁龙 | `XPhoto-1.1.10-win-x64.exe` |
| Windows 骁龙本 | 处理器是 Snapdragon / 标注 ARM64 | `XPhoto-1.1.10-win-arm64.exe` |
| 苹果电脑（M 系列芯片） | 苹果菜单 → 关于本机 → 芯片含 M1 / M2 / M3 / M4 | `XPhoto-1.1.10-mac-arm64.dmg` |
| 苹果电脑（Intel 芯片） | 关于本机 → 芯片写 Intel | `XPhoto-1.1.10-mac-x64.dmg` |
| Linux 电脑 | Ubuntu / Deepin / Fedora 等，x86_64 芯片 | `XPhoto-1.1.10-linux-x64.AppImage` |
| Linux ARM64 电脑 | 芯片为 aarch64 / ARM64 | `XPhoto-1.1.10-linux-arm64.AppImage` |

## 只有这几种情况才需要选别的

- **Mac 提示「已损坏 / 无法打开」**：打开「终端」，执行 `xattr -cr /Applications/XPhoto.app`，再双击打开。这是未签名应用的正常提示，不是文件坏了。
- **Linux 双击没反应**：在终端执行 `chmod +x XPhoto-1.1.10-linux-x64.AppImage`，再双击运行。
- **企业批量部署 / 用包管理器**：才需要 `tar.gz` 或 `deb`（与上面那些包在同一页）。普通用户不用管。
- **文件名带 `UNSIGNED` 的 zip**：给测试用的，普通客户不要下载。

## 安装后

- Windows / Linux AppImage 会自动更新：后台下载新版，下次启动（AppImage 是重启）时生效。
- Mac 的 dmg、Linux 的 deb / tar.gz 不会自动更新，应用内会提示你到下载页拿新版。

---

[← 返回仓库首页](../README.md)
