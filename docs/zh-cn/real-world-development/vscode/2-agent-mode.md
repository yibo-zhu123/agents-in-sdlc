---
title: "练习 2 - 使用 Copilot 智能体模式添加新功能"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

即使是最简单的应用更新，通常也需要修改多个文件并执行测试等操作。作为开发人员，您通常需要找到所有相关文件、进行更改、运行测试、调试、发现遗漏的文件、再次修改……工作似乎永无止境。

这正是 Copilot 智能体模式的用武之地。

Copilot 智能体模式可以在 IDE 中更自主地工作。它的行为类似开发人员：先探索现有项目结构，然后执行所需更新、运行测试等任务，并自动修复发现的问题。现在了解如何使用智能体模式为网站添加新功能。

> [!NOTE]
> 智能体模式与云智能体名称相似，但两者提供不同的体验。智能体模式在 IDE 中执行任务，可快速反馈并与您互动。云智能体则像团队成员一样异步工作，通过议题和拉取请求与您协作。

本练习将介绍如何：

- 使用 Copilot 智能体模式探索项目、识别相关文件并协调进行更改。
- 使用 GitHub Copilot 智能体模式跨 UI 和数据层实现新功能。
- 在将代码合并到代码库前，审查 Copilot 智能体模式生成的更改和测试。

## 场景

游戏列表不断增加，您希望允许用户按类别和发行商筛选。上一练习已经添加发行商辅助函数；现在使用 Copilot 智能体模式完成其余的数据层、UI 和测试工作。

## 运行 Tailspin Toys 网站

更改前先探索 Tailspin Toys 网站，了解其当前功能。

该网站是一个以开发者为主题的棋盘游戏众筹平台。用户可以浏览游戏列表并查看详情。整个网站是一个 Astro 应用程序，在构建时将页面渲染为静态 HTML。页面通过 Drizzle ORM，在 frontmatter 中直接查询本地 SQLite 数据库；没有单独的后端 API，也没有客户端 UI 框架。可复用的数据访问辅助函数位于 `src/lib/`，交互功能则通过使用标准 DOM API 的小型 Astro `<script>` 实现。

### 启动网站

项目提供了 `npm run dev` 任务，用于启动单个 Astro 开发服务器。在 GitHub Codespace 中按以下步骤运行：

1. 返回 Codespace。完成上一练习后，当前分支应为 `main`。
2. 在 Codespace 中按 <kbd>Control</kbd>+<kbd>\`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>\`</kbd>（Windows/Linux）打开新终端。
3. 创建并切换到用于筛选功能的新分支：

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. 运行以下命令启动网站：

   ```bash
   npm run dev
   ```

   Astro 开发服务器就绪后，会显示类似以下 URL 的横幅：

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> 如果出现对话框，提示您打开 `http://localhost:4321` 的浏览器窗口，请选择 **x** 将其关闭。

5. 在终端中按住 <kbd>Command</kbd>（Mac）或 <kbd>Ctrl</kbd>（Windows/Linux），同时选择 Astro 服务器地址 `http://localhost:4321`，打开网站。

> [!NOTE]
> 在 Codespace 中选择终端里的 localhost 链接时，会自动将其重定向到 `https://<your-codespace-name>-4321.app.github.dev/`。这是连接到 Codespace 的私有隧道，您的网站服务器现在就在 Codespace 中运行！

### 探索网站

网站运行后，可以探索其功能。主要功能包括：

- **主页**：显示棋盘游戏列表，包括标题、图片和说明。
- **游戏详情页**：选择游戏后会打开详情页，显示标题、说明、发行商和类别等信息。

## 使用 Copilot 探索待办事项

> [!TIP]
> **打开 Copilot Chat**
>
> 开始以下练习前，返回 codespace，打开 Copilot Chat 面板，然后选择 **New Chat** 开始全新对话。每个练习的模式和模型选择可能不同；相关步骤会在适用时说明。

网站初始版本已能正常运行，但我们还想添加新功能。先查看待办事项列表。通过模板设置项目时，系统已自动创建一组议题；让 GitHub Copilot 显示这些议题。

1. 在 Chat 视图的智能体下拉菜单中选择 **Agent**。**Agent** 智能体可以自主规划并跨文件实现更改、运行终端命令和调用工具。

   ![Chat 视图中的智能体选择器截图。](../../../_images/shared-chat-mode-selector.png)

2. 从可用模型列表中选择 **Claude Sonnet 4.5**。

> [!CAUTION]
> 本研讨会作者并未表示偏好某个模型。编写本研讨会时使用的是 Claude Sonnet 4.5，因此说明中也采用该模型，希望您获得较一致的体验。但由于大型语言模型具有概率性，您看到的建议可能与本研讨会中描述的结果不同，这是正常且符合预期的。

> [!NOTE]
> 由于大型语言模型具有概率性，Copilot 可能会使用不同的 MCP 命令，但仍应能够完成任务。

3. 向 Copilot 发送以下提示，询问待办议题：

   ```plaintext
   Please show me the backlog of items from my GitHub repository. Help me prioritize them based on those which will be most useful to the user.
   ```

