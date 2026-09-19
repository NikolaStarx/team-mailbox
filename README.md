# Team Mailbox

**让不同队员的 AI Agent，通过 GitHub 协作。**

一个从 GROW.md 团队协作中提取的开源 Skill：GitHub Issues 传消息，任务文件保存约定，Pull Requests 交付成果。适合数学建模、科研与开发小队。成员可以使用不同 Agent、不同电脑和各自的 GitHub 账号，无需共享账号、模型 API Key 或搭建服务器。

它是一套轻量协作约定和查收工具，不是实时聊天、远程控制或通用 Agent RPC 标准。**工具仓库公开；你们的项目、数据和通信可以放在自己的私有 GitHub 仓库。** 不要把团队工作消息发到本工具仓库的 Issues。

## 直接交给你的 Agent

复制下面这段话给队友的 Agent，填上团队仓库和本机路径：

> 请从 https://github.com/NikolaStarx/team-mailbox 拉取 team-mailbox Skill，先阅读 README.md 和 skills/team-mailbox/SKILL.md，再将 Skill 安装到我们的项目【本机路径】。团队仓库是【OWNER/REPO】，请核对项目 origin 指向它，用我自己的 GitHub 账号初始化并查收。按当前 Agent 的技能目录安装，默认不用 Hook。以后开工和交接前查收消息；可以在我已授权的任务内回复技术问题与进度，新增任务或范围变化先问我。请保留项目已有规则和未提交修改。

## 快速安装

需要 Python 3.10+、Git、[GitHub CLI](https://cli.github.com/)。团队先建立一个启用 Issues 的 GitHub 仓库，让每个成员加入并各自克隆。已有项目直接使用。

```sh
git clone https://github.com/NikolaStarx/team-mailbox.git
cd team-mailbox
python3 scripts/install.py --project /path/to/your-team-project
```

默认安装到项目的 `.agents/skills/team-mailbox`。Claude Code 使用 `--agent claude`，安装到 `.claude/skills/team-mailbox`。安装脚本不会登录、发消息或修改 `AGENTS.md` / `CLAUDE.md`。更新时先 `git pull --ff-only`，再用同一安装命令加 `--update`，原目录会备份到项目 Git common dir。

随后进入**团队项目**，每个成员用自己的账号执行：

```sh
gh auth login --hostname github.com
gh api user --jq .login
python3 .agents/skills/team-mailbox/scripts/mailbox.py init
python3 .agents/skills/team-mailbox/scripts/mailbox.py check
```

Claude Code 将 `.agents` 换成 `.claude`；Windows 可将 `python3` 换成 `py -3`。首次安装后按客户端方式重新发现 Skill，或直接让 Agent 读取它。完整设置、可选 Codex Hook、暂停与卸载见[安装说明](skills/team-mailbox/references/setup.md)。

## 团队如何使用

1. 在团队项目维护成员 GitHub 账号与分工；可从[建模示例](examples/math-modeling/README.md)开始。
2. 一项任务一个 Issue，在正文/评论中 `@收件人`；复杂任务链接已推送的任务文件。
3. 收件人的 Agent 运行 `check`，再读 Issue 和评论原文；在本人已授权的范围内处理。
4. 回复结果、复现命令与证据链接，代码/模型/论文修改通过 PR 交付。

发送使用原生 GitHub CLI，消息正文先保存为 Markdown 文件：

```sh
gh issue create --repo OWNER/REPO --title 'Q2：比较两个模型的验证误差' --body-file draft.md
gh issue view NUMBER --repo OWNER/REPO --comments
gh issue comment NUMBER --repo OWNER/REPO --body-file reply.md
```

`OWNER/REPO`、`NUMBER` 是占位值。每人克隆同一个团队仓库，项目 `origin` 与命令的 `--repo` 保持一致。默认没有自动回信；收到消息不等于同意接手。不进行“收到/谢谢”回信循环。

## Agent 兼容范围

| 环境 | 接入方式 | 自动查收 |
| --- | --- | --- |
| Codex | `.agents/skills/team-mailbox`，使用 `$team-mailbox` 或自然语言 | 可选 `init --codex-hooks`，需客户端信任 |
| Claude Code | `.claude/skills/team-mailbox`，使用 `/team-mailbox` 或自然语言 | 默认手动 `check` |
| 其他能读文件和运行命令的 Agent | 读取同一个 `SKILL.md`，执行 Python/`gh` | 在自己的工作步骤中调用 `check` |
| 只有 GitHub 连接器的 Agent | 按 Skill 用连接器查询、读写 Issues | 不使用 Python 去重缓存 |

技能目录依据 [Codex 官方文档](https://learn.chatgpt.com/docs/build-skills)和 [Claude Code 官方文档](https://code.claude.com/docs/en/skills)。核心协议不要求某个模型；具体客户端是否自动发现或触发 Skill，需在本机验证。可选 Hook 遵循 [Codex Hooks 文档](https://learn.chatgpt.com/docs/hooks)。

## 范围与验证

Python 脚本只读取 GitHub，不发送消息；它扫描本人被 @、被指派、创建或参与的 Issues，排除 PR 和自己的发言。Hook 有 60 秒冷却和尽力去重，没有后台服务、空闲唤醒或送达保证。手动 `check` 保留最近入口，不是严格已读/未读系统。

支持 github.com 的 HTTPS/SSH origin；面向小仓库，首次会扫描历史 Issues 和评论。本机状态位于 Git common dir 中，不提交。原项目的成员、私有通讯录、凭据和聊天记录不包含在此发布中。

```sh
python3 -m unittest discover -s skills/team-mailbox/tests -v
python3 -m unittest discover -s tests -v
```

测试使用模拟 GitHub 响应和真实临时 Git 仓库；CI 在 Linux、macOS、Windows 运行。它们不证明两位真人已完成互通或某个 Agent 客户端的 Hook 已实际投递。发布检查见[验证说明](docs/VALIDATION.md)。

## English summary

Team Mailbox is an agent-neutral collaboration skill: GitHub Issues carry messages, project files record agreements, and pull requests deliver work. Each teammate uses their own GitHub identity and preferred agent. Install with `python3 scripts/install.py --project PATH` (`--agent claude` for Claude Code), then follow the skill's setup guide in your team repository. The inbox is read-only; sending uses the GitHub CLI or your agent's GitHub connector under your authorization. Codex hooks are optional. Your team repository can stay private.

## License and origin

[MIT](LICENSE). Extracted and generalized from the GROW.md team's `team-mailbox` skill, originally developed under the Vioano account; maintained here by NikolaStarx and contributors. This repository contains the reusable communication tools and documentation, not the game's code or assets.
