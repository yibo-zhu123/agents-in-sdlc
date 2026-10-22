---
title: "第 4 课 - 使用自定义指令指导 Copilot"
description: "探索存储库指令、添加文档规范，并将其应用于筛选功能代码。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

上下文对于生成式 AI 至关重要。如果任务需要遵循特定方式，您应确保 Copilot 能获取相关指导。[指令文件][instruction-files]不仅说明您需要什么代码，还说明代码应如何组织。现在筛选功能已完成，您将探索 Copilot 使用的指令，添加文档规范，并将其应用于代码。

本课将介绍如何：

- 探索存储库指令和按路径限定的指令文件如何传递给智能体。
- 更新指令文件，确保遵循编码规范。
- 了解指令文件对代码的影响。

## 场景

Tailspin Toys 制定了一套开发实践指南和要求，其中包括：

- 注释应说明意图和不明显的决策，而不是重复代码内容。
- `db/` 和 `src/lib/` 中导出的函数应使用 TSDoc/JSDoc 记录用途、参数和返回值；如果存在可注入的 `db` 参数，也应加以说明。
- 可复用的 Astro 组件应记录其 `Props` 接口，并在相关代码更改时更新注释。
- 应保留现有格式和 lint 指引。

通过指令文件，您可以确保 Copilot 了解这些实践，并据此完成任务。

## 指令文件

自定义指令可向 Copilot 提供上下文和偏好，帮助它更好地理解您的编码风格和要求。这项功能可以引导 Copilot 提供更相关的建议和代码片段。您可以指定偏好的编码规范、库以及注释风格，也可以为整个存储库或特定文件类型创建指令。

指令文件有两种：

- `.github/copilot-instructions.md`：单个指令文件，会随存储库的**每个**请求发送给 Copilot。该文件应包含项目级信息，例如适用于大多数 Copilot Chat 或 CLI 请求的上下文、技术栈、项目概述、最佳实践和全局指导。
- `.github/instructions/*.instructions.md`：可为特定语言、文件类型或任务提供指导。

> [!NOTE]
> 其他指令格式及其支持情况因环境而异。采用特定格式前，请查看[自定义指令支持参考][custom-instructions-support]。

## 探索此项目中的自定义指令文件

入门项目已包含一些指令文件。先看看现有内容，再进行更改，以了解其效果。

1. 返回 Codespace。
2. 在 Codespaces 编辑器（而不是终端）中打开 `.github/copilot-instructions.md`。
3. 查看项目简介和编码指导。这些指令适用于 Copilot 在此存储库中的每次交互。
4. 打开 `.github/instructions` 文件夹并浏览其中内容。注意其中包括 Astro 文件、Drizzle 数据层、测试等方面的指令。
5. 打开 `.github/instructions/unit-tests.instructions.md`。查看顶部的 `applyTo` 字段；它设置了一个 glob，用于确定指令适用的文件。
6. 打开 `.github/instructions/drizzle.instructions.md`，查看其中指向其他指令文件和项目文件的链接。这样可以将较大的指令集拆分成更小、可复用的文件，并指引 Copilot 参考现有项目示例。

## 更新指令文件以符合团队规范

现有文件是不错的起点，但仍有不足。修改核心 `copilot-instructions.md` 文件，确保新生成的 TypeScript 代码包含 TSDoc 注释。

1. 在 `.github/copilot-instructions.md` 中找到 **Code formatting guidance** 部分，通常位于第 35 行附近。
2. 在最后一个项目后添加以下内容：

   ```markdown
   - All new TypeScript should contain TSDocs comments for documentation purposes.
   ```

文件会自动保存！

## 使用更新后的指导

Copilot CLI 会在对话启动时加载存储库指令。保存更新后的指令后，退出并恢复筛选功能的对话，这样 Copilot 就能在保留功能上下文的同时加载新指导。

1. 如果筛选功能的对话仍处于打开状态，请输入 `/exit` 关闭它。Copilot CLI 会自动保存对话。
2. 在存储库根目录的 `game-filters-cli` 分支中恢复已保存的对话：

   ```bash
   copilot --resume
   ```

   如果出现提示，请选择筛选功能对话，并确认检出的是 `game-filters-cli` 分支。
3. 让 Copilot 应用更新后的指导：

   ```plaintext
   We just updated our instructions and code guidance. Can you please update the code you generated to match that guidance?
   ```

4. 输入 `/diff` 并查看已更改的 TypeScript 文件。注意新生成的 TSDoc 注释，并确认它们准确描述代码。

## 总结与后续步骤

您已了解 Copilot CLI 如何读取指令文件，并将新规范应用于功能代码。具体来说，您：

- 探索了存储库指令和按路径限定的指令文件如何传递给智能体。
- 更新了指令文件，确保遵循编码规范。
- 查看了指令文件对代码的影响。

接下来，您将[自定义并运行可复用的 quality-checks 技能][next-lesson]，确保 lint 和测试运行方式一致。

## 资源

- [为 Copilot CLI 添加自定义指令][instruction-files]
- [自定义指令支持][custom-instructions-support]
- [创建自定义指令的最佳实践][instructions-best-practices]
- [Awesome Copilot — 指令文件及其他资源集合][awesome-copilot]

[instruction-files]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions
[instructions-best-practices]: https://docs.github.com/copilot/concepts/prompting/response-customization#writing-effective-custom-instructions
[awesome-copilot]: https://awesome-copilot.github.com/
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support
[previous-lesson]: ../3-agent-modes/
[next-lesson]: ../5-agent-skills/
