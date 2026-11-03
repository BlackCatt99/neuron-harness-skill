# Agent installation guide / agent 安装指引

This is the continuation for the one-line clone shown on the repository homepage. Cloning downloads
the skill only; it does not install dependencies, activate the skill or modify a project.

首页那一行 `git clone` 只下载 skill。它不安装依赖、不激活 skill，也不修改项目。

## Before changing directory / 切目录前

1. Save the business project's absolute path from the current workspace or an explicit user path.
   Do not scan the disk and never use this cloned repository as the business project.
2. Check `git` and `pwsh` 7. Report a missing dependency; do not install it or elevate privileges
   silently.
3. Identify the host only from reliable session metadata. Supported values are `zcode`, `codex`,
   `claude`, `cursor` and `generic`. Do not infer it from installed executables or the mere presence
   of `AGENTS.md` or `CLAUDE.md`. Ask one focused question only if the host or project remains
   ambiguous.
4. Choose the final skill directory before initialization. Keep it outside the business project.
   Use a known host skill path only when that host and path are verified; otherwise keep the stable
   checkout and pass its absolute path as `-SkillRoot`. If the relative clone landed inside the
   project, move the complete checkout before continuing. Never move it after integration, overwrite
   an existing checkout, or edit global configuration.

1. 先保存业务项目的绝对路径。不要扫盘，也不要把本仓库初始化成业务项目。
2. 检查 Git 与 PowerShell 7。缺依赖就报告，不擅自安装或提权。
3. 只依据可靠的会话信息识别 `zcode`、`codex`、`claude`、`cursor` 或 `generic`。不能凭本机程序或规则文件猜测；仍不确定时只问一个必要问题。
4. 初始化前确定最终 skill 目录，并放在业务项目外。只有宿主路径已核实时才使用标准 skill 路径，否则保留稳定检出并通过 `-SkillRoot` 接入。相对克隆落在项目内时，先移动完整检出再继续。接入后不得再移动，不覆盖已有目录，不改全局配置。

## Install / 安装

Run `Status` before `Init` when `.neuron-harness/integration/install-state.json` exists. If it is
already installed and consistent, stop. If the transaction is incomplete or the recorded harness,
roots or hook setting conflict, stop and explain `Recover`; do not force a reinstall.

已有安装记录时，先用记录中的 harness、项目根、skill 根和 hook 设置运行 `Status`。配置一致就停止；事务未完成或配置冲突时停止并说明 `Recover`，不要强制重装。

```powershell
pwsh -NoProfile -File <skill-root>/scripts/assemble.ps1 -Mode Init -Project <project-root>
pwsh -NoProfile -File <skill-root>/scripts/integrate.ps1 -Action Plan -Harness <harness> -Project <project-root> -SkillRoot <skill-root>
pwsh -NoProfile -File <skill-root>/scripts/integrate.ps1 -Action Install -Harness <harness> -Project <project-root> -SkillRoot <skill-root>
pwsh -NoProfile -File <skill-root>/scripts/integrate.ps1 -Action Status -Harness <harness> -Project <project-root> -SkillRoot <skill-root>
```

`Plan` writes nothing, but it is not a complete preflight. Inspect its output, then install. An
explicit request to install and enable project integration authorizes `Init` and the installer's
managed project-rule block only. A request merely to clone does not. Do not pass
`-EnablePromptHook` unless the user explicitly asks for Claude prompt capture.

`Plan` 不写文件，但不是完整预检。查看输出后再安装。明确要求“安装并接入项目”时，只授权 `Init` 和安装器自己的项目规则托管块；只要求克隆则没有写权限。只有用户明确要求 Claude 自动记录原文时才加 `-EnablePromptHook`。

## Finish safely / 安全收尾

- Require the text `STATUS: installed; configuration consistent`. A zero exit code is not enough.
  Status never proves live execution.
- Verify `.neuron-harness/` is not tracked. If the project uses Git and has no exclusion, add
  `.neuron-harness/` to the project's ignore rules without removing tracked history from the index.
- Read `.neuron-harness/runtime.md` and continue the current session from it. Rule loading in a new
  session still requires a new-session check.
- When the host permits, validate with one clearly fictional message. Otherwise report:
  “configuration installed; live execution unverified.”

- 成功文本必须包含 `STATUS: installed; configuration consistent`；退出码 0 不够，也不能证明真实会话已执行。
- 确认 `.neuron-harness/` 未被 Git 跟踪。项目使用 Git 且尚无排除规则时，把该目录加入项目忽略规则；已跟踪历史不自动移出索引。
- 当前会话读取 `.neuron-harness/runtime.md` 后继续。规则是否被新会话加载，必须另开一次会话验证。
- 宿主允许时用一条明确虚构的消息验证；否则如实报告“配置已安装，真实执行未验证”。
