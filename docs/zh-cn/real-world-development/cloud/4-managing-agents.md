---
title: "练习 4 - 监视和管理智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

在前几项练习中，您让 Copilot 云智能体处理了三个改善用户体验和添加功能的任务。云智能体以异步和自主的方式工作，但监视任务仍然很重要。

您可以使用多种工具管理分配给云智能体的任务，包括 GitHub.com 上的[智能体页面][agents-page]。在此任务控制中心，您可以查看所有具有未关闭拉取请求（PR）的智能体任务、探索已执行的操作，甚至指导正在进行的会话。

本课将介绍如何：

- 探索智能体页面并监视云智能体任务。
- 指导正在进行的会话，请求添加其他功能。

## 场景

为实现高对比度模式分配智能体后，团队意识到也可以同时添加浅色模式。既然已经在更新网站样式并添加切换功能，不妨一并实现。您将指导智能体，同时添加浅色模式和高对比度模式。

## 审查 Copilot 云智能体任务

查看分配给 Copilot 云智能体的所有任务的当前状态。

1. 转到 [Copilot 智能体页面](https://github.com/copilot/agents)。
2. 查看主窗格和左侧窗格中的任务列表。您应该能看到已分配给 Copilot 的任务，其中包括：
    - 为代码库更新文档。
    - 生成用于修改游戏的 API。
    - 为网站添加高对比度模式。
3. 选择一个正在运行的任务。查看 Copilot 已执行的操作，例如：
    - 从存储库检出代码。
    - 为 Copilot 创建工作环境。
    - 设置 MCP 服务器。
    - 执行完成分配任务所需的其他步骤。

> [!NOTE]
> 具体显示的步骤会因 Copilot 工作进度和采用的方案而异。

4. 另请查看右侧显示的 PR 窗格，其中包含 PR 和已更改文件，可用于进一步监视。

## 指导云智能体

您已查看当前任务，现在请求 Copilot 在实现高对比度模式的同时添加浅色模式切换开关。

1. 选择与添加高对比度模式相关的会话。具体标题可能会根据 Copilot 使用的名称和工作进度而异。

    ![任务控制中心中的无障碍访问会话](../../../_images/ex6-accessibility-session.png)

2. 观察会话几分钟，直到显示设置已完成并开始执行任务。看到类似以下内容的消息后，即可继续。
3. 在 **Steer active session while Copilot is working** 对话框中，输入以下提示：

    ```plaintext
    While we are working on a high contrast mode, let's also add a light mode. There should be a switch for this mode as well where users can select their desired display mode.
    ```

    ![智能体页面上的云智能体任务截图，其中突出显示了“Steer active session while Copilot is working”对话框](../../../_images/ex6-steer-cloud-agent-task.png)

4. 按 <kbd>Enter</kbd> 发送提示。
5. 注意 Copilot 如何确认提示，并将它加入工作流程。

## 让 Copilot 完成工作

和之前一样，Copilot 会继续处理更新后的任务！它会在完成收到消息时正在执行的步骤后，将新请求纳入工作流程。

和之前一样，这需要几分钟。您可以借此暂停一下，回顾到目前为止学到和探索的内容。

## 总结与后续步骤

本课介绍了 Copilot 智能体页面，这是监视和指导 GitHub Copilot 云智能体任务的中心位置。您可以使用此任务控制中心跟踪所有进行中和已完成的任务、审查正在执行的工作，甚至重新引导正在进行的任务，以调整范围或提供额外指导。

您了解了以下内容：

- 如何使用任务控制中心和智能体页面监视云智能体任务。
- 如何重新引导正在进行的会话以请求添加其他功能。

Copilot 正在完成无障碍访问功能。接下来，您将[迭代改进 Copilot 创建的拉取请求][next-lesson]。

## 资源

- [Copilot 智能体页面][agents-page]
- [关于自定义智能体][custom-agents]

---

| [← 上一课：自定义智能体][previous-lesson] | [下一课：迭代改进 Copilot 的工作 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
