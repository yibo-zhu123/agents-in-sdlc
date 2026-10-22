---
title: "演習 3 - Playwright MCP サーバーで機能をテストする"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: エージェントモード][previous-lesson] · [次のレッスン: カスタムエージェント →][next-lesson]

エージェントモードでフィルタリング機能を構築しました。プルリクエストを開く前に、ブラウザーで実際に動作することを確認します。自分でアプリを操作する代わりに、**Playwright MCP サーバー**を接続して、Copilot に実際のブラウザーで機能をテストしてもらいます。その後、ブランチを公開して PR を開きます。

この演習では、次の作業を行います。

- Model Context Protocol (MCP) と、MCP サーバーが Copilot のツールを拡張する方法を学びます。
- Playwright MCP サーバーをワークスペースに追加します。
- Copilot に Playwright MCP サーバーを使わせ、ブラウザーでフィルタリング機能を手動でテストします。
- フィルタリング作業用のブランチを公開し、プルリクエストを開きます。

## Model Context Protocol (MCP) とは

エージェントモードは、エディター以外のツールも利用できるため、さらに便利になります。Copilot は Model Context Protocol (MCP) を使って外部ツールやサービスと通信します。

![エージェントモードがコンテキスト、LLM、MCP サーバーや VS Code 拡張機能のツールとやりとりする仕組みの図](../../../_images/ex1-mcp-diagram.png)

[Model Context Protocol (MCP)](https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/) は、AI エージェントが外部ツールやサービスと通信するための仕組みです。MCP を使うと、AI エージェントはリソースから最新情報を取得し、ツールを通じてユーザーに代わって操作できます。

これらのツールやリソースには、AI エージェントと外部ツールやサービスをつなぐ MCP サーバーを通じてアクセスします。MCP サーバーは、既存の API や NPM パッケージなどのローカルツールと、AI エージェント間の通信を管理します。各 MCP サーバーは、エージェントが利用できるツールとリソースのセットを提供します。

よく使われる MCP サーバーには次のものがあります。

