---
title: "レッスン 6 - Playwright MCP で機能を検証する"
description: "Copilot CLI の Playwright MCP を確認または構成し、ブラウザーでフィルタリング機能を試します。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

コードを書くことは、コードだけに取り組むことではありません。データや外部サービスを使い、Copilot が利用できる自動化も追加する必要があります。MCP サーバーを使うと、Copilot CLI に組み込まれている機能を拡張して、追加のツールやサービスを利用できます。

このレッスンでは、次の作業を行います。

- Model Context Protocol (MCP) と Copilot CLI での使い方を学びます。
- 利用できない場合は Playwright MCP サーバーを追加します。
- エージェントにブラウザーを操作させて、フィルタリング機能を確認します。

## シナリオ

ユニットテストやエンドツーエンドテストも重要ですが、UI の変更を検証するには、実際に UI を操作する必要があります。Copilot がユーザーのように作業中の Web サイトを使えるようにすると、変更が期待どおりに動作する確信を高めながら、作業をさらに自動化できます。

## Model Context Protocol (MCP) とは

[Model Context Protocol (MCP)][mcp-blog-post] は、AI エージェントが外部ツールやサービスと通信するための仕組みです。MCP を使うと、AI エージェントは外部ツールやサービスにリアルタイムで接続し、最新情報にアクセスしてユーザーに代わって操作できます。

MCP サーバーは AI エージェントと外部ツールやサービスの間をつなぎ、ツールやリソースへのアクセスを提供します。各 MCP サーバーは、AI エージェントが利用できるツールとリソースのセットを表します。

よく使われる MCP サーバーには次のものがあります。

- **[GitHub MCP Server][github-mcp]**: GitHub リポジトリ、Issue、プルリクエストを管理する API を提供します。
- **[Playwright MCP Server][playwright-mcp-server]**: Playwright を使ったブラウザー自動化機能を提供します。

ほかにも多数の MCP サーバーがあります。GitHub がホストする [MCP registry][mcp-registry] では、エコシステム内のサーバーを見つけたり、貢献したりできます。

> [!CAUTION]
> MCP サーバーは、ほかの依存関係と同じように扱ってください。利用する前にソースコードを確認し、発行元を検証して、セキュリティへの影響を検討します。

## Playwright MCP サーバーを追加する

Playwright MCP サーバーがまだ利用できない場合は、Copilot がユーザーと同じように Web サイトを操作できるように追加します。

> [!NOTE]
> Copilot CLI で Playwright MCP がすでに構成されている場合は、セットアップを省略して次のセクションに進みます。

1. Codespace に戻ります。
2. Copilot CLI で次のコマンドを実行して、MCP サーバーの追加ダイアログを開きます。

   ```plaintext
   /mcp add
   ```

3. 名前に `playwright` と入力して、<kbd>Tab</kbd> を押します。
4. サーバーの種類が **STDIO** であることを確認し、<kbd>Enter</kbd>、続いて <kbd>Tab</kbd> を押します。
5. **Command** ダイアログに次の内容を貼り付けます。

   ```plaintext
   npx -y @playwright/mcp@latest --headless --no-sandbox
   ```

6. <kbd>Control</kbd>+<kbd>S</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>S</kbd> (Windows/Linux) を押して、新しい MCP サーバーを保存します。
7. <kbd>Esc</kbd> を押して MCP ダイアログを閉じます。

## Playwright を使って機能を確認するよう Copilot に依頼する

前に、機能が正常に動作することを手動で確認しました。次に、Copilot で利用できる Playwright MCP サーバーにも同じ確認を依頼します。

1. 次のプロンプトを使って、Playwright MCP サーバーで機能を検証するよう Copilot に依頼します。

   ```plaintext
   Start the app and use Playwright MCP to check filtering against the issue and our plan. Tell me what works and what doesn't, without making changes. Stop the server you started when you're done.
   ```

> [!NOTE]
> Copilot は通常、MCP サーバーを自動で見つけるため、使用を指示しなくてもかまいません。ただし、使うべきものが分かっている場合はプロンプトで伝えると、方向性が明確になり、結果が安定しやすくなります。

2. Copilot がブラウザー上で機能を確認する手順を示す様子を確認します。
3. 報告内容を読み、すべてが期待どおりに動作することを確認します。

Copilot はサーバーを起動して、Playwright で Web サイトを操作し、サーバーを停止した後、結果を報告します。

## まとめと次のステップ

Copilot CLI から Playwright MCP サーバーを使い、実際のブラウザーで機能を確認しました。次の作業を行いました。

- Model Context Protocol (MCP) と Copilot CLI での使い方を学びました。
- 利用できない場合は Playwright MCP サーバーを追加しました。
- エージェントにブラウザーを操作させて、フィルタリング機能を確認しました。

次に、スキルとブラウザーツールを組み合わせた [QA カスタムエージェントを作成します][next-lesson]。

## リソース

- [MCP とは何か、なぜ注目されているのか][mcp-blog-post]
- [Microsoft Playwright MCP Server][playwright-mcp-server]
- [Copilot CLI に MCP サーバーを追加する][add-mcp]

[previous-lesson]: ../5-agent-skills/
[next-lesson]: ../7-qa-agent/
[mcp-blog-post]: https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/
[playwright-mcp-server]: https://github.com/microsoft/playwright-mcp
[github-mcp]: https://github.com/github/github-mcp-server
[mcp-registry]: https://github.com/mcp
[add-mcp]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/add-mcp-servers
