# OK Uptime Skill

让 Codex、Claude Code 等支持 Skill 的 AI 助手，通过 [OK Uptime](https://www.okuptime.com) CLI 添加网站监控、查询项目和监控状态、发起检测，以及按用户要求检查或升级 CLI。

Skill 直接调用 CLI 并读取 JSON，不重复实现鉴权和业务规则。创建监控与完成异步检测分别报告；发现新版只提示，用户要求更新时才安装。

## 安装

将本仓库根目录的 `SKILL.md` 保存到以下任一位置：

| AI 助手 | 文件位置 |
| --- | --- |
| Codex | `~/.codex/skills/okuptime/SKILL.md` |
| Claude Code | `~/.claude/skills/okuptime/SKILL.md` |

也可以将本仓库直接克隆到相应的 `okuptime` 目录。安装后开启新会话；若助手尚未发现 Skill，重新加载或重启助手。

CLI 尚未安装时，Skill 会按官网发布清单获取预编译包，核对平台、文件大小和 SHA256 后安装；无需自行编译 Go。官网尚未提供发布包时，会停止并说明原因。

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

官网新版 CLI 发布接口、预编译包和创建项目接口尚未部署；依赖这些能力的首次自动安装、在线升级和创建项目，需要完成官网发布后才能使用。Skill 公开发布不代表这些网站功能已上线。

Skill 与 CLI 独立维护。CLI 升级不会更新 Skill，Skill 更新也不会自动安装或升级 CLI；更新 Skill 时从本仓库获取最新的 `SKILL.md`。

- [CLI 源码：okuptime-agent](https://github.com/jicheng1014/okuptime-agent)
- [Skill 源码：okuptime-skill](https://github.com/jicheng1014/okuptime-skill)
