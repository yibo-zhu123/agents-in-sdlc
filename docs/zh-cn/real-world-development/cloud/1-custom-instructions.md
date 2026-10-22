---
title: "练习 1 - 自定义指令（云智能体）"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 上一课：先决条件][previous-lesson] · [下一课：GitHub Copilot 云智能体 →][next-lesson]

上下文对于生成式 AI 至关重要。如果任务需要遵循特定方式，或者 Copilot 需要了解背景信息，就应确保 Copilot 能访问相关上下文。[指令文件][instruction-files]可提供这些指导，让 Copilot 不仅了解您希望它完成**什么**，还了解应当**如何**完成。

本练习将介绍如何：

- 探索项目上下文、编码指南和文档标准如何通过存储库自定义指令及按路径限定的指令文件传递给 Copilot。
- 在 `.github/copilot-instructions.md` 中添加存储库级标准。

> [!NOTE]
> 与 VS Code 和 CLI 学习路径不同，本练习不会进行更改前后的提示对比。Copilot 云智能体会在 GitHub 议题上异步工作，因此难以实时并排展示效果。后续审查云智能体创建的拉取请求时，可以看到指令文件的影响。

## 指令文件

### 场景

Tailspin Toys 制定了一套开发实践指南和要求，其中包括：

- 数据层始终需要单元测试。
- UI 应采用深色模式，且外观现代。
- 应使用 TSDoc 文档注释为代码添加文档。
- 应在每个文件开头添加一段注释，说明文件的用途。

通过指令文件，您可以确保 Copilot 了解这些实践，并据此完成任务。

### 自定义指令

自定义指令可向 Copilot 提供上下文和偏好，帮助它更好地理解您的编码风格和要求。这项功能可以引导 Copilot 提供更相关的建议和代码片段。您可以指定偏好的编码规范、库以及注释风格，也可以为整个存储库或特定文件类型创建指令。

指令文件有两种：

- `.github/copilot-instructions.md`：单个指令文件，会随存储库的**每个**请求发送给 Copilot。该文件应包含项目级信息，例如适用于大多数 Copilot Chat 或 CLI 请求的上下文、技术栈、项目概述、最佳实践和全局指导。
- `.github/instructions/*.instructions.md`：可为特定语言或文件类型（例如 TypeScript 或 Astro）以及创建 UI 组件或单元测试等任务提供指导。

> [!NOTE]
> 在 IDE 中，指令文件仅用于 Copilot Chat 生成代码，不用于代码补全或下一步编辑建议。
>
> Copilot Chat、Copilot CLI 和 Copilot 云智能体在生成代码时，都会使用存储库级指令和带有 `applyTo` frontmatter 的 `*.instructions.md` 文件。
>
> Copilot 还[支持其他标准的指令文件][custom-instructions-support]，包括 AGENTS.md 和 CLAUDE.md。

### 管理指令文件的最佳实践

本研讨会不涵盖如何完整创建指令文件，但示例项目展示了有代表性的方法。总体而言：

- 让 `copilot-instructions.md` 专注于项目级指导，例如项目用途、项目结构和全局编码标准。
- 使用 `*.instructions.md` 文件为文件类型（单元测试、Astro 组件、数据层）或特定任务提供指导。
- 使用自然语言，让指导清晰易懂，并提供代码应当如何编写或不应如何编写的示例。

编写指令文件没有唯一正确的方法，使用 AI 也是如此。通过试验可以找到最适合项目的方法。

> [!TIP]
> 每个使用 GitHub Copilot 的项目都应该有完善的指令文件集合。浏览当前项目的指令文件时，您会发现其中涵盖了多种任务，例如 [UI 更新][ui-instructions]和 [Astro][astro-instructions]。
>
> Copilot 也可以帮您生成指令文件。不同界面中的入口各不相同，例如 VS Code 中的 **Configure Chat → Generate Agent Instructions**，或 Copilot CLI 中的 `/init`。对应界面的课程会在相关部分说明。
>
> 需要模板或起点？可以查看 [awesome-copilot][awesome-copilot]，其中包含指令文件、自定义智能体和其他资源。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## 探索此项目中的自定义指令文件

花些时间阅读存储库附带的指令文件，其中包括一个核心 `copilot-instructions.md` 文件和一组用于不同任务的 `*.instructions.md` 文件。请在编辑器或 GitHub Web UI 中打开这些文件。

