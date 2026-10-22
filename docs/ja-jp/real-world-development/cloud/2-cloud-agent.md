---
title: "演習 2 - GitHub Copilot cloud agent"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 前のレッスン: カスタム指示][previous-lesson] |
|:--|

技術的負債に悩まされていない組織は、ほとんどありません。未解決のセキュリティ問題、更新が必要なレガシーコード、実装する時間がなくバックログに残った機能リクエストなどがあります。GitHub Copilot の cloud agent は、コードの更新や機能の追加などを自律的に実行できます。作業が終わると、人がレビューできるドラフト PR を作成します。面倒な作業を任せて開発プロセスを加速し、開発者がより大きな課題に集中できるようになります。

この演習では、Copilot cloud agent を使って次の作業を行います。

- コード生成環境をカスタマイズします。
- 操作が安全に行われるようにします。
- Issue のスコープを明確にする重要性を確認します。
- Issue を Copilot に割り当てます。

## シナリオ

Tailspin Toys には対応したい技術的負債があります。サイトの初期バージョンを開発した委託先はドキュメントを十分に整備していませんでした。実際には、ドキュメントがまったくありません。まず、アプリケーション内のエクスポートされた関数すべてに TSDoc のドキュメントコメントを追加します。

また、デザインチームはゲームを見つけやすくしたいと考えています。各ゲームの詳細ページに、同じカテゴリに属するほかのゲームを表示し、閲覧を続けられるようにします。まだ完成度の高い実装は必要なく、ユーザー体験 (UX) の受け入れテストに使えるものを求めています。現在はブロッカーですが、優先度の高い別の Issue があります。

どちらも優先順位が下がりがちなタスクの例で、Copilot cloud agent に割り当てるのに適しています。cloud agent は非同期で作業するため、開発者は別のタスクを進めながら、後で戻って作業をレビューできます。

## GitHub Copilot cloud agent について

[GitHub Copilot cloud agent](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent) は、人間の開発者と同じようにバックグラウンドでタスクを実行できます。たとえば、[GitHub Issue を Copilot に割り当てる][assign-issue]方法があります。割り当て後、Copilot は進行状況を追跡するドラフトプルリクエストを作成し、環境を設定して作業を始めます。実行中または完了後にセッションを確認できます。提案された解決策のレビュー準備ができると、プルリクエストで通知されます。

## 範囲を明確にした指示の重要性

GitHub Copilot に魔法はありません。考える作業を省くことはできません。一見簡単な操作でも、細部を確認すると複雑なことがあります。[Copilot cloud agent に依頼するタスクの範囲を明確にしてください][cloud-agent-best-practices]。AI ペアプログラマーとして扱い、段階を踏んで学び、試しながら調整します。生成 AI を加えても、ソフトウェア開発の基本は変わりません。

## このリポジトリのカスタム指示

前の演習では、カスタム指示と Copilot の動作に与える影響を確認しました。すでにカスタム指示の演習を完了していれば、`.github/copilot-instructions.md` の効果を見ています。まだの場合は、cloud agent に作業を割り当てる前に、このリポジトリにある指示ファイルを読み取り専用で確認し、後でその効果を確かめましょう。

リポジトリの GitHub Web UI、または起動済みの codespace で次のファイルを開きます。

- `.github/copilot-instructions.md` — **Code standards** セクションを確認し、特に TypeScript の規則と TSDoc ドキュメントコメントの要件に注目します。
- `.github/instructions/unit-tests.instructions.md` — `applyTo` フロントマターを確認します。この指示は `**/*.test.ts` ファイルに適用されます。

次のセクションで `Code lacks documentation` Issue を cloud agent に割り当てるときは、後でプルリクエストを確認し、これらの指示ファイルに由来する TSDoc コメント、TypeScript 規則、コメントヘッダーが使われているか確認します。

## Copilot cloud agent の開発環境をセットアップする

コードを作成するときは、通常、環境を準備し、必要なツールや依存関係を導入するセットアップスクリプトを実行します。Copilot にタスクを割り当てる場合も同じです。Copilot は SWE (ソフトウェアエンジニア) のような方法で作業します。

Cloud agent は作業環境に [GitHub Actions][github-actions] を使います。作業前に実行する[セットアップワークフロー][setup-workflow]を `.github/workflows/copilot-setup-steps.yml` に作成すると、必要な開発ツールと依存関係を利用できるようになります。このラボでは、学習に集中できるよう事前に構成済みです。単一の Astro アプリのために、Node.js、プロジェクトの依存関係、エンドツーエンドテスト用ブラウザー、移行とシードを済ませた SQLite データベースを用意します。

```yaml
name: "Copilot Setup Steps"

# Allows you to test the setup steps from your repository's "Actions" tab
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # Set the permissions to the lowest permissions possible needed for *your steps*. Copilot will be given its own token for its operations.
    permissions:
      # If you want to clone the repository as part of your setup steps, for example to install dependencies, you'll need the `contents: read` permission. If you don't clone the repository in your setup steps, Copilot will do this for you automatically after the steps complete.
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # Frontend / app setup - Node.js (the whole app is now Astro + Drizzle/libSQL)
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

      # Migrate + seed the local SQLite database so builds and tests have data.
      - name: Set up the database
        run: npm run db:setup
```

