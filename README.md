# OK Uptime Skill

让 Codex、Claude Code 等支持 Skill 的 AI 助手，通过 [OK Uptime](https://www.okuptime.com) CLI 添加网站监控、查询项目和监控状态、发起检测，以及按用户要求检查或升级 CLI。

Skill 直接调用 CLI 并读取 JSON，不重复实现鉴权和业务规则。创建监控与完成异步检测分别报告；发现新版只提示，用户要求更新时才安装。

## 安装

将整个仓库（包括 `SKILL.md` 和 `scripts/`）安装到以下任一 Skill 目录；不能只复制 `SKILL.md`：

| AI 助手 | 文件位置 |
| --- | --- |
| Codex | `~/.codex/skills/okuptime/` |
| Claude Code | `~/.claude/skills/okuptime/` |

也可以将本仓库直接克隆到相应的 `okuptime` 目录。安装后开启新会话；若助手尚未发现 Skill，重新加载或重启助手。

加载 Skill 后直接要求 AI 添加网站即可。CLI 尚未安装时，AI 会运行仓库附带的安装器，识别 macOS、Linux 或 Windows 及 amd64/arm64 架构，获取并校验官网预编译包后安装；无需编译 Go、管理员权限或配置 PATH。首次仍需在本地配置账号 Token。

| 系统 | 安装器 | 用户目录 |
| --- | --- | --- |
| macOS / Linux | `sh scripts/install.sh` | `~/.local/bin/okuptime` |
| Windows | `powershell.exe -NoProfile -ExecutionPolicy Bypass -File scripts/install.ps1` | `%LOCALAPPDATA%\OKUptime\bin\okuptime.exe` |

安装器不覆盖已有 CLI；官网暂无对应发布或校验失败时停止。Windows 的执行策略参数仅影响本次进程。AI 会使用可执行文件绝对路径，无需用户额外修改 PATH。

## 一次性账号配置

在 OK Uptime 网站的“个人资料 → API 访问令牌”创建 Token，再在本机运行：

```sh
okuptime config set-token
```

CLI 隐藏读取并保存凭证。不要将 Token 发到聊天、写入命令参数、Skill 或仓库。自动化环境也可通过 `OKUPTIME_TOKEN` 提供凭证。

## 使用示例

- “把 https://example.com 加入公司项目的监控。”
- “将这几个网站加入 OK Uptime，并汇总已存在和添加失败的项目。”
- “查看这个网站最近的监控状态。”
- “重新检测监控 12，告诉检测结果。”
- “检查 OK Uptime CLI 有没有新版本。”
- “更新 OK Uptime CLI。”

## 发布状态与更新

官网提供 macOS、Linux 与 Windows 的 amd64、arm64 预编译 CLI 包。Skill 根据官网发布清单安装；首次配置账号后即可管理网站监控。通过 `okuptime update --check` 检查版本，主动运行 `okuptime update` 安装新版，保留账号配置和旧二进制。Windows 下载验签后暂存新版，再由 AI 同步执行 CLI 返回的替换命令，保留旧版；完成后核对版本。

Skill 与 CLI 独立维护。CLI 升级不会更新 Skill，仅更新 Skill 不会升级已有 CLI；首次使用且未安装时会自动安装。更新 Skill 时从本仓库获取完整目录。

- [CLI 源码：okuptime-agent](https://github.com/jicheng1014/okuptime-agent)
- [Skill 源码：okuptime-skill](https://github.com/jicheng1014/okuptime-skill)
