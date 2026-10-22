---
title: "练习 0：先决条件"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

开始 Copilot 云智能体练习前，需要先准备好环境。您将创建 Tailspin Toys 存储库的副本，并启动一个 [codespace][codespaces]，用于编辑指令文件和审查云智能体生成的工作。

## 设置实验室存储库

若要创建用于本实验的代码副本，请从[模板][template-repository]创建一个实例。新实例将包含实验所需的所有文件，后续练习都将在此基础上进行。

1. 在新的浏览器窗口中，转到 [Tailspin Toys 模板][tailspin-template]。
2. 在实验室存储库页面上选择 **Use this template**，创建自己的存储库副本。然后选择 **Create a new repository**。

    ![Use this template 按钮](../../../_images/ex0-use-template.png)

3. 如果您参加由 GitHub 或 Microsoft 主办的讲师指导活动，请按照导师提供的说明操作。否则，可以在您有权使用 GitHub Copilot 的组织中创建新存储库。

    ![填写存储库模板设置](../../../_images/ex0-repository-settings.png)

4. 记下您创建的存储库路径（**organization-or-user-name/repository-name**），稍后在实验中会用到。

## 创建 codespace

接下来，您将使用 codespace 完成本实验。

[GitHub Codespaces][codespaces] 是基于云的开发环境，可让您直接在浏览器中编写、运行和调试代码。它提供功能齐全的 IDE，支持多种编程语言、扩展和工具。

1. 转到刚创建的存储库。
2. 选择绿色的 **Code** 按钮。

    ![选择 Code 按钮](../../../_images/ex0-code-button.png)

3. 选择 **Codespaces** 选项卡，再选择 **+** 按钮创建新的 Codespace。

    ![创建新的 codespace](../../../_images/ex0-create-codespace.png)

创建 codespace 需要几分钟，不过仍比手动安装所有服务快得多。等待时，可以先探索 GitHub Copilot 的其他功能，接下来我们就会介绍这些功能。

> [!CAUTION]
> 后续练习还会用到此 codespace。现在先不要关闭浏览器标签页。

> [!NOTE]
> 本研讨会可在 codespace 或本地 [dev container][dev-containers] 中运行。两者都已安装所需的先决条件，可帮助您顺利完成练习。如果想在本地运行，请在 VS Code 中打开克隆的存储库；出现提示时选择 **Reopen in Container**。VS Code 将构建与 codespace 相同的开发容器。

[codespaces]: https://github.com/features/codespaces
[dev-containers]: https://code.visualstudio.com/docs/devcontainers/containers

## 总结

恭喜，您已创建实验室存储库的副本！您也已开始创建 codespace，接下来将使用它与 Copilot 云智能体一起完成练习。

## 下一步

接下来，为云智能体添加要遵循的自定义指令。继续学习[练习 1 - 自定义指令][next-lesson]。

## 资源

- [GitHub Codespaces 概述][codespaces]
- [从模板创建存储库][template-repository]
- [开始使用 Codespaces][codespaces-quickstart]

[tailspin-template]: https://github.com/github-samples/tailspin-toys
[template-repository]: https://docs.github.com/repositories/creating-and-managing-repositories/creating-a-template-repository
[codespaces-quickstart]: https://docs.github.com/codespaces/getting-started/quickstart
[next-lesson]: ../1-custom-instructions/
