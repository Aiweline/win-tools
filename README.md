# win-tools

中文 | [English](#english)

Windows 小工具与修复脚本仓库，收录真实问题验证过的桌面修复工具。

## Microsoft Store / App Installer 修复脚本

适用于 Microsoft Store / App Installer 无法安装应用的情况，例如：

- 商店弹窗提示“请稍后重试”
- “我们这边出现了问题”
- 错误码 `0x803FB017`
- 错误码 `0x80072EFD`
- `winget` 已安装但命令无法识别
- Microsoft Store 可以打开，但安装/授权应用失败

脚本路径：

```bat
tools\fix-windows-app-installer-store.bat
```

这个脚本会处理：

- 恢复 `%LOCALAPPDATA%\Microsoft\WindowsApps` 到当前用户 `PATH`
- 清理损坏的本地 PAC 自动代理，例如 `http://127.0.0.1:<port>/commands/pac`
- 将 WinHTTP 代理重置为直连
- 启动 Store / AppX 安装相关服务
- 重注册 Microsoft Store、App Installer、Store Purchase App、Store Engagement
- 重置并更新 `winget` 源
- 使用 Spotify 的 Microsoft Store 产品 ID `9NCBCSZSJRSB` 验证商店目录访问
- 执行 `wsreset.exe` 清理商店缓存

本次已验证的故障是：Microsoft Store 安装 Spotify 时，通过本地 PAC 访问以下接口失败：

```text
https://displaycatalog.mp.microsoft.com/v7.0/products/9NCBCSZSJRSB/0010?fieldsTemplate=InstallAgent
https://licensing.mp.microsoft.com
```

失败代理地址：

```text
http://127.0.0.1:33331/commands/pac
```

最终表现为 Store 日志中的 `0x80072EFD` 和 `0x803FB017`。清理本地 PAC `AutoConfigURL` 并恢复 App Installer 路径后，安装链路恢复正常。

## 使用方式

右键脚本，以管理员身份运行：

```bat
tools\fix-windows-app-installer-store.bat
```

脚本会在桌面生成备份：

- `user-path-backup-before-store-fix.txt`
- `internet-settings-before-store-fix.reg`

## 注意事项

如果 Clash Verge、v2rayN、sing-box 或其他代理工具重新开启“系统代理 / PAC 模式”，Microsoft Store 可能再次失败。关闭对应模式后重新运行脚本即可。

## 关键词

Windows 商店修复，Microsoft Store 无法安装应用，App Installer 修复，winget 无法识别，0x803FB017，0x80072EFD，Spotify 商店安装失败，Store 授权错误，displaycatalog.mp.microsoft.com，licensing.mp.microsoft.com，Clash Verge PAC 代理导致 Windows Store 失败。

---

## English

Small Windows repair scripts for real-world desktop problems.

## Microsoft Store / App Installer repair script

Use this when Microsoft Store or App Installer fails to install apps with messages like:

- "Please try again later"
- "Something happened on our end"
- `0x803FB017`
- `0x80072EFD`
- `winget` is installed but not recognized
- Microsoft Store opens, but app installation or licensing fails

Script:

```bat
tools\fix-windows-app-installer-store.bat
```

What it fixes:

- Restores `%LOCALAPPDATA%\Microsoft\WindowsApps` to the current user's `PATH`
- Clears broken local PAC proxy configuration such as `http://127.0.0.1:<port>/commands/pac`
- Resets WinHTTP proxy to direct access
- Starts Store/AppX installation services
- Re-registers Microsoft Store, App Installer, Store Purchase App, and Store Engagement packages
- Resets and updates `winget` sources
- Validates Microsoft Store catalog access with the Spotify Store product ID `9NCBCSZSJRSB`
- Runs `wsreset.exe`

The confirmed failure was Microsoft Store trying to fetch:

```text
https://displaycatalog.mp.microsoft.com/v7.0/products/9NCBCSZSJRSB/0010?fieldsTemplate=InstallAgent
https://licensing.mp.microsoft.com
```

through a local PAC URL:

```text
http://127.0.0.1:33331/commands/pac
```

This produced Store errors `0x80072EFD` and `0x803FB017`. Clearing the local PAC `AutoConfigURL` and restoring the App Installer path fixed the install chain.

## Run

Right-click the script and run as administrator:

```bat
tools\fix-windows-app-installer-store.bat
```

The script creates backups on the Desktop:

- `user-path-backup-before-store-fix.txt`
- `internet-settings-before-store-fix.reg`

## Notes

If Clash Verge, v2rayN, sing-box, or another proxy app re-enables "System Proxy" or "PAC mode", Microsoft Store may fail again. Turn off that mode or rerun the script.

## Keywords

Windows Store fix, Microsoft Store cannot install apps, App Installer repair, winget not recognized, 0x803FB017, 0x80072EFD, Spotify Microsoft Store install failed, Store licensing error, displaycatalog.mp.microsoft.com, licensing.mp.microsoft.com, Clash Verge PAC proxy Windows Store.
