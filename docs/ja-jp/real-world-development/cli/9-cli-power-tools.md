---
title: "レッスン 9 - Copilot CLI の便利な機能"
description: "コンテキストの管理、モデルの選択、セッションの共有、cloud agent への任意の委任に使うスラッシュコマンドを確認します。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

多くの CLI ツールと同様に、GitHub Copilot CLI にもさまざまなスラッシュコマンドがあります。高度な機能、内部情報、追加の構成オプションを利用できます。これまでに `/diff`、`/mcp`、`/skills`、`/agent`、`/pr` などのコマンドを使いました。ここでは、さらに便利な機能を確認します。

このレッスンでは、次の作業を行います。

- `/context` と `/compact` を使い、Copilot が会話のコンテキストを管理する方法を確認します。
- `/model` で利用できるモデルを確認します。
- `/share` を使ってセッションをエクスポートまたは共有する方法を学びます。
- 並列作業、worktree、cloud agent への委任に使う任意のコマンドを確認します。

## シナリオ

CLI の中心的なワークフローは完了しました。次は、コンテキストの管理、モデルの切り替え、セッションの共有、[Copilot cloud agent][about-cloud-agent]への任意の委任など、ほかの機能を確認します。

## Copilot CLI のコンテキストを確認する

大きなタスクや複雑なタスクでは、モデルの最大コンテキストウィンドウに達することがあります。Copilot CLI は必要に応じて自動的に会話を圧縮します。スラッシュコマンドで自分で確認したり、圧縮したりすることもできます。

1. Codespace に戻り、まだ起動していない場合はリポジトリのルートから Copilot CLI を起動します。
2. 次のコマンドを入力します。

   ```plaintext
   /context
   ```

3. モデル、現在のトークン使用量、システム指示、ツール、メッセージ、空き領域の内訳を確認します。
4. 会話を圧縮します。

   ```plaintext
   /compact
   ```

5. もう一度 `/context` を入力して結果を比較します。会話がまだ短い場合、大きな変化がないこともあります。

> [!NOTE]
> コンテキストウィンドウがいっぱいになると、Copilot CLI は自動的にコンテキストを圧縮します。圧縮するタイミングを選びたい場合は `/compact` を使います。関係のない別のタスクを始めるときに会話を新しくするには `/clear` または `/new` を使います。

## モデルを選ぶ

モデルごとに得意なことが異なり、開発者の好みもさまざまです。Copilot CLI では利用可能なモデルを一覧表示して、使用するモデルを選択できます。

1. 次のコマンドを入力します。

   ```plaintext
   /model
   ```

2. 利用可能なモデルと使用状況を確認します。
3. 現在のモデルを使うか、別のモデルを選ぶか、<kbd>Esc</kbd> を押して一覧を閉じます。

## セッションを共有する

チームで学んだことを共有すると、AI ツールの活用方法を改善できます。`/share` コマンドを使うと、セッションを Markdown または HTML ファイルにエクスポートし、共有リンクを作成し、GitHub gist として公開できます。

1. `/help` を入力し、インストール済みバージョンで `/share` が使えるか確認します。
2. セッションを共有する場合は、ローカルの Markdown ファイルにエクスポートする `/share file` など、目的に合う方法を選びます。
3. エクスポートした内容を、ほかの人に送ったり公開したりする前にレビューします。セッションのエクスポートには、プロンプト、応答、プロジェクトの詳細が含まれる場合があります。

リンクや gist の公開は任意です。チームが共有を意図していないリポジトリや会話の内容は公開しないでください。

## 任意: 作業を並行したり委任したりする

ワークショップの中心的な内容は完了しました。Copilot CLI には、より大きなタスクに使えるコマンドもあります。

- `/fleet` は独立したサブタスクをサブエージェントに割り当てて並列実行できます。
- `/worktree` は別のタスク用に、分離された Git worktree を作成できます。
- `/delegate` はタスクを Copilot cloud agent に送信します。非同期で作業してプルリクエストを作成する場合があります。

これらのコマンドは worktree を追加したり、リモートで作業したりする可能性があるため任意です。試すときは範囲を明確にした新しいタスクから始め、通常のワークフローに沿って結果をレビューしてください。非同期エージェントについて詳しく学ぶには、[cloud agent ワークショップ][cloud-workshop]に進みます。

## まとめと次のステップ

Copilot CLI のスラッシュコマンドを使うと、構成、セッションの共有、内部状態の確認などを行えます。このレッスンでは、次の作業を行いました。

- `/context` と `/compact` を使い、Copilot が会話のコンテキストを管理する方法を確認しました。
- `/model` で利用可能なモデルを確認しました。
- `/share` を使ってセッションをエクスポートまたは共有する方法を学びました。
- 並列作業、worktree、cloud agent への任意の委任に使うコマンドを確認しました。

Copilot CLI には、ほかにも多くのスラッシュコマンドがあります。[学んだ内容を振り返り][next-lesson]、学習を続ける方法を確認しましょう。

## リソース

- [Copilot CLI コマンドリファレンス][cli-reference]
- [Copilot CLI のコンテキスト管理][context-management]
- [Copilot cloud agent について][about-cloud-agent]

[previous-lesson]: ../8-create-pull-request/
[next-lesson]: ../10-review/
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[context-management]: https://docs.github.com/copilot/concepts/agents/copilot-cli/context-management
[about-cloud-agent]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-workshop]: ../../cloud/
