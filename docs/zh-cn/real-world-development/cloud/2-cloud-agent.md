---
title: "练习 2 - GitHub Copilot 云智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：自定义指令][previous-lesson] |
|:--|

几乎所有组织都会面临技术债务，例如尚未解决的安全问题、需要更新的旧代码，或因缺少时间而一直积压的功能请求。GitHub Copilot 云智能体可以自主执行更新代码、添加功能等任务。智能体完成工作后会创建草稿 PR，供开发人员审查。这样可以把繁琐任务交给智能体，加快开发进度，让开发人员专注于更重要的工作。

本课将介绍如何使用 Copilot 云智能体：

- 自定义生成代码的环境。
- 确保操作安全。
- 了解明确限定议题范围的重要性。
- 将议题分配给 Copilot。

## 场景

Tailspin Toys 希望解决一些技术债务。最初受雇开发网站的承包商没有留下完善的文档——实际上，您会发现代码完全缺少文档。团队希望首先为应用程序中所有导出的函数添加 TSDoc 文档注释。

此外，设计团队希望改进游戏发现体验。他们希望在每款游戏的详情页显示相关游戏，也就是同一类别中的其他游戏，让访问者可以继续浏览。现阶段不需要完善的实现，只需能用于 UX 验收测试即可。目前这项工作受阻，但还有其他优先级更高的议题。

这类任务很容易被推迟，因此非常适合交给 Copilot 云智能体。Copilot 云智能体可以异步处理任务，让开发人员先专注于其他工作，之后再回来审查其成果并确认一切符合预期。

## 了解 GitHub Copilot 云智能体

[GitHub Copilot 云智能体](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent)可以像人类开发人员一样在后台执行任务。与人类开发人员协作类似，您可以通过多种方式分配工作，例如[将 GitHub 议题分配给 Copilot](https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions)。分配任务后，Copilot 会创建草稿拉取请求来跟踪进度、设置工作环境并开始处理任务。您可以在会话进行期间或完成后查看详细内容。解决方案准备好供您审查时，Copilot 会在拉取请求中标记您！

## 明确限定任务范围的重要性

GitHub Copilot 并没有什么魔法——您仍然需要思考。即使看似简单的操作，深入分析后也可能很复杂，因此[请谨慎限定分配给 Copilot 云智能体的任务范围][cloud-agent-best-practices]。把它当作 AI 结对编程伙伴：分阶段工作、学习、试验并逐步调整。加入生成式 AI 并不会改变软件开发的基本原则。

## 此存储库中的自定义指令

前面的练习介绍了自定义指令及其如何指导 Copilot。如果您已完成自定义指令实践练习，就已了解 `.github/copilot-instructions.md` 的作用；否则，现在可以快速查看。在将工作分配给 Copilot 云智能体之前，只读查看存储库中已有的指令文件，以便之后观察其影响。

在存储库的 GitHub Web UI 中，或在已启动的 codespace 中打开以下文件：

- `.github/copilot-instructions.md`：查看 **Code standards** 部分，特别留意 TypeScript 规范和 TSDoc 文档注释要求。
- `.github/instructions/unit-tests.instructions.md`：查看 `applyTo` frontmatter，它将这些指令限定为适用于 `**/*.test.ts` 文件。

在下一节将 `Code lacks documentation` 议题分配给云智能体时，请留意生成的拉取请求中的 TSDoc 文档注释、TypeScript 规范和注释头。这些内容来自上述指令文件。后续课程审查 PR 时也会再次提到这一点。

## 为 Copilot 云智能体设置开发环境

创建代码通常需要特定环境并运行一些设置脚本，以确保环境状态良好；无论由谁编写代码，都是如此。将任务分配给 Copilot 时也一样，因为它会以类似软件工程师的方式完成工作。

云智能体使用 [GitHub Actions][github-actions] 执行任务。您可以通过创建[专用设置工作流][setup-workflow]来自定义其环境。该工作流配置在 `.github/workflows/copilot-setup-steps.yml` 文件中，会在云智能体开始工作前运行，并为其提供所需的开发工具和依赖项。本实验已预先配置此工作流，以便顺利完成练习并提供学习机会。它会为 Copilot 安装 Node.js、项目依赖项、端到端测试所需的浏览器，以及已迁移并填充数据的单个 Astro 应用程序 SQLite 数据库：

```yaml
name: "Copilot Setup Steps"

# 允许您从存储库的 "Actions" 选项卡测试设置步骤
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # 将权限设为步骤所需的最低权限。Copilot 会获得自己的令牌来执行操作。
    permissions:
      # 如果设置步骤需要克隆存储库（例如安装依赖项），则需要 contents: read 权限。如果不在设置步骤中克隆存储库，Copilot 会在步骤完成后自动克隆。
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # 前端 / 应用程序设置 - Node.js（整个应用程序现在基于 Astro + Drizzle/libSQL）
      - name: Set up Node.js
        uses: actions/setup-node@v6
        with:
          node-version: "lts/*"
          cache: "npm"
          cache-dependency-path: "./package-lock.json"

      - name: Install JavaScript dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium

      # 迁移并填充本地 SQLite 数据库，以便构建和测试有数据可用。
      - name: Set up the database
        run: npm run db:setup
```

