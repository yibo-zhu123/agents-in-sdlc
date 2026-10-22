---
title: "第 8 课 - 创建并合并功能 PR"
description: "一起审查筛选功能、指令、技能更新、QA 配置和测试，然后创建 PR 并使用 Agent Merge。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

筛选功能实现、指令更新、技能更新、质量保证（QA）配置和测试都已保存在同一个分支上。现在一起审查这些内容并创建拉取请求。这次您将使用 **Agent Merge** 管理流程，而不像之前那样亲自合并星级评分拉取请求（PR）。

> [!NOTE]
> 通常会将功能、指令更新、技能更新和 QA 智能体拆分为几个不同的 PR。为了简化本研讨会的流程，您将完整的筛选功能和质量工作流保留在同一对话和分支中，并在此 PR 中一起提交。

本课将介绍如何：

- 了解 Agent Merge 及其如何自动管理合并生命周期。
- 检查完整的功能更改和验证证据。
- 创建筛选功能 PR。
- 只在审查完成后启用 Agent Merge，并确认 PR 已合并。

## 场景

在筛选功能工作流中，您使用 Copilot 规划、实现并验证了一项功能。Tailspin Toys 现在希望自动完成剩余的 PR 工作，同时保留由开发人员控制合并授权。

## 了解 Agent Merge

**Agent Merge** 会自动处理 PR 合并所需的剩余工作。启用后，Copilot 会解决阻碍 PR 合并的问题——修复失败的持续集成（CI）检查、回复审查意见，并在需要时执行变基——然后在存储库允许时启用 GitHub 自动合并。

到目前为止，您都是亲自选择 **Merge pull request**。Agent Merge 可以接手这项工作。请先审查内容并确认已准备就绪，再启用 Agent Merge。

## 使用 Agent Merge 管理 PR

代码已全部完成。现在一起审查、创建 PR，并让 Agent Merge 管理后续流程。

1. 返回 Codespace。
2. 输入 `/agent` 打开智能体对话框。
3. 从选项列表中选择 **Default**，然后按 <kbd>Enter</kbd>。
4. 使用命令 `/pr create` 创建新 PR。
5. 使用 `/pr agentmerge` 启用 Agent Merge。
6. Copilot 会监视 PR 的持续集成流程。所有检查成功后，它会执行合并。
7. 确认看到 Copilot 发出的类似“PR #14 was squash-merged successfully.”的消息。

> [!IMPORTANT]
> Agent Merge 不会绕过必需的审批、分支保护、合并队列、存储库设置或权限限制。如果操作受阻，请查看报告的原因；在存储库允许的情况下，完成审查后手动合并。

## 总结与后续步骤

您已自动完成开发流程中的多个环节，包括生成代码、测试和验证代码，以及处理拉取请求。本课中，您：

- 了解了 Agent Merge 及其如何自动管理合并生命周期。
- 检查了完整的功能更改和验证证据。
- 创建了筛选功能 PR。
- 仅在审查完成后启用了 Agent Merge，并确认 PR 已合并。

接下来，您将[探索更多实用的 Copilot CLI 斜杠命令][next-lesson]，用于管理上下文、模型、共享内容和可选的云委派。

## 资源

- [使用 Copilot CLI 管理拉取请求][manage-prs]
- [Copilot CLI 命令参考][cli-reference]

[previous-lesson]: ../7-qa-agent/
[next-lesson]: ../9-cli-power-tools/
[manage-prs]: https://docs.github.com/copilot/how-tos/copilot-cli/use-copilot-cli/manage-pull-requests
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
