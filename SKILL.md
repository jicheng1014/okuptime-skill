---
name: okuptime
description: 通过 OK Uptime CLI 将网站加入账号的监控项目，查询项目、监控及检测结果，发起重新检测，并检查或按用户要求升级 CLI。用户提到 OK Uptime、添加网站监控或批量导入网站时使用。
---

# OK Uptime

使用 `okuptime` CLI 操作用户自己的 OK Uptime 账号。所有业务调用使用 `--json`，按退出状态及 `error.code` 判断结果；不要复制 API 鉴权和业务逻辑。

## 安装与账号

先查找 `okuptime`，也检查 `~/.local/bin/okuptime`；后者存在但不在 PATH 时直接使用绝对路径。不要读取或输出已保存的 Token。

没有 CLI 时，从官网安装预编译二进制，无需 Go：

1. 确定本机平台和架构：macOS 对应 `darwin`，Linux 对应 `linux`；x86_64 对应 `amd64`，aarch64/Apple Silicon 对应 `arm64`。
2. 请求 `https://www.okuptime.com/api/v1/cli/releases/latest?platform=PLATFORM&architecture=ARCH`，读取 `data` 中的 `url`、`size`、`sha256`、`version`。
3. 只接受同官网主机的 HTTPS 下载地址，拒绝跳转；先下载到临时文件，限制 64 MiB，核对实际大小与 SHA256，全部吻合后安装到 `~/.local/bin/okuptime`，权限 `0755`。使用现有下载和校验工具，不使用 `curl | sh`，不覆盖已有可执行文件。
4. 运行绝对路径的 `okuptime version --json` 确认版本。官网无发布、平台不支持或校验失败时停止安装，明确报告原因。

首次安装通过官网 HTTPS 和发布校验值建立信任；后续 `okuptime update` 还会用二进制内置公钥验证发布签名。

首次授权需用户在 [OK Uptime](https://www.okuptime.com) 的“个人资料 → API 访问令牌”创建 Token，并在本机执行 `okuptime config set-token`。该命令隐藏读取并保存凭证。不要要求用户把 Token 发到聊天中，不放进命令参数、日志、Skill 或仓库。自动化环境已有 `OKUPTIME_TOKEN` 时直接复用。

鉴权失败时提示配置或更新凭证；不要反复重试无效 Token。默认连接官网；仅在用户明确指定环境时使用 `OKUPTIME_BASE_URL`。

## 添加网站

用户要求添加网站即授权本次添加，不必再次确认同一操作。保持用户指定的网址、端口、项目、描述和监控间隔，不擅自购买套餐或更改通知设置。

```sh
okuptime project list --json
okuptime project create '网站监控' --json
okuptime project monitors 1 --json
okuptime monitor add --project-id 1 --description '首页' https://example.com --json
```

- 指定项目时先根据列表解析 ID；只有一个项目时直接使用；多个项目且归属不明时询问用户，不默默使用最早的项目。
- 没有项目时为本次网站添加创建“网站监控”项目；用户指定新项目名时使用该名称。
- 每次添加显式传 `--project-id`。指定间隔时使用 `--interval 秒`；没有指定时保留服务端默认值。
- 多个网站逐个添加；列表有 `next_cursor` 时用 `--after-id` 翻页。`project monitors ID` 按项目查询，`monitor list` 查询账号全部监控。
- `duplicate_monitor` 记为已存在，不删除重建。重复判定按同项目的域名和端口，不把同域名不同路径误报成新增监控。
- `validation_failed` 或套餐配额不足时报告具体失败原因；`rate_limited` 时有限退避，仍失败则保留已完成项并报告剩余项。
- 网络超时可能已经创建成功。重试前查询目标项目，避免把未知结果直接当成失败。

添加成功后汇总新增、已存在和失败的网站及项目。监控创建与检测完成分开表述；添加已触发首次异步检测，不再额外调用 `monitor check`。

## 查询与重新检测

```sh
okuptime monitor list --json
okuptime monitor show 1 --json
okuptime monitor check 1 --json
```

用户要求重新检测时再使用 `monitor check`。提交成功只是任务已接受；随后通过 `monitor show` 读取 `check_status`、`uptime_status` 和检测时间。需要等待时每 5 秒查一次、最多等待 60 秒，并遵守接口限流；仍在检测则报告监控 ID 和当前状态，不声称网站正常。

## CLI 更新

```sh
okuptime version --json
okuptime update --check --json
okuptime update --json
```

发现新版先提示可更新及版本号；只有用户要求安装或更新 CLI 时执行 `update`。普通业务命令的新版提示在 stderr，stdout 仍是 JSON。版本检查无需 Token，检查失败不能阻断网站操作。

升级失败保留旧版本并报告原因，不自动使用 sudo。源码开发构建缺少发布公钥时不能原位升级，应安装官网签名构建。升级不修改账号配置。此 Skill 在 [okuptime-skill](https://github.com/jicheng1014/okuptime-skill) 独立维护；CLI 升级不会更新 Skill，需要另行从该仓库更新 Skill。
