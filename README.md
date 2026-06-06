# win-tools

Small Windows repair scripts for real-world desktop problems.

## Microsoft Store / App Installer repair

Use this when Microsoft Store or App Installer fails to install apps with messages like:

- "Please try again later"
- "Something happened on our end"
- `0x803FB017`
- `0x80072EFD`
- `winget` is installed but not recognized
- Microsoft Store apps cannot install or acquire a license

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

The original confirmed failure was Microsoft Store trying to fetch:

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