1. 打开 `.github/copilot-instructions.md`。
2. 浏览文件，注意其中简短的项目说明，以及 **Agent notes**、**Code standards**、**Scripts** 和 **Repository Structure** 等部分。注意 **Code standards** 下的 **GitHub Actions Workflows** 指导。这些内容适用于您与 Copilot 的所有交互。
3. 打开 `.github/instructions` 文件夹并浏览其中内容。注意其中包括 Astro 文件、Drizzle 数据层、测试等方面的指令。
4. 打开 `.github/instructions/unit-tests.instructions.md`。查看顶部的 `applyTo` 字段；它设置了一个相对于存储库根目录的 glob，用于确定指令适用的文件。例如，所有符合 `**/*.test.ts` 的 TypeScript 测试文件都会匹配。
5. 查看项目中特定的单元测试编写说明。
6. 最后打开 `.github/instructions/drizzle.instructions.md` 并滚动到文件底部。查看它指向的其他指令文件（例如 `unit-tests.instructions.md`）和项目现有文件。这样可以将大型指令集拆分成更小的可复用文件，并指引 Copilot 参考现有项目示例。此处路径相对于指令文件，而不是存储库根目录。

> [!NOTE]
> `copilot-instructions.md` 中的 **Code formatting requirements** 部分记录了项目编码规范，但尚未要求在代码中添加文档。接下来的步骤会添加 TSDoc 文档注释和文件注释头的规则。

## 添加存储库标准

接下来，您将进行本练习中唯一的编辑：添加一条项目级规则，要求在代码中使用 TSDoc 文档注释和文件注释头。稍后在本学习路径中分配议题后，云智能体会使用这条规则。

编辑前，请创建并切换到一个分支（云智能体会从议题目标分支读取指令，但仍应整洁地提交更改）：

1. 在 codespace 终端中运行以下命令：

   ```bash
   git checkout -b custom-instructions-cloud
   ```

如前所述，`.github/copilot-instructions.md` 用于向 Copilot 提供项目级信息。现在记录存储库编码规范，以改进代码建议。

1. 重新打开 `.github/copilot-instructions.md`。
2. 找到 **Code formatting requirements** 部分，通常位于第 27 行附近。注意其中记录了项目编码规范，但尚未规定代码文档要求，因此生成的辅助函数还没有文档注释。
3. 在现有规范下方添加以下 Markdown 内容，要求 Copilot 为文件添加注释头和 TSDoc 文档注释：

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. 保存 `copilot-instructions.md`。

> [!TIP]
> 如上一课所述，可以在存储库级别（`.github/copilot-instructions.md`）创建适用于全局的指令文件，也可以使用 `*.instructions.md` 文件为特定语言、文件类型或任务提供指导。存储库级文件适合存放项目级规范，例如刚添加的文档注释规则。

## 提交、推送并合并指令更新

云智能体会读取议题目标分支中的指令。下一练习将把议题分配给 Copilot，而 Copilot 会从 `main` 创建分支，因此必须先将指令更改合并到 `main`，云智能体才能使用它们。

1. 暂存并提交：

   ```bash
   git add .github/copilot-instructions.md
   git commit -m "Add doc comment and file-header standards to copilot instructions"
   ```

2. 推送分支：

   ```bash
   git push -u origin custom-instructions-cloud
   ```

3. 在 github.com 上创建从 `custom-instructions-cloud` 指向 `main` 的拉取请求并合并。之后，云智能体处理下一练习分配的议题时就会读取这些指令。

> [!TIP]
> 如果希望在本次研讨会中直接使用 `main`，可以跳过分支步骤并直接向 `main` 提交。创建分支是为了模拟真实项目中的工作方式。

## 总结与后续步骤

您已了解 Copilot 如何读取项目中的指令文件，并在 `.github/copilot-instructions.md` 中添加了存储库级标准。接下来审查云智能体创建的拉取请求时，就能看到这条标准的作用。

接下来，将第一个议题[分配给 Copilot 云智能体][next-lesson]。

## 资源

- [用于自定义 GitHub Copilot 的指令文件][instruction-files]
- [创建自定义指令的最佳实践][instructions-best-practices]
- [编写更好的 Copilot 自定义指令的 5 个技巧][copilot-instructions-five-tips]
- [Awesome Copilot — 指令文件及其他资源集合][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-cloud-agent/
[instruction-files]: https://docs.github.com/copilot/customizing-copilot/about-customizing-github-copilot-chat-responses
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
