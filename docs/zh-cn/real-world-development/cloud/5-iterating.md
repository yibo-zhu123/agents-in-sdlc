---
title: "练习 5：迭代改进 GitHub Copilot 的工作"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：管理智能体][previous-lesson] |
|:--|

## 审查工作

在本实验中，您使用 GitHub Copilot 完成了多个改善用户体验和添加功能的任务。您让 Copilot 为代码添加文档、构建相关游戏功能供设计团队迭代，并实现包括高对比度和浅色模式切换开关在内的无障碍访问功能。现在查看代码更改；如有需要，也可以向 Copilot 提供反馈以改进成果。

### 场景

前面已经多次强调，加入生成式 AI 并不会改变软件设计和 DevOps 的基本原则。我们始终需要审查生成的代码，并遵循常规 DevOps 流程。现在，在为团队其他成员启用审查之前，先审查 GitHub Copilot 为代码文档、相关游戏功能和无障碍访问功能提出的更改。

## 安全性与 GitHub Copilot 云智能体

Copilot 云智能体会异步且自主地执行任务，因此设置了一些安全约束，确保整个过程安全：

- Copilot 只能读取存储库，并且**只能**向用于提交代码的分支写入内容。
- 云智能体在 GitHub Actions 中运行，并在单独的临时环境中工作。
- 运行任何 GitHub Actions 工作流都需要人工审批。
- 默认情况下，[对外部资源的访问会受到限制](https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall)，包括 MCP 服务器。

## 审查生成的文档

先查看 GitHub Copilot 云智能体创建的第一个拉取请求（PR），该 PR 为代码添加了文档。您将使用 GitHub.com 上标准的 PR 界面完成此操作。

> [!NOTE]
> 查看 PR 时，可能会看到 GitHub Copilot 被防火墙阻止的警告。这是**预期行为**，因为默认情况下 Copilot 对外部资源（包括外部 MCP 服务器）的访问受限。如有需要，可以[自定义或禁用 Copilot 云智能体的防火墙][agent-firewall]。

1. 返回 github.com 上的存储库。
2. 选择 **Pull Requests**，打开拉取请求列表。
3. 打开标题类似 **Add missing documentation** 或内容更具体的拉取请求。

> [!NOTE]
> 如果 Copilot 仍在处理任务，拉取请求标题中会包含 **[WIP]** 标记。此时请等待 Copilot 完成工作。这可能需要几分钟；您可以稍作休息，或回顾之前学到的内容。

4. 拉取请求准备就绪后，选择 **Files changed** 选项卡并审查更改。

    ![Files changed 选项卡](../../../_images/shared-pr-files-changed.png)

5. 查看更新后的代码，其中包含新生成的 TSDoc 文档注释和其他文档。具体更改可能有所不同。

    查看时，留意 TSDoc 文档注释、TypeScript 规范和注释头。这些内容来自练习 2 开始时审查的自定义指令文件。

6. 审查完更新并确认一切正常后，返回 **Conversation** 选项卡并向下滚动。
7. 您应该会看到有工作流等待审批的指示器。
8. 如果有工作流等待审批，请选择 **Approve and run workflows**。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

9. 您应该会在拉取请求的检查区域看到工作流已排队。如果一切正常，单个 Astro 应用程序的项目检查应该会通过。此过程可能需要几分钟。

## 请求 GitHub Copilot 修改内容

与 Copilot 协作审查拉取请求并非单向过程。您可以像标记团队成员一样，在拉取请求评论或代码行内评论中标记 Copilot。Copilot 会读取评论并启动新会话来处理更改。由于结果并非确定，我们无法提供必须照搬的请求文本。可以让 Copilot 修改的内容包括：

- 在每个代码文件顶部添加简短的注释头，说明文件用途。
- 为 TypeScript 和 Astro 文件添加 TSDoc 文档注释。
- 创建 README，介绍 Astro 应用程序结构。

1. 添加评论，请求修改生成的文档，并像标记其他用户一样标记 **@copilot**。可以使用上述任一建议，也可以提出其他希望在代码库中看到的文档改进。
2. 选择 **View Session**，观察 Copilot 如何处理任务。注意 Copilot 会启动新会话来进行更新。
3. 选择 **Back to pull request**，即可返回拉取请求。

    ![Back to pull request](../../../_images/ex7-back-to-pr.png)

4. Copilot 完成更改后，拉取请求中应该会出现新的提交。
5. 选择 **Files changed** 选项卡审查更改。

您可以继续迭代，直到满意为止。确认满意后，可以将 PR 从草稿转换为待审查状态，再合并到主分支。

![将 PR 转为待审查状态](../../../_images/ex7-ready-for-review.png)

## 审查相关游戏功能

现在查看 Copilot 为解决“在游戏详情页显示相关游戏”议题而创建的 PR。

1. 返回 GitHub.com 上的存储库。
2. 选择 **Pull Requests** 选项卡。
3. 选择标题类似 **Show related games on the game details page** 或内容更具体的 PR。
4. 选择 **Files changed** 选项卡，审查生成的代码。
5. 审查更新并确认一切正常后，返回 **Conversation** 选项卡并向下滚动。
6. 您应该会看到有工作流等待审批的指示器。
7. 如果有工作流等待审批，请选择 **Approve and run workflows**。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

