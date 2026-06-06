# 修复 Microsoft Store 错误 0x803FB017 / 0x80072EFD

中文 | [English](#fix-microsoft-store-errors-0x803fb017--0x80072efd)

## 症状

Microsoft Store 可以打开，但安装应用失败，弹窗类似：

```text
请稍后重试
我们这边出现了问题
```

本次确认案例中，Spotify (`9NCBCSZSJRSB`) 无法从 Microsoft Store 安装。

## 已确认日志

Microsoft Store 事件日志中出现：

```text
Exception thrown trying to install 9NCBCSZSJRSB. 0x803FB017
Failure detected: 0x803fb017
Failure detected: 0x80072efd
Failed to connect to https://displaycatalog.mp.microsoft.com/... with proxy config url http://127.0.0.1:33331/commands/pac
Failed to connect to https://licensing.mp.microsoft.com with proxy config url http://127.0.0.1:33331/commands/pac
```

这说明 Store 安装流程已经启动，但目录和授权请求被路由到了一个不可用的本地 PAC 代理。

## 根因

当前用户的 Windows Internet Settings 中存在本地 PAC 自动代理：

```text
HKCU\Software\Microsoft\Windows\CurrentVersion\Internet Settings
AutoConfigURL = http://127.0.0.1:33331/commands/pac
```

本次确认的监听进程是 Clash Verge：

```text
C:\Program Files\Clash Verge\clash-verge.exe
```

Microsoft Store 通过这个 PAC 请求目录和授权接口失败。界面只显示通用“稍后重试”，但事件日志能看到 `0x80072EFD` 和 `0x803FB017`。

## 修复方式

运行：

```bat
tools\fix-windows-app-installer-store.bat
```

脚本会：

1. 备份用户 PATH 和 Internet Settings。
2. 移除指向 `127.0.0.1` 或 `localhost` 的本地 PAC `AutoConfigURL`。
3. 设置 `ProxyEnable=0` 和 `AutoDetect=0`。
4. 重置 WinHTTP 代理。
5. 恢复 WindowsApps 执行别名路径。
6. 启动 Store / AppX 相关服务。
7. 重注册 Store / App Installer 相关包。
8. 重置并更新 winget 源。
9. 使用 Spotify 产品 ID 验证 Microsoft Store 目录访问。
10. 执行 `wsreset.exe`。

## 手动验证

运行脚本后执行：

```powershell
winget --version
winget show --id 9NCBCSZSJRSB --source msstore --accept-source-agreements
Get-ItemProperty -Path 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet Settings' |
  Select-Object ProxyEnable, AutoConfigURL, AutoDetect
```

预期：

```text
AutoConfigURL 为空
ProxyEnable 为 0
winget 可以查询 msstore 源
Microsoft Store 可以重新安装应用
```

## 如果问题复发

关闭代理工具里的“系统代理 / PAC 模式”，然后重新运行脚本。有些代理工具会在修复后重新写入 `AutoConfigURL`。

---

# Fix Microsoft Store errors 0x803FB017 / 0x80072EFD

## Symptoms

Microsoft Store opens but app installation fails with a modal like:

```text
Please try again later
Something happened on our end
```

In the confirmed case, Spotify (`9NCBCSZSJRSB`) failed to install from Microsoft Store.

## Confirmed log evidence

Recent Store logs showed:

```text
Exception thrown trying to install 9NCBCSZSJRSB. 0x803FB017
Failure detected: 0x803fb017
Failure detected: 0x80072efd
Failed to connect to https://displaycatalog.mp.microsoft.com/... with proxy config url http://127.0.0.1:33331/commands/pac
Failed to connect to https://licensing.mp.microsoft.com with proxy config url http://127.0.0.1:33331/commands/pac
```

That means the Store install service could reach the install flow, but catalog/licensing requests were routed through a broken local PAC proxy.

## Root cause

The Windows Internet Settings key had `AutoConfigURL` set to a local PAC endpoint:

```text
HKCU\Software\Microsoft\Windows\CurrentVersion\Internet Settings
AutoConfigURL = http://127.0.0.1:33331/commands/pac
```

The listener belonged to Clash Verge in the confirmed case:

```text
C:\Program Files\Clash Verge\clash-verge.exe
```

Microsoft Store then failed to fetch catalog and license information. The UI surfaced a generic retry message, while the event log showed `0x80072EFD` and `0x803FB017`.

## Fix

Run:

```bat
tools\fix-windows-app-installer-store.bat
```

The script:

1. Backs up user PATH and Internet Settings.
2. Removes local PAC `AutoConfigURL` values that point to `127.0.0.1` or `localhost`.
3. Sets `ProxyEnable=0` and `AutoDetect=0`.
4. Resets WinHTTP proxy.
5. Restores the WindowsApps alias path.
6. Starts Store/AppX services.
7. Re-registers Store/App Installer packages.
8. Resets and updates winget sources.
9. Validates the Microsoft Store catalog using the Spotify product ID.
10. Runs `wsreset.exe`.

## Manual verification

After running the script:

```powershell
winget --version
winget show --id 9NCBCSZSJRSB --source msstore --accept-source-agreements
Get-ItemProperty -Path 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet Settings' |
  Select-Object ProxyEnable, AutoConfigURL, AutoDetect
```

Expected:

```text
AutoConfigURL is empty
ProxyEnable is 0
winget can query the msstore source
Microsoft Store can install apps again
```

## If the problem returns

Disable "System Proxy" or "PAC mode" in the proxy app, then rerun the script. Some proxy tools can write `AutoConfigURL` again after the repair.
