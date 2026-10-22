---
title: "第 9 课 - GitHub Copilot CLI 斜杠命令"
description: "探索用于管理上下文、选择模型、共享会话和可选云端委派的斜杠命令。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

与其他优秀的 CLI 工具一样，GitHub Copilot CLI 提供了许多斜杠命令。这些命令可用于访问高级功能、了解幕后信息以及配置其他选项。您已经使用过 `/diff`、`/mcp`、`/skills`、`/agent` 和 `/pr` 等命令。接下来探索一些其他实用命令。

本课将介绍如何：

- 使用 `/context` 和 `/compact` 探索 Copilot 如何管理对话上下文。
- 使用 `/model` 查看可用模型。
- 了解如何使用 `/share` 导出或共享会话。
- 探索用于并行工作、工作树和委派给云智能体的可选命令。

## 场景

您已完成 CLI 核心工作流。现在了解一些其他功能——管理上下文、切换模型、共享会话，以及选择性地将任务委派给 [Copilot 云智能体][about-cloud-agent]。

## 探索 Copilot CLI 上下文

处理较大或较复杂的任务时，可能会达到模型的最大上下文窗口。Copilot CLI 会在需要时自动压缩对话，您也可以通过斜杠命令检查或手动压缩上下文。

1. 返回 Codespace；如果 Copilot CLI 尚未打开，请从存储库根目录启动。
2. 输入：

   ```plaintext
   /context
   ```

3. 查看模型、当前 token 用量，以及上下文如何分配给系统指令、工具、消息和空闲空间。
4. 压缩对话：

   ```plaintext
   /compact
   ```

5. 再次输入 `/context` 并比较结果。如果对话本来就很短，变化可能不明显。

> [!NOTE]
> Copilot CLI 会在上下文窗口即将填满时自动压缩内容。需要自行选择时机时，可以使用 `/compact`。如果要切换到无关任务并开始全新对话，请使用 `/clear` 或 `/new`。

## 选择模型

不同模型各有优势，开发人员也有不同偏好。Copilot CLI 可以列出并选择要使用的模型。

1. 输入：

   ```plaintext
   /model
   ```

2. 查看可用模型及其用量信息。
3. 保留当前模型、选择其他模型，或按 <kbd>Esc</kbd> 关闭列表。

## 共享会话

团队协作和分享经验有助于每个人更好地使用 AI 工具。`/share` 命令可以将会话导出为 Markdown 或 HTML 文件、创建可共享链接，或发布 GitHub gist。

1. 输入 `/help`，查看您所用版本中 `/share` 的选项。
2. 如果要共享此会话，请选择适合的目标，例如使用 `/share file` 导出本地 Markdown 文件。
3. 将导出内容发送给他人或发布之前，请先审查。会话导出内容可能包含提示、回复和项目详情。

发布链接或 gist 是可选操作。不要发布团队不打算公开的存储库或对话内容。

## 可选：扩展并行工作或委派任务

本研讨会的核心部分已完成。Copilot CLI 还提供了适用于较大任务的命令：

- `/fleet` 可以将彼此独立的子任务分配给子智能体并行执行。
- `/worktree` 可以为独立任务创建隔离的 Git 工作树。
- `/delegate` 可以将任务发送给 Copilot 云智能体，由它异步处理并可能创建拉取请求。

这些命令是可选的，因为它们可能会创建额外的工作树或远程任务。尝试之前，请先开始一个范围明确的新任务，并通过常规工作流审查结果。若要深入了解异步智能体工作，请继续学习[云智能体研讨会][cloud-workshop]。

## 总结与后续步骤

使用 Copilot CLI 斜杠命令可以进行配置、共享会话并查看后台情况。本课中，您：

- 使用 `/context` 和 `/compact` 探索了 Copilot 如何管理对话上下文。
- 使用 `/model` 查看了可用模型。
- 了解了如何使用 `/share` 导出或共享会话。
- 探索了用于并行工作、工作树和云端委派的可选命令。

Copilot CLI 还有更多斜杠命令和功能值得探索！接下来，我们将[回顾所学内容][next-lesson]，并了解后续学习建议。

## 资源

- [Copilot CLI 命令参考][cli-reference]
- [Copilot CLI 中的上下文管理][context-management]
- [关于 Copilot 云智能体][about-cloud-agent]

[previous-lesson]: ../8-create-pull-request/
[next-lesson]: ../10-review/
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[context-management]: https://docs.github.com/copilot/concepts/agents/copilot-cli/context-management
[about-cloud-agent]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-workshop]: ../../cloud/
