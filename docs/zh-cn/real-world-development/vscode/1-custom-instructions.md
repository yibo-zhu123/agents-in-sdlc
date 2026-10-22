---
title: "练习 1 - 自定义指令（VS Code）"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 上一课：先决条件][previous-lesson] · [下一课：智能体模式 →][next-lesson]

上下文对于生成式 AI 至关重要。如果任务需要遵循特定方式，或者 Copilot 需要了解背景信息，就应确保 Copilot 能访问相关上下文。您可以通过多种方式向 Copilot 提供特定上下文，其中一种重要方式是使用[指令文件][instruction-files]来指导代码生成。

本练习将介绍如何：

- 探索项目上下文、编码指南和文档标准如何通过存储库自定义指令及按路径限定的指令文件传递给 Copilot。
- 在当前指令生效的情况下，生成筛选功能的第一个数据切片（发行商辅助函数）。
- 在 `.github/copilot-instructions.md` 中添加存储库级标准。
- 再次发送相同提示，观察生成的代码如何遵循新标准。
- 提交指令更新和筛选功能切片到 `main`，以便 Copilot 在下一练习中使用更新后的指导。

> [!CAUTION]
> 生成的代码可能不完全符合您设置的规范。Copilot 的结果并非确定。此练习旨在观察更新指令后行为的**趋势变化**，而不是要求输出逐字一致。

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
- `.github/instructions/*.instructions.md`：可为特定语言（例如 TypeScript 或 Astro）、文件类型或任务（例如创建 UI 组件或一组新单元测试）提供指导。

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

## 为更改创建分支

现在按照最佳实践为更改创建分支。

1. 返回上一练习使用的 codespace。
2. 按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux）打开新终端。
3. 创建并切换到新分支：

   ```bash
   git checkout -b custom-instructions
   ```

## 更新指令前先使用 Copilot Chat

> [!TIP]
> **打开 Copilot Chat**
>
> 开始以下练习前，返回 codespace，打开 Copilot Chat 面板，然后选择 **New Chat** 开始全新对话。每个练习的模式和模型选择可能不同；相关步骤会在适用时说明。

要了解自定义指令的影响，先在当前指令文件生效的情况下发送提示。稍后更新指令后，再发送相同提示并比较结果。

1. 关闭之前练习中打开的编辑器标签页，让 Copilot 只读取所需上下文。
2. 打开 `src/lib/publishers.ts`，让 Copilot 知道辅助函数应放在哪里。
3. 在 Chat 视图的智能体下拉菜单中选择 **Agent**，以便 Copilot 可以修改文件。

   ![Chat 视图中的智能体选择器截图。](../../../_images/shared-chat-mode-selector.png)

4. 发送以下提示：

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

5. Copilot 会探索项目并应用代码更新，通常会同时修改辅助函数文件和测试文件。
6. 注意建议的辅助函数是类型化函数：它以 `db` 客户端作为第一个参数，并返回类型化的发行商数组。这来自 `.github/instructions/drizzle.instructions.md` 中的数据层规范，该文件适用于 `src/lib/*.ts`。
7. 注意建议的代码**缺少** TSDoc 文档注释和文件注释头。

> [!CAUTION]
> Copilot 的结果具有概率性，因此即使没有明确要求，它也可能添加文档注释。如果发生这种情况也没关系；本练习要观察的是更新指令后行为的一致性变化。

## 添加存储库标准

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

## 重新发送提示并观察变化

1. 返回 Copilot Chat 并选择 **New Chat** 清除对话内容。
2. 再次选择 `src/lib/publishers.ts`，让 Copilot 聚焦于正确的文件。
3. 发送与之前**完全相同**的提示：

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

4. 注意现在生成的文件会以类似以下内容的注释块开头：

   ```typescript
   /**
    * Publisher data-access helpers for the Tailspin Toys Crowd Funding platform.
    * Provides functions to retrieve publisher information from the database.
    */
   ```

5. 注意生成的函数现在包含类似以下内容的 TSDoc 注释：

   ```typescript
   /**
    * Returns a list of all publishers with their id and name.
    *
    * @param db - The Drizzle database client.
    * @returns A promise that resolves to an array of publisher objects.
    */
   ```

