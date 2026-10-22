---
title: "练习 5 - 监视和管理智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：自定义智能体][previous-lesson] |
|:--|

在智能体模式下使用 GitHub Copilot 时，它会自主探索代码库、提出更改、编辑文件并运行命令。由于这些操作会在您的计算机上实时进行，VS Code 中的 Copilot Chat 会显示每次工具调用和文件编辑的进展。您可以在聊天中查看每个差异、接受或拒绝单项更改，也可以通过后续提示引导对话，在不离开编辑器的情况下完善或扩展工作。

本练习将介绍如何：

- 实时监视无障碍访问智能体的工作。
- 审查并接受建议的文件编辑。
- 在会话进行期间指导智能体，添加与高对比度并列的浅色模式切换开关。

## 场景

您在[练习 4][previous-lesson]中使用的无障碍访问自定义智能体正在实现高对比度模式。工作进行期间，设计团队又提出了一项要求：他们还希望为觉得默认主题太暗的用户提供浅色模式切换开关。您将指导现有会话扩展工作，而不是重新开始一个新对话。

## 监视 Copilot 的进度

Copilot Chat 视图会显示智能体执行的每个步骤，包括探索的文件、运行的命令和建议的编辑。每次文件编辑都会在聊天中以差异形式显示，并提供 **Keep** 和 **Undo** 控件。

1. 返回练习 4 中的 Copilot Chat 视图。
2. 智能体工作期间滚动聊天，查看每次工具调用。您会看到文件读取、文件编辑以及请求运行的终端命令。
3. 智能体建议编辑文件时，差异会以内嵌形式显示：
    - 选择 **Keep** 接受更改。
    - 选择 **Undo** 撤销更改，让智能体重新尝试。
4. 也可以打开聊天顶部的 **Files changed** 指示器，查看此会话期间智能体修改的全部文件。

> [!NOTE]
> 如果工具调用请求权限（例如运行终端命令），智能体会暂停并显示 **Allow** 提示。仅批准您认可的命令。

5. 等待智能体完成高对比度模式的实现。完成后，它会显示更改摘要。

## 指导智能体添加浅色模式

现在高对比度模式已实现，接下来在同一对话中添加浅色模式切换开关。由于继续使用同一个聊天会话，智能体会保留之前工作的上下文，包括它修改的组件、使用的样式方法和设置的本地存储模式。

1. 在 Copilot Chat 输入框中发送以下后续提示：

    ```plaintext
    Nice work! Now add a light-mode toggle alongside the high-contrast toggle. Light mode should follow the same pattern — persist via local storage and be accessible from the same area of the UI. The user should be able to enable high-contrast and light mode independently.
    ```

2. 智能体会继续工作，探索已有更改并提出浅色模式的补充修改。
3. 新编辑出现时逐项审查，像处理高对比度模式一样使用 **Keep** 或 **Undo**。
4. 如果智能体的实现方向不符合您的期望，例如它以您不喜欢的方式重构切换开关 UI，可以选择聊天输入框旁的 **Stop** 按钮停止它，再发送澄清提示。

> [!TIP]
> 在进行中的会话中调整方向，比从头开始更快。可以通过后续提示要求调整（“简化 CSS，使用 CSS 变量而不是重复选择器”）、纠正方向（“不要添加第三个切换开关，将浅色模式集成到现有设置面板中”），或扩大范围（“还要为两个开关添加键盘快捷键”）。

## 审查工作集

提交工作前，快速检查智能体修改的全部内容。

1. 在 VS Code 中打开 **Source Control** 视图。
2. 审查所有已更改文件。应能看到 Astro 组件、样式和相关测试的更新。
3. 打开部分已更改文件并查看差异。确认自定义智能体的无障碍访问模式已应用，例如 ARIA 属性、键盘导航、语义 HTML 和使用本地存储持久化设置。
4. 确认无误后，在 Source Control 面板中暂存并提交更改。下一课将发布这些更改。

## 总结与后续步骤

您已使用 Copilot Chat 智能体模式监视并指导进行中的会话——逐项审查文件编辑、接受或拒绝更改，并通过后续提示扩展工作，同时保留上下文。

本课中，您了解了：

- 如何实时监视智能体的工作。
- 如何接受或拒绝单项文件编辑。
- 如何通过后续提示在会话进行期间调整方向。

在[下一课][next-lesson]中，您将把无障碍访问工作发布为拉取请求，并审查本实验中本地自定义智能体生成的更改。

## 资源

- [VS Code 中的 Copilot Chat][copilot-chat-vscode]
- [使用智能体模式][agent-mode]

---

| [← 上一课：自定义智能体][previous-lesson] | [下一课：迭代改进 Copilot 的工作 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../4-custom-agents/
[next-lesson]: ../6-iterating/
[copilot-chat-vscode]: https://code.visualstudio.com/docs/copilot/chat/copilot-chat
[agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
