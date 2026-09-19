# 验证说明

本项目的测试分为三层，避免把脚本通过说成队友已经收到消息。

## 可复现的离线测试

```sh
python3 -m unittest discover -s skills/team-mailbox/tests -v
python3 -m unittest discover -s tests -v
```

15 项邮箱测试：@/指派/创建者/参与者路由、双向消息模拟、重复与编辑提醒、自身回声过滤、PR 排除、通知不带外部正文、冷却与暂停、账号切换、失败保留游标、分页预算、Hook 保留其他配置、默认不安装 Hook、Issues 关闭时拒绝初始化、已跟踪 Hook 配置保护。

4 项安装测试：重复安装不改项目规则、覆盖需显式指定且保存备份、Claude 路径及安装后脚本执行、拒绝非 Git 项目。临时目录包含空格与中文。

这些测试使用模拟 GitHub 数据和真实临时 Git 仓库，不向队友发信。CI 矩阵为 Linux / macOS / Windows × Python 3.10 / 3.13；实际结果以 [GitHub Actions](https://github.com/NikolaStarx/team-mailbox/actions) 对应提交为准。

## 发布检查

发布时在本机运行上述测试、Skill 格式校验和 Markdown 本地链接检查；公开后检查匿名 clone、远端提交一致性，以及在独立 clone 中使用本人 GitHub 身份运行 `init` / `check` / `doctor`。具体通过情况记录在 [Releases](https://github.com/NikolaStarx/team-mailbox/releases)。

## 需要团队自己验证

- 两位真实成员用各自账号双向收发，及私有仓库/附件权限。
- 各个 Agent 客户端实际发现 Skill 的效果。
- 可选 Codex Hook 经本人信任后的实际投递。
- 消息处理质量、模型结果和论文结论的正确性。

生成配置文件不等于客户端已执行；离线双向模拟不等于真人互通；手动查询成功不等于自动通知已经送达。
