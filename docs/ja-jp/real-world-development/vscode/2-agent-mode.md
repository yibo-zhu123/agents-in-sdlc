---
title: "演習 2 - Copilot Agent Mode で機能を追加する"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

アプリケーションへの小さな変更でも、複数のファイルを更新し、テストを実行する必要があることがあります。開発者は通常、必要なファイルを見つけて変更し、テストを実行し、デバッグし、変更し忘れたファイルを見つけて、再度更新します。こうした作業は何度も続くことがあります。

Copilot Agent Mode を使うと、IDE 内でより自律的に作業できます。開発者と同じように、まず既存のプロジェクト構成を調べ、必要な変更を行い、テストなどのタスクを実行して、見つかった問題を自動的に修正します。Agent Mode でサイトに新機能を追加してみましょう。

> [!NOTE]
> 名前は似ていますが、エージェントモードと cloud agent は異なる使い方をします。エージェントモードは IDE 内で作業し、すばやく対話しながら進められます。Cloud agent はチームの一員のように非同期で作業し、Issue とプルリクエストを通してやりとりします。

この演習では、次のことを学びます。

- Copilot Agent Mode がプロジェクトを調査し、関連するファイルを特定して、複数のファイルを連携して変更する方法。
- GitHub Copilot Agent Mode を使って、UI とデータ層にまたがる機能を実装する方法。
- コードベースにマージする前に、Copilot Agent Mode が生成した変更とテストをレビューする方法。

## シナリオ

ゲームの一覧が増えてきたため、ユーザーがカテゴリとパブリッシャーで絞り込めるようにします。前の演習でパブリッシャーヘルパーを追加しました。次は、残りのデータ層、UI、テストの作業を Copilot Agent Mode で完了します。

## Tailspin Toys の Web サイトを実行する

変更を加える前に、Tailspin Toys の Web サイトを見て、現在の機能を確認しましょう。

この Web サイトは、開発者をテーマにしたボードゲームのクラウドファンディングプラットフォームです。ゲームの一覧と詳細を表示します。Web サイトは単一の Astro アプリで、ビルド時にページを静的 HTML としてレンダリングします。ページは frontmatter 内で Drizzle ORM を使ってローカル SQLite データベースに直接クエリします。独立したバックエンド API やクライアント側 UI フレームワークはありません。再利用可能なデータアクセスヘルパーは `src/lib/` にあり、操作性は標準 DOM API を使った小規模な Astro `<script>` で追加します。

### Web サイトを起動する

`npm run dev` コマンドを使うと、単一の Astro 開発サーバーを起動できます。GitHub Codespace で次の手順を実行します。

1. Codespace に戻ります。前の演習から `main` にいるはずです。
2. <kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押して、Codespace 内に新しいターミナルを開きます。
3. フィルタリング作業用のブランチを作成して切り替えます。

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. 次のコマンドで Web サイトを起動します。

   ```bash
   npm run dev
   ```

   Astro 開発サーバーの準備ができると、次のような URL が表示されます。

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> `http://localhost:4321` を開くように促すダイアログが表示された場合は、**x** を選択して閉じます。

5. ターミナルに表示される Astro サーバーの URL `http://localhost:4321` を、Mac では <kbd>Command</kbd>、Windows/Linux では <kbd>Ctrl</kbd> を押しながら選択して、Web サイトを開きます。

> [!NOTE]
> Codespace のターミナルで localhost URL を選択すると、`https://<your-codespace-name>-4321.app.github.dev/` に自動的にリダイレクトされます。Web サーバーがホストされている Codespace へのプライベートトンネルです。

### Web サイトを確認する

Web サイトが起動したら、次の主要な機能を確認します。

- **ホームページ**: タイトル、画像、説明とともにボードゲームの一覧を表示します。
- **ゲーム詳細ページ**: ゲームを選択すると、タイトル、説明、パブリッシャー、カテゴリなどの情報を含む詳細ページが表示されます。

## Copilot でバックログを確認する

> [!TIP]
> **Copilot Chat を開く**
>
> 次の演習を始める前に Codespace に戻り、Copilot Chat パネルを開いて **New Chat** を選択し、新しい会話を開始します。モードとモデルの選択は演習ごとに異なります。必要な場合は手順内で説明します。

Web サイトの初期実装は動作していますが、機能を追加したいと考えています。まずバックログを確認しましょう。テンプレートからプロジェクトをセットアップすると、Issue のバックログが自動的に作成されます。GitHub Copilot に項目を表示してもらいます。

1. Chat ビューのエージェント一覧から **Agent** を選択します。**Agent** エージェントは自律的に計画を立て、複数のファイルを変更し、ターミナルコマンドを実行して、ツールを呼び出します。

   ![Chat ビューの agent picker を示すスクリーンショット](../../../_images/shared-chat-mode-selector.png)

2. 利用可能なモデルの一覧から **Claude Sonnet 4.5** を選択します。

> [!CAUTION]
> このワークショップの作成者が特定のモデルを推奨しているわけではありません。ワークショップの作成に Claude Sonnet 4.5 を使ったため、比較的安定した体験が得られることを期待して手順に記載しています。ただし、LLM は確率的に動作するため、案内と異なる提案が行われることがあります。これは通常の動作です。

> [!NOTE]
> 確率的な動作のため、Copilot が別の MCP コマンドを使用する場合がありますが、タスクは完了できるはずです。

3. 次のプロンプトを Copilot に送って、Issue のバックログを確認します。

   ```plaintext
   Please show me the backlog of items from my GitHub repository. Help me prioritize them based on those which will be most useful to the user.
   ```