4. 选择 **Continue**，运行列出所有议题的命令。
5. 查看生成的议题列表。

请注意，Copilot 甚至根据它认为对用户最有用的程度为这些事项确定了优先级。

## 审查指令文件

让智能体生成代码前，先查看将用于为 Copilot 提供工作上下文的指令文件。我们将使用[用户界面 (UI)](https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md) 指令文件，其中说明了向网站添加功能时应采用的方法。

1. 在 codespace 中转到 `.github/instructions/ui.instructions.md`。
2. 查看其中关于如何添加功能的总体指导，包括：
   - 架构概述。
   - 组件设计、可测试性和无障碍访问原则。
   - 指向特定文件类型指令的链接，包括：
     - Astro
     - Tailwind CSS
     - Drizzle

> [!TIP]
> 指令文件可以引用其他指令文件和项目文件。路径相对于指令文件所在位置。这样既便于复用，也能把复杂指令拆分成更易于管理的小文件，并提供示例和模板。

## 实现筛选功能

完成筛选功能至少需要对应用进行三项更新：

- 在数据层（`src/lib/`）中添加或完善其余筛选逻辑。
- 添加或更新筛选行为测试。
- 更新游戏列表页，添加筛选 UI。

此外，合并到代码库之前还需要运行并通过测试。Copilot 智能体模式可以帮您完成这些任务！现在开始添加该功能。

1. 可以继续当前 Copilot 对话，也可以选择 **New Chat** 开始新对话。
2. 依次选择 **Add Context**、**Instructions** 和 **ui** 指令文件。

   ![选择 UI 指令文件的示例截图](../../../_images/ex3-select-instructions-file.png)

3. 确认 Chat 视图的智能体下拉菜单中仍选择 **Agent**。

   ![Chat 视图中的智能体选择器截图。](../../../_images/shared-chat-mode-selector.png)

4. 确认当前模型仍为 **Claude Sonnet 4.5**。
5. 使用以下提示，让 Copilot 根据相关待办议题实现功能：

   ```plaintext
   Please update the site to include filtering by publisher and category based on the requirements from the related GitHub issue in the backlog. A publishers helper already exists from the previous exercise, so preserve and refine that work as needed while completing the remaining data-layer, UI, and tests. Ensure all tests are passing before completion. The server is already running, so you do not need to start it up.
   ```

6. 观察 Copilot 如何先探索项目并找到功能相关文件。它应该会找到数据层辅助函数、UI 和测试，然后修改文件并运行测试。

   ![Copilot 正在探索项目文件的截图](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot 会执行多项任务，例如探索项目、修改文件和运行测试。根据任务复杂度和代码库规模，这可能需要几分钟。过程中，代码编辑器可能会显示 **Keep** 和 **Undo** 按钮。Copilot 完成后，每项更改都会有 **Keep** 或 **Undo** 按钮，因此工作进行时无需选择它们。

7. Copilot 提示时，选择 **Continue** 运行测试。

   ![Copilot Chat 面板请求用户确认是否运行测试的截图](../../../_images/ex3-agent-mode-run-tests.png)

8. 过程中可能会暂停，甚至有测试失败，这都没关系！Copilot 会反复生成代码并运行测试，直到任务完成且没有发现错误。

   ![Copilot 智能体模式完整 Chat 会话截图](../../../_images/ex3-agent-mode-proposed-changes.png)

9. 检查生成的代码，看看是否有潜在问题。

> [!CAUTION]
> 请务必审查 Copilot 或其他 AI 工具生成的代码。

10. 返回运行网站的浏览器，探索新功能！
11. 确认一切正常并审查代码后，在 Copilot Chat 窗口中选择 **Keep**。

## 总结与后续步骤

恭喜！本练习介绍了如何使用 GitHub Copilot 智能体模式为 Tailspin Toys 网站添加新功能。您了解了如何：

- 使用 GitHub Copilot 智能体模式跨 UI 和数据层实现新功能。
- 使用 Copilot 智能体模式探索项目、识别相关文件并协调进行更改。
- 在将代码合并到代码库前，审查 Copilot 智能体模式生成的更改和测试。

接下来，使用 [Playwright MCP 服务器测试功能][next-lesson]，然后为其创建拉取请求。

### 附加探索练习 – 实现分页

游戏列表不断增加后，就需要支持分页。使用本练习中学到的技能，提示 Copilot 更新网站并实现分页。可以考虑以下方面：

- 遵循现有最佳实践，包括使用现有指令文件。
- 考虑如何实现分页：允许用户选择每页大小，还是将其固定。
- 编写提示时提供必要指导，以便 Copilot 按照预期实现功能。
- 可能需要通过迭代向 GitHub Copilot 提出更改请求并提供上下文。这是使用 Copilot 的正常流程！

## 资源

- [Copilot 的 ask、edit 和 agent 模式：它们的作用及适用场景][choose-mode]
- [VS Code 中的智能体模式][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
