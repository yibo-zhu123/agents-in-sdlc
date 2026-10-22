---
title: "第 7 课 - 创建并使用 QA 智能体"
description: "创建 QA 自定义智能体，将议题要求、quality-checks 技能和 Playwright MCP 整合在一起。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

您已使用 `quality-checks` 技能运行自动化检查，并通过 Playwright MCP 在浏览器中查看筛选功能。现在，将这些能力整合到自定义智能体中，并为其定义清晰的 QA 流程。

本课将介绍如何：

- 探索自定义智能体如何结合指令、技能和 MCP 工具。
- 创建并检查可复用的质量保证配置。
- 选择 QA 智能体，并根据筛选议题审查其发现。

## 场景

在创建拉取请求（PR）前，Tailspin Toys 希望以一致的方式检查需求、代码质量、自动化检查、测试覆盖率和浏览器行为。自定义智能体可以协调 QA 流程并生成可复用的报告。

## 什么是自定义智能体？

自定义智能体是在 Markdown 配置文件中定义的 Copilot 专用版本。配置文件会说明智能体的用途、指令和可用工具。本研讨会将在 `.github/agents/qa.agent.md` 中定义 QA 角色，并在 Copilot CLI 中选择它。

您使用的自定义功能各有不同用途：存储库指令描述团队规范；`quality-checks` 技能封装可重复执行的检查；Playwright MCP 提供浏览器工具；QA 配置则告诉 Copilot 如何使用这些能力来评估需求并报告结果。QA 配置不会取代这些功能，也不需要开启另一个对话。

## 创建 QA 配置

创建功能 PR 前，让 Copilot 创建可复用的 QA 配置。该配置会定义 QA 要执行的检查及其必须遵守的边界。

1. 返回 Codespace，并确认筛选功能对话处于 Interactive 模式。
2. 让默认智能体创建新的自定义智能体：

   ```plaintext
   Create a custom agent named QA in .github/agents/qa.agent.md. It should check features against their issues and agreed requirements, follow the repository instructions, run the quality-checks skill, use Playwright MCP to verify behavior, and add tests when coverage is missing.

   Have it report each requirement as pass, fail, or blocked with supporting evidence. It must ask before changing implementation code, and it must not commit changes or open pull requests.

   Just create the profile for now so I can review it.
   ```

## 检查配置

使用新智能体前，请检查它的配置，确认 Copilot 已记录预期的 QA 工作流和边界。这可以避免配置不完整或范围过宽，导致智能体在您只希望验证功能时修改了代码。

1. 输入 `/diff` 并打开 `.github/agents/qa.agent.md`。
2. 阅读 frontmatter。`description` 为必填项；`name` 是可选项，但填写后会为智能体提供清晰的显示名称。
3. 阅读配置指令，确认 QA 会从需求出发、遵循存储库指令、运行 `quality-checks` 技能，并使用 Playwright MCP。
4. 确认 QA 会报告支持证据、在修改实现代码前征求同意，并且不会提交更改或创建拉取请求。
5. 如果生成的配置遗漏上述职责或边界，请让默认智能体先进行修订。

## 根据议题运行 QA

Copilot CLI 会在对话启动时加载项目智能体。创建配置后，恢复同一个筛选功能对话，再选择 QA，这样它就能使用现有对话中的议题和规划决定。

1. 使用以下提示启用智能体：

   ```plaintext
   /agent QA
   ```

> [!NOTE]
> 由于刚刚创建了该智能体，它可能还不会显示在可用智能体列表中，但仍可通过上述命令启用。

2. 使用以下提示，让 QA 智能体审查功能：

   ```plaintext
   Review the filtering feature against the issue and the decisions in our plan. Is it ready for a PR?
   ```

3. QA 智能体会开始工作！
4. 完成后阅读它提供的报告。

## 总结与后续步骤

您已在工作流中添加可复用的专家角色，并审查了它的工作。本课中，您：

- 探索了自定义智能体如何结合指令、技能和 MCP 工具。
- 创建并检查了可复用的质量保证配置。
- 选择了 QA 智能体，并根据筛选议题审查了它的发现。

现在，您已准备好实现、技能更新、QA 配置、测试和验证报告。接下来，您将[创建功能 PR 并使用 Agent Merge][next-lesson]。

## 资源

- [为 Copilot CLI 创建自定义智能体][create-agents]
- [自定义智能体配置][agent-config]

[previous-lesson]: ../6-mcp-playwright/
[next-lesson]: ../8-create-pull-request/
[create-agents]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/create-custom-agents-for-cli
[agent-config]: https://docs.github.com/copilot/reference/custom-agents-configuration