它看起来与其他 GitHub 工作流文件类似，但有几个要点：

- 其中包含一个名为 `copilot-setup-steps` 的作业。该作业会在 Copilot 开始处理拉取请求之前在 GitHub Actions 中运行。
- 注意 `workflow_dispatch` 触发器，它允许您在存储库的 **Actions** 选项卡中手动运行工作流。这有助于测试工作流，而不必等待 Copilot 启动它。

## 添加文档

大家都知道文档很重要，但大多数项目要么文档过时，要么完全缺少文档。这类技术债务往往无人处理，会降低效率，并增加维护代码库或引入新开发人员的难度。Copilot 擅长生成文档，因此这是一个适合分配给 Copilot 云智能体的议题。它会在后台生成所需文档。后续练习中，您会回来审查成果。

1. 在新的浏览器标签页中转到 github.com 上的存储库。
2. 选择 **Issues** 选项卡。
3. 选择 **New issue**，打开新议题对话框。
4. 选择 **Blank issue**，创建空白议题。
5. 将 **Title** 设置为 `Code lacks documentation`。
6. 将 **Description** 设置为：

    ```plaintext
    Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
    ```

7. 选择 **Create** 创建议题。
8. 在右侧选择 **Assign to Copilot**，打开分配对话框。

  ![将 Copilot 分配给议题](../../../_images/shared-assign-copilot.png)

9. 选择 **Assign**。

  ![Copilot 分配详情](../../../_images/ex4-assign-copilot-details.png)

10. 选择 **Pull Requests** 选项卡。
11. 打开新生成的拉取请求（PR），标题类似于 `[WIP]: Code lacks documentation`。如果列表中还没有新 PR，请稍等片刻并刷新浏览器窗口。
12. 几分钟后，您应该能看到 Copilot 创建的待办事项列表。

> [!NOTE]
> Copilot 在 PR 中生成待办事项列表可能需要几分钟。Copilot 正在创建工作环境（运行前面介绍的工作流）、分析项目并确定解决问题的最佳方案。

13. 审查列表和 Copilot 将要完成的任务。
14. 向下滚动拉取请求时间线，您应该能看到 Copilot 已开始处理议题的更新。
15. 选择 **View session** 按钮。

  ![Copilot 会话视图](../../../_images/ex4-view-session.png)

> [!CAUTION]
> 您可能需要刷新窗口才能看到更新后的状态指示器。

16. 滚动查看实时会话，观察 Copilot 如何解决问题，包括探索代码、了解当前状态、暂停思考并确定合适的计划，以及创建代码。

这可能需要几分钟。Copilot 云智能体的主要目标之一是异步执行任务，让我们可以专注于其他工作。接下来将利用这一特性，再向 Copilot 云智能体分配一项任务，然后转而编写代码，为应用程序添加功能。

## 在游戏详情页添加相关游戏区域

如前所述，GitHub Copilot 云智能体的一大优势是能够并行分配工作：您专注于一组任务，智能体则处理另一组任务。即使为设计团队添加相关游戏区域可能不需要太长时间，这段时间也可以用于其他工作。现在将其分配给 Copilot 云智能体！

1. 返回 github.com 上的存储库。
2. 选择 **Issues** 选项卡。
3. 选择 **New issue**，打开新议题对话框。
4. 选择 **Blank issue**，使用空白模板。
5. 将 **Title** 设置为 `Show related games on the game details page`。
6. 将 **Description** 设置为：

    ```markdown
    We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
    ```

7. 选择 **Create** 创建议题。
8. 在右侧选择 **Assign to Copilot**，打开分配对话框。

  ![将 Copilot 分配给议题](../../../_images/shared-assign-copilot.png)

9. 选择 **Assign**。

很快，您应该会看到议题的第一条评论中出现一组 👀，表示 Copilot 已开始处理！

![Copilot 使用眼睛表情符号表示正在处理议题](../../../_images/ex4-issue-eyes-emoji.png)

Copilot 正在认真处理您的第二个请求！Copilot 云智能体的工作方式类似软件工程师，因此您无需持续监视它；任务完成后再审查即可。现在转而创建并使用自定义智能体。

## 总结与后续步骤

本课介绍了 [GitHub Copilot 云智能体][copilot-agents]。您可以将议题分配给云智能体，让它异步处理任务。Copilot 可用于解决技术债务、创建新功能，或协助将代码从一个框架迁移到另一个框架。

您了解了以下概念：

- 自定义生成代码的环境。
- 确保操作安全。
- 明确限定议题范围的重要性。
- 将议题分配给 Copilot。

云智能体正在后台工作，现在可以转而创建并使用自定义智能体。[Copilot 云智能体也可以使用 MCP 服务器][cloud-agent-mcp]，并且支持自定义指令；前面的模块已介绍过这些内容。

## 资源

- [关于 Copilot 云智能体][copilot-agents]
- [将 GitHub 议题分配给 Copilot][assign-issue]
- [Copilot 云智能体设置工作流最佳实践][cloud-agent-best-practices]

| [下一课：自定义智能体 →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