4. **Continue** を選択して、すべての Issue を一覧表示するコマンドを実行します。
5. 生成された Issue の一覧を確認します。

Copilot がユーザーにとって有用だと考える順に項目を優先順位付けすることも確認できます。

## 指示ファイルを確認する

コードを生成するようエージェントに依頼する前に、Copilot にコンテキストを提供する指示ファイルを確認します。この Web サイトに機能を追加する方法を説明する [UI 指示ファイル](https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md)を使います。

1. Codespace で `.github/instructions/ui.instructions.md` に移動します。
2. 機能追加の方法に関する全体的な指針を確認します。次の内容が含まれています。
   - アーキテクチャの概要
   - コンポーネント設計、テスト容易性、アクセシビリティの原則
   - Astro、Tailwind CSS、Drizzle などのファイル形式ごとの指示へのリンク

> [!TIP]
> 指示ファイルから、ほかの指示ファイルとプロジェクト内のファイルの両方を参照できます。パスは指示ファイルの場所からの相対パスです。指示を再利用し、大きな指示を小さなファイルに分割して、Copilot に例を示せます。

## フィルタリング機能を実装する

フィルタリングを完成させるには、少なくともアプリケーションに 3 つの変更が必要です。

- データ層 (`src/lib/`) に残りのフィルタリングロジックを追加または改善します。
- フィルタリング動作のテストを追加または更新します。
- ゲーム一覧ページにフィルタリング UI を追加します。

さらに、コードベースにマージする前にテストを実行して、成功することを確認する必要があります。Copilot Agent Mode に作業を依頼しましょう。

1. 現在の会話を続けるか、**New Chat** を選択して新しい会話を開始します。
2. **Add Context**、**Instructions**、**ui** の順に選択して、指示ファイルを追加します。

   ![UI 指示ファイルを選択する例を示すスクリーンショット](../../../_images/ex3-select-instructions-file.png)

3. Chat ビューのエージェント一覧で **Agent** が選択されていることを確認します。

   ![Chat ビューの agent picker を示すスクリーンショット](../../../_images/shared-chat-mode-selector.png)

4. モデルに **Claude Sonnet 4.5** が選択されていることを確認します。
5. バックログにある関連 Issue の要件に基づいて機能を実装するよう、Copilot に依頼します。

   ```plaintext
   Please update the site to include filtering by publisher and category based on the requirements from the related GitHub issue in the backlog. A publishers helper already exists from the previous exercise, so preserve and refine that work as needed while completing the remaining data-layer, UI, and tests. Ensure all tests are passing before completion. The server is already running, so you do not need to start it up.
   ```

6. Copilot がプロジェクトを調査し、目的の機能に関係するファイルを特定します。データ層のヘルパー、UI、テストを探して、ファイルの変更とテストの実行を始めます。

   ![Copilot がプロジェクトファイルを調査しているスクリーンショット](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot はプロジェクトの調査、変更、テストの実行など、複数のタスクを行います。作業の複雑さやコードベースの大きさによっては、数分かかる場合があります。作業中、コードエディターに **Keep** と **Undo** が表示されることがあります。完了すると、変更ごとに **Keep** または **Undo** を選択できます。作業中に選択する必要はありません。

7. Copilot に促されたら **Continue** を選択してテストを実行します。

   ![Copilot Chat パネルがテストの実行許可を求めているダイアログのスクリーンショット](../../../_images/ex3-agent-mode-run-tests.png)

8. 作業の途中で待機が発生したり、テストが失敗したりする場合があります。問題ありません。Copilot はエラーが見つからなくなるまで、コード生成とテストを繰り返します。

   ![Copilot Agent Mode の会話が完了した状態を示すスクリーンショット](../../../_images/ex3-agent-mode-proposed-changes.png)

9. 生成されたコードに問題がないか確認します。

> [!CAUTION]
> Copilot などの AI ツールが生成したコードは、必ずレビューしてください。

10. Web サイトを起動したブラウザーに戻り、新機能を試します。
11. 動作とコードを確認したら、Copilot Chat ウィンドウで **Keep** を選択します。

## まとめと次のステップ

GitHub Copilot Agent Mode で Tailspin Toys の Web サイトに機能を追加する方法を学びました。次の作業を行いました。

- GitHub Copilot Agent Mode を使って、UI とデータ層にまたがる機能を実装しました。
- Copilot Agent Mode がプロジェクトを調査し、関連するファイルを特定して、複数のファイルを連携して変更する方法を確認しました。
- コードベースにマージする前に、Copilot Agent Mode が生成した変更とテストをレビューしました。

次に、[Playwright MCP サーバーで機能をテストし][next-lesson]、プルリクエストを作成します。

### ボーナス演習 - ページネーションを実装する

ゲーム一覧が増えると、ページネーションが必要になります。この演習で学んだことを使い、Copilot にページネーションの実装を依頼します。次の点を検討してください。

- 既存の指示ファイルを使うなど、ベストプラクティスに従います。
- ページサイズをユーザーが選べるようにするか、固定値にするかなど、ページネーションの設計を決めます。
- 希望どおりに実装してもらえるよう、Copilot へのプロンプトに必要な指針を含めます。
- Copilot に変更を依頼してコンテキストを提供し、反復が必要な場合があります。Copilot を使うときの通常の流れです。

## リソース

- [Copilot の ask、edit、agent モード: 機能と使い分け][choose-mode]
- [VS Code のエージェントモード][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