现在，您已引导 Copilot 遵循新的项目标准，并将其应用于后续练习会继续使用的实际代码。

## 提交指令并推送分支

指令文件和存储库中的其他资源一样，也应使用相同的源代码管理流程。现在提交并推送此分支。

1. 按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux）打开新终端窗口。
2. 在终端中运行以下命令，确认指令更新和辅助函数更改已准备就绪：

   ```bash
   git status
   ```

3. 在终端中暂存并提交指令更新，以及 Copilot 为辅助函数基础所做的所有更改：

   ```bash
   git add .github/copilot-instructions.md src/lib/publishers.ts
   # 如果 git status 显示其他支持性更改（例如测试），也要暂存这些文件。
   git commit -m "Add doc comment standards and publishers helper foundation"
   ```

4. 在终端中将分支推送到存储库：

   ```bash
   git push -u origin custom-instructions
   ```

## 创建并合并拉取请求

分支推送完成后，应创建拉取请求，并将其关联到待办事项中的文档标准议题。您可以手动操作，不过 Copilot 也可以使用已连接到项目的 GitHub 工具帮您完成。让 Copilot 查找议题、创建用于关闭该议题的 PR，然后合并 PR。

> [!NOTE]
> 这是第一次让 Copilot 代表您在 GitHub 上执行操作。项目模板已连接 GitHub 工具，无需额外设置。Copilot 首次使用工具时，VS Code 可能会提示您登录 GitHub。按照提示授权即可。后续练习将介绍此连接的工作原理（模型上下文协议）。

1. 在 codespace 中打开 Copilot Chat。
2. 按 <kbd>Control</kbd>+<kbd>Command</kbd>+<kbd>I</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>I</kbd>（Windows/Linux）打开 Copilot Chat 视图，并确认在智能体选择器中选择了 **Agent**。
3. 让 Copilot 查找更新指令文件的议题，并根据当前分支创建 PR，同时说明指令更新和新增的发行商辅助函数基础：

   ```
   Find the issue related to updating the instructions file. Create a new PR from the current branch, highlight that the PR closes that issue, and include that we also added the publishers helper foundation for the upcoming filtering work.
   ```

4. Copilot 会开始查找议题并创建 PR。
5. Copilot 请求代表您执行 GitHub 操作时，请审查命令，并在适当时选择 **Allow**。
6. 创建 PR 后，使用以下提示让 Copilot 合并 PR，并将分支切回 `main`：

   ```
   Merge the PR into main. Then return to main locally, and pull the latest code so we are up to date.
   ```

7. Copilot 请求代表您执行 GitHub 操作和 shell 命令时，请审查命令，并在适当时选择 **Allow**。

现在，您已在 GitHub Copilot 的帮助下创建并合并了拉取请求！

## 总结与后续步骤

您了解了 Copilot 如何读取指令文件，并使用 VS Code 中的 Copilot Chat 完成以下工作：

- 发送代码生成提示，并观察现有指令下 Copilot 的输出。
- 在 `.github/copilot-instructions.md` 中添加存储库级标准。
- 再次发送相同提示，观察生成的代码如何遵循新标准。
- 将指令更新和筛选功能切片提交到 `main`，供下一练习使用。

接下来，Copilot 将使用这些指令跨代码库添加新功能；请在[智能体模式][next-lesson]中继续学习。

## 资源

- [用于自定义 GitHub Copilot 的指令文件][instruction-files]
- [创建自定义指令的最佳实践][instructions-best-practices]
- [编写更好的 Copilot 自定义指令的 5 个技巧][copilot-instructions-five-tips]
- [GitHub Copilot 个人自定义指令][personal-instructions]
- [Awesome Copilot — 指令文件及其他资源集合][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-agent-mode/
[instruction-files]: https://code.visualstudio.com/docs/copilot/copilot-customization
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[personal-instructions]: https://docs.github.com/copilot/customizing-copilot/adding-personal-custom-instructions-for-github-copilot
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
