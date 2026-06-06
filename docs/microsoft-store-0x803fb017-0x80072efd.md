# Fix Microsoft Store errors 0x803FB017 and 0x80072EFD

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
