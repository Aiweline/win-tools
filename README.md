# win-tools

中文 | [English](#english)

`win-tools` 是一个 Windows 小工具仓库，用来收录真实问题验证过的修复脚本、排障脚本和桌面维护工具。

仓库首页只维护工具列表和仓库说明。每个工具的详细症状、错误码、日志、根因、修复步骤和验证方式，放在工具自己的文档里。

## 工具列表

| 工具 | 脚本 | 详细文档 | 适用问题 | 状态 |
| --- | --- | --- | --- | --- |
| Microsoft Store / App Installer 修复工具 | [`tools/fix-windows-app-installer-store.bat`](tools/fix-windows-app-installer-store.bat) | [`docs/microsoft-store-0x803fb017-0x80072efd.md`](docs/microsoft-store-0x803fb017-0x80072efd.md) | Microsoft Store 无法安装应用、App Installer 异常、`winget` 无法识别、`0x803FB017`、`0x80072EFD`、本地 PAC 代理导致 Store 授权/目录请求失败 | 已实机验证 |

## 仓库目标

- 收集可重复使用的 Windows 修复脚本。
- 给每个工具配套中英文说明，必要时补充更多语言。
- 保留真实错误码、关键日志和验证命令，方便搜索和复现。
- 尽量使用可审计的 BAT / PowerShell 实现。
- 避免收录密钥、token、cookie、个人隐私或机器专属敏感信息。

## 使用方式

1. 从工具列表选择对应工具。
2. 先阅读工具的详细文档。
3. 按文档说明以管理员身份运行脚本。
4. 运行后按文档中的验证命令确认问题是否解决。

## 维护原则

- 根 `README.md` 只做仓库级说明和工具索引。
- 单个工具的详细说明放在 `docs/` 下。
- 脚本放在 `tools/` 下。
- 每个工具文档至少包含中文和英文。
- 错误码、服务名、注册表路径、URL、产品 ID 等关键信息应保留原文。

## 仓库关键词

Windows 小工具，Windows 修复脚本，Microsoft Store 修复，App Installer 修复，winget 修复，Windows 代理 PAC 问题，Windows 桌面排障工具。

---

## English

`win-tools` is a Windows utilities repository for repair scripts, troubleshooting scripts, and desktop maintenance tools that were validated against real problems.

The root README is only for the repository overview and tool index. Each tool's detailed symptoms, error codes, logs, root cause, fix steps, and verification commands live in that tool's own documentation.

## Tool List

| Tool | Script | Documentation | Problems | Status |
| --- | --- | --- | --- | --- |
| Microsoft Store / App Installer repair tool | [`tools/fix-windows-app-installer-store.bat`](tools/fix-windows-app-installer-store.bat) | [`docs/microsoft-store-0x803fb017-0x80072efd.md`](docs/microsoft-store-0x803fb017-0x80072efd.md) | Microsoft Store cannot install apps, App Installer issues, `winget` not recognized, `0x803FB017`, `0x80072EFD`, local PAC proxy breaking Store catalog/licensing requests | Tested on a real machine |

## Repository Goals

- Collect reusable Windows repair scripts.
- Provide Chinese and English documentation for each tool, with more languages when useful.
- Preserve real error codes, key logs, and verification commands for searchability and reproducibility.
- Prefer auditable BAT / PowerShell implementations.
- Avoid secrets, tokens, cookies, private personal data, or sensitive machine-specific data.

## Usage

1. Pick a tool from the tool list.
2. Read the tool's documentation first.
3. Run the script as administrator when instructed.
4. Use the verification commands in the documentation to confirm the fix.

## Maintenance Rules

- Root `README.md` is for repository-level overview and tool index only.
- Tool-specific details belong under `docs/`.
- Scripts belong under `tools/`.
- Each tool document should include at least Chinese and English.
- Keep error codes, service names, registry paths, URLs, and product IDs in their original form.

## Repository Keywords

Windows tools, Windows repair scripts, Microsoft Store fix, App Installer repair, winget repair, Windows PAC proxy issue, Windows desktop troubleshooting tools.