- **[GitHub MCP Server](https://github.com/github/github-mcp-server)**: GitHub リポジトリを管理する API を提供します。リポジトリの新規作成、既存リポジトリの更新、Issue やプルリクエストの管理などを AI エージェントが行えます。
- **[Playwright MCP Server](https://github.com/microsoft/playwright-mcp)**: Playwright を使ったブラウザー自動化機能を提供します。Web ページへの移動、フォームへの入力、ボタンの選択などを AI エージェントが行えます。

ほかにも多くの MCP サーバーが、さまざまなツールやリソースを提供しています。GitHub がホストする [MCP registry](https://github.com/mcp) で、サーバーの発見やエコシステムへの貢献ができます。

> [!CAUTION]
> セキュリティの観点から、MCP サーバーはほかの依存関係と同じように扱ってください。利用する前にソースコードをよく確認し、発行元を検証して、セキュリティへの影響を検討します。信頼できる MCP サーバーのみを使用し、機密性の高いリソースや操作へのアクセス許可には注意してください。

## MCP の構成を確認する

`.vscode/mcp.json` ファイルでは、この VS Code ワークスペースで利用できる MCP サーバーを構成します。

1. Codespace で `.vscode/mcp.json` を開きます。
2. `github` サーバーがすでに構成されていることを確認します。

    ```json
    {
      "servers": {
        "github": {
          "type": "http",
          "url": "https://api.githubcopilot.com/mcp/"
        }
      }
    }
    ```

`github` エントリはプロジェクトテンプレートに含まれています。そのため、前の演習で Copilot はバックログの Issue を読み取れました。[リモート GitHub MCP サーバー][remote-github-mcp-server]を使用します。ローカルでのインストールは不要です。VS Code は HTTP 経由で接続し、Copilot が最初にツールを使うときに GitHub で認証します。

## Playwright MCP サーバーを追加する

次に 2 つ目のサーバーを追加します。[Playwright MCP サーバー][playwright-mcp-server]を使うと、Copilot が操作できるブラウザーが提供され、機能をテストできます。

1. `.vscode/mcp.json` に `github` と並べて `playwright` エントリを追加し、ファイルが次のようになるようにします。

    ```json
    {
      "servers": {
        "github": {
          "type": "http",
          "url": "https://api.githubcopilot.com/mcp/"
        },
        "playwright": {
          "command": "npx",
          "args": ["@playwright/mcp@latest", "--headless"]
        }
      }
    }
    ```

2. ファイルを保存します。

リモートの `github` サーバーとは異なり、`playwright` はローカルサーバーです。VS Code が `npx @playwright/mcp@latest` を実行して起動します。`--headless` フラグを指定すると、画面に表示されない状態で Playwright がブラウザーを実行します。デスクトップ画面を表示できない Codespace で必要です。

> [!NOTE]
> Tailspin Toys プロジェクトはエンドツーエンドテストにも Playwright を使うため、通常、必要なブラウザーはすでにインストールされています。Copilot がブラウザーがないと報告した場合は、`npx playwright install chromium` を実行して再度試すよう依頼します。

## サーバーを起動して信頼する

VS Code は MCP サーバーを**必要に応じて**起動します。Copilot が初めてサーバーのツールを必要とすると、VS Code はサーバーを起動して、信頼するかどうかを確認します。

1. `.vscode/mcp.json` の `playwright` エントリの上に **Start** アクションが表示されます。これを選択してサーバーを起動し、構成が正しいことを確認します。
2. VS Code がサーバーを信頼するか確認した場合は、構成を確認し、信頼することを選択してサーバーを起動します。

この時点で手動で起動する必要はありません。起動しなかった場合は、次の手順で Copilot が初めてツールを使うときに VS Code がサーバーを起動し、信頼を確認します。

## フィルタリング機能をテストする

[Playwright MCP サーバー][playwright-mcp-server]を使うと、Copilot が実際のブラウザーを操作できます。エージェントがページを開いて移動し、フィルターを適用して結果を読み取り、観察したことをまとめます。会話を離れずに、機能が期待どおりに動くことをすばやく確認できます。

Playwright MCP サーバーは、スクリーンショットではなく、ページの[アクセシビリティツリー][playwright-mcp-server]を使って動作します。そのため、エージェントは支援技術と同様に、ラベル付きのボタン、リンク、リスト項目などの構造化された要素を使って推論します。簡単な機能確認が、アクセシビリティの基本的な確認にもなります。

サーバーを接続してアプリを起動した状態で、次のプロンプトを Copilot に送信し、フィルタリング機能を操作させます。

```text
Using the Playwright MCP server, open a browser to the running app at http://localhost:4321 and verify the new game filtering feature:

1. Go to the games page and note how many games are listed.
2. Apply a category filter and confirm the list updates to only show games in that category.
3. Clear it, then apply a publisher filter and confirm the list updates to that publisher.
4. Combine a category and a publisher filter and confirm the results respect both.

Report what you observe at each step, and call out anything that does not behave as expected.
```

Copilot は Playwright MCP サーバーでブラウザーを起動し、各手順を実行して、結果を報告します。報告を Issue の受け入れ条件と照らし合わせてください。問題があれば、プルリクエストを開く前に追加で質問したり、コードの修正を依頼したりします。

> [!NOTE]
> このテストには、アプリが `http://localhost:4321` で動作している必要があります。開発サーバーを停止していた場合は、プロンプトを送信する前に再起動します。Copilot が Playwright MCP サーバーを初めて使うときに、ブラウザーをダウンロードする必要がある場合があります。ブラウザーがないと報告された場合は、`npx playwright install chromium` を実行して再度試すよう依頼します。

[playwright-mcp-server]: https://github.com/microsoft/playwright-mcp

## ブランチを公開してプルリクエストを作成する

機能が動作することを確認できたので、チームがレビューできるようプルリクエスト (PR) を開きます。まず、`filtering-vscode` ブランチを公開します。

1. Codespace の **Source Control** パネルに移動し、Copilot が行った変更を確認します。
2. **+** アイコンを選択して変更をステージします。
3. **Sparkle** ボタンでコミットメッセージを生成します。

   ![Source Control パネルで変更を確認する画面](../../../_images/ex3-source-control-changes.png)

4. **Publish** を選択して、ブランチをリポジトリにプッシュします。

## プルリクエストを作成する

github.com や GitHub CLI など、さまざまな方法でプルリクエストを作成できます。ここでは GitHub Copilot に関連する Issue を探して、ひも付けた PR を作成してもらいます。

1. Copilot Chat パネルに移動し、**New Chat** を選択して新しいセッションを開始します。
2. GitHub のツールを使えるよう、エージェント一覧で **Agent** を選択します。
3. 次のプロンプトを使って PR の作成を依頼します。

   ```plaintext
   Find the issue in the repo related to filtering by category and publisher. Create a new pull request for the current branch, and associate it with the correct issue.
   ```

4. 情報の取得や操作の実行を許可するため、必要に応じて **Continue** を選択します。GitHub ツールを初めて使うときは GitHub への認証を求められることがあります。表示される手順に従ってください。
5. Copilot が Issue を検索し、該当するものを見つけて PR を作成する様子を確認します。
6. Copilot が生成したリンクを選択して PR をレビューします。ただし、まだマージしないでください。

## まとめと次のステップ

この演習では、次の作業を行いました。

- Model Context Protocol (MCP) と、MCP サーバーが Copilot のツールを拡張する方法を学びました。
- Playwright MCP サーバーをワークスペースに追加しました。
- ブラウザーで機能を手動でテストし、リリース前に動作を確認しました。
- ブランチを公開して、フィルタリング機能のプルリクエストを開きました。

機能の**テスト**に MCP サーバーを使いました。MCP は Copilot を拡張する方法の 1 つです。次に、アクセシビリティレビューなどの作業を効率化する[カスタムエージェントを作成します][next-lesson]。

## リソース

- [MCP とは何か、なぜ注目されているのか][mcp-blog-post]
- [Microsoft Playwright MCP Server][playwright-mcp-server]
- [GitHub MCP Server][github-mcp-server]
- [GitHub MCP Registry][mcp-registry]
- [VS Code の MCP サーバー][vscode-mcp-config]

[previous-lesson]: ../2-agent-mode/
[next-lesson]: ../4-custom-agents/
[mcp-blog-post]: https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/
[github-mcp-server]: https://github.com/github/github-mcp-server
[mcp-registry]: https://github.com/mcp
[remote-github-mcp-server]: https://github.blog/changelog/2025-06-12-remote-github-mcp-server-is-now-available-in-public-preview/
[vscode-mcp-config]: https://code.visualstudio.com/docs/agents/reference/mcp-configuration