このファイルは通常の GitHub Actions ワークフローと似ていますが、次の点に注目してください。

- `copilot-setup-steps` という単一のジョブがあります。Copilot がプルリクエストで作業を始める前に GitHub Actions で実行されます。
- `workflow_dispatch` トリガーを使うと、リポジトリの **Actions** タブからワークフローを手動で実行できます。Copilot を待たずにワークフローの動作を確認するのに便利です。

## ドキュメントを追加する

ドキュメントの重要性は誰もが理解していますが、多くのプロジェクトでは情報が古いか、ドキュメントそのものがありません。この技術的負債が放置されると、生産性が下がり、コードベースの保守や新しい開発者の参加が難しくなります。Copilot はドキュメントの作成に役立ちます。バックグラウンドで必要なドキュメントを生成するこの作業を、cloud agent に割り当ててみましょう。次の演習では作業をレビューします。

1. 新しいブラウザータブで github.com のリポジトリに移動します。
2. **Issues** タブを選択します。
3. **New issue** を選択して Issue の作成画面を開きます。
4. **Blank issue** を選択します。
5. **Title** に `Code lacks documentation` と入力します。
6. **Description** に次の内容を入力します。

    ```plaintext
    Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
    ```

7. **Create** を選択して Issue を作成します。
8. 右側の **Assign to Copilot** を選択して割り当て画面を開きます。

  ![Issue に Copilot を割り当てる画面](../../../_images/shared-assign-copilot.png)

9. **Assign** を選択します。

  ![Copilot の割り当て詳細](../../../_images/ex4-assign-copilot-details.png)

10. **Pull Requests** タブを選択します。
11. 新しく作成されたプルリクエスト (PR) を開きます。タイトルは `[WIP]: Code lacks documentation` のようになります。PR がまだ表示されない場合は、少し待ってからブラウザーを更新します。
12. 数分後、Copilot が To Do リストを作成します。

> [!NOTE]
> Copilot の To Do リストが PR に表示されるまで、数分かかる場合があります。Copilot は環境を作成し (先ほどのワークフローを実行します)、プロジェクトを分析して、問題に取り組む方法を決めています。

13. リストと、Copilot が実行するタスクを確認します。
14. プルリクエストのタイムラインを下にスクロールし、Copilot が Issue に取り組み始めたことを示す更新を確認します。
15. **View session** ボタンを選択します。

  ![Copilot セッションの表示](../../../_images/ex4-view-session.png)

> [!CAUTION]
> 更新された表示を確認するために、ウィンドウの更新が必要な場合があります。

16. セッションをスクロールして、Copilot がどのように問題を解決するか確認します。コードを調査して状態を理解し、適切な計画を立てて、コードを作成する様子を確認できます。

完了までには数分かかるでしょう。Copilot cloud agent の主な目的の 1 つは、非同期でタスクを実行し、ほかの作業に集中できるようにすることです。この機能を活用して別のタスクも cloud agent に割り当てた後、アプリケーションの機能追加に取り組みます。

## ゲーム詳細ページに関連ゲームを追加する

GitHub Copilot cloud agent の利点の 1 つは、作業を分担できることです。デザインチーム向けに関連ゲームのセクションを追加する作業に時間がかかるかどうかに関わらず、その間にほかの作業を進められます。cloud agent に割り当ててみましょう。

1. github.com のリポジトリに戻ります。
2. **Issues** タブを選択します。
3. **New issue** を選択して Issue の作成画面を開きます。
4. **Blank issue** を選択します。
5. **Title** に `Show related games on the game details page` と入力します。
6. **Description** に次の内容を入力します。

    ```markdown
    We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
    ```

7. **Create** を選択して Issue を作成します。
8. 右側の **Assign to Copilot** を選択して割り当て画面を開きます。

  ![Issue に Copilot を割り当てる画面](../../../_images/shared-assign-copilot.png)

9. **Assign** を選択します。

まもなく、Issue の最初のコメントに 👀 が表示されます。Copilot が作業を開始した合図です。

![Copilot が Issue に取り組んでいることを示す目の絵文字](../../../_images/ex4-issue-eyes-emoji.png)

Copilot は 2 つ目の依頼にも取り組んでいます。Copilot cloud agent は SWE と同じように動作するため、作業中に常に監視する必要はありません。完了してからレビューできます。次はカスタムエージェントを作成して使います。

## まとめと次のステップ

[GitHub Copilot cloud agent][copilot-agents]を使うと、Issue を Copilot に割り当て、非同期で作業させられます。技術的負債への対応、新機能の作成、別のフレームワークへのコード移行などに利用できます。

この演習では、次の作業を行いました。

- コード生成環境をカスタマイズしました。
- 操作が安全に行われるようにしました。
- Issue のスコープを明確にする重要性を確認しました。
- Issue を Copilot に割り当てました。

cloud agent がバックグラウンドで作業している間に、カスタムエージェントを作成して使います。[Copilot cloud agent は MCP サーバー][cloud-agent-mcp]も使用でき、前のモジュールで確認したカスタム指示も利用できます。

## リソース

- [Copilot cloud agent について][copilot-agents]
- [GitHub Issue を Copilot に割り当てる][assign-issue]
- [Copilot cloud agent のセットアップワークフローのベストプラクティス][cloud-agent-best-practices]

| [次のレッスン: カスタムエージェント →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