8. 您应该会在拉取请求的检查区域看到工作流已排队。如果一切正常，单个 Astro 应用程序的项目检查应该会通过。此过程可能需要几分钟。
9. **可选：**您也可以在 Codespace 中切换到此分支，手动测试相关游戏功能。转到 Codespace，打开终端并运行以下命令（将 `<branch-name>` 替换为 Copilot 创建的分支名称，例如 **copilot/fix-8**）：

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

Copilot 已经构建了相关游戏功能！和之前一样，您可以继续迭代，让 Copilot 云智能体进行更新。例如，可以要求 Copilot 调整显示的相关游戏数量，或确保添加注释头和 TSDoc 文档注释（请记住，这项任务是在自定义指令更新之前分配的）。和之前一样，可以在 **Conversation** 选项卡中添加评论来提出请求；Copilot 会读取评论并启动新会话。

## 审查无障碍访问功能

最后，审查使用无障碍访问自定义智能体实现的功能。此 PR 应同时包含练习 3 中分配的高对比度模式，以及练习 4 中在任务控制中心要求添加的浅色模式。

1. 返回 GitHub.com 上的存储库。
2. 选择 **Pull Requests** 选项卡。
3. 选择标题类似 **Add high contrast mode to website** 或内容更具体的 PR。

> [!NOTE]
> 如果 Copilot 仍在处理任务，拉取请求标题中会包含 **[WIP]** 标记。此时请等待 Copilot 完成工作。这可能需要几分钟。

4. 选择 **Files changed** 选项卡，审查生成的代码。
5. 审查实现，特别留意：
   - 用于切换模式的 UI 控件。
   - 用于通过本地存储保存用户偏好的实现。
   - 高对比度和浅色模式的 CSS 或样式更改。
   - 无障碍属性（ARIA 标签、键盘导航等）。
   - 管理模式切换的 JavaScript/TypeScript 代码。

6. 审查更新并确认一切正常后，返回 **Conversation** 选项卡并向下滚动。
7. 您应该会看到有工作流等待审批的指示器。
8. 如果有工作流等待审批，请选择 **Approve and run workflows**。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

9. 您应该会在拉取请求的检查区域看到工作流已排队。如果一切正常，单个 Astro 应用程序的项目检查应该会通过。此过程可能需要几分钟。
10. **可选：**您可以在 Codespace 中切换到此分支，手动测试无障碍访问功能。转到 Codespace，打开终端并运行以下命令（将 `<branch-name>` 替换为 Copilot 创建的分支名称）：

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

    然后启动应用程序，在浏览器中测试高对比度和浅色模式切换开关，并确认页面重新加载后设置仍然保留。

请注意，无障碍访问自定义智能体帮助 Copilot 按照无障碍访问最佳实践实现了这些功能。如果发现无障碍访问方面的问题或改进机会，可以像前面审查 PR 时一样在评论中标记 **@copilot**，请求更新。

## 可选练习 — 继续委派任务

当您可以把真实待办事项交给云智能体并转而处理其他工作时，云智能体最能发挥作用。可以在自己的存储库中创建几个议题并分配给 Copilot，逐步养成这种工作习惯。以下是一些示例：

- 在游戏详情页创建支持者意向表单。
- 为游戏列表页添加分页。
- 为数据访问辅助函数添加输入验证和错误处理。

## 总结

您已完成云智能体学习路径。通过这些课程，您：

- **检查了存储库附带的自定义指令文件**，以便稍后观察这些文件对云智能体输出的影响。
- **将议题分配给 Copilot 云智能体**，并观察它设置环境、制定计划和异步执行任务。
- **创建并使用自定义智能体**处理无障碍访问，添加高对比度和浅色模式切换开关。
- **使用 Copilot 智能体页面作为任务控制中心**，监视并指导进行中的无障碍访问会话。
- **审查并迭代改进云智能体的拉取请求**，通过标记 `@copilot` 请求更改并批准工作流。

## 回顾与后续步骤

您已完成云智能体学习路径。如果想继续探索，其他学习路径可以补充本次练习的内容：

- 🖥️ **[VS Code 学习路径](../../vscode/)** — 在 IDE 中探索 Copilot Chat 智能体模式和 MCP 集成。
- 💻 **[CLI 学习路径](../../cli/)** — 从终端交付两项经过审查的更改，使用 Plan 和 Autopilot 模式、智能体技能、自定义 QA 智能体、Playwright MCP、Agent Merge，以及可选的 `/delegate` 云智能体桥接功能。

您也可以在自己的存储库中尝试以下后续练习：

- 将重构或测试覆盖率议题分配给云智能体。
- 为其他领域创建自定义智能体。
- 为不同技术栈设置 `copilot-setup-steps.yml`。

## 资源

- [GitHub Copilot][github-copilot]
- [关于 Copilot 智能体][copilot-agents]
- [将 GitHub 议题分配给 Copilot][assign-issue]
- [Copilot 云智能体设置工作流最佳实践][cloud-agent-best-practices]
- [配置 Copilot 云智能体防火墙][agent-firewall]

---

| [← 上一课：管理智能体][previous-lesson] |
|:--|

[github-copilot]: https://github.com/features/copilot
[cloud-agent-overview]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[agent-firewall]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall

[previous-lesson]: ../4-managing-agents/
