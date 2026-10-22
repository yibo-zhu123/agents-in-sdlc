---
title: "演習 4 - エージェントの監視と管理"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

前の 2 つの演習では、ユーザー体験を改善して機能を追加する 3 つのタスクを Copilot cloud agent に依頼しました。Cloud agent は非同期で自律的に動作しますが、タスクの進行状況を監視することも大切です。

cloud agent に割り当てたタスクは、GitHub.com の[エージェントページ][agents-page]など、さまざまなツールで管理できます。この mission control では、オープン中のプルリクエスト (PR) があるエージェントタスクを確認できます。実行した操作を調べたり、進行中のセッションに指示を送ったりできます。

このレッスンでは、次の作業を行います。

- エージェントページで cloud agent のタスクを監視します。
- 実行中のセッションに指示を送り、機能の追加を依頼します。

## シナリオ

チームはエージェントにハイコントラストモードを依頼した後、ライトモードも追加することにしました。サイトのスタイルと切り替え機能を更新しているため、合わせて実装するのが合理的です。エージェントに指示を送り、ハイコントラストに加えてライトモードも追加してもらいます。

## Copilot cloud agent のタスクを確認する

Copilot cloud agent に割り当てたタスクの現在の状態を確認しましょう。

1. [Copilot agents ページ](https://github.com/copilot/agents)に移動します。
2. メインパネルと左側のパネルに表示されるタスクの一覧を確認します。割り当てた次のタスクが表示されます。
    - コードベースのドキュメントの更新
    - ゲームを変更する API の生成
    - Web サイトへのハイコントラストモードの追加
3. 実行中のタスクを選択し、Copilot が行った作業を確認します。たとえば、次の作業が含まれます。
    - リポジトリのチェックアウト
    - Copilot が作業する環境の作成
    - MCP サーバーのセットアップ
    - 割り当てられたタスクを完了するためのさまざまな作業

> [!NOTE]
> 表示される具体的な手順は、Copilot の作業状況やアプローチによって異なります。

4. 右側に表示されるプルリクエスト (PR) パネルも確認します。PR と変更されたファイルを確認して、作業をさらに監視できます。

## cloud agent に指示を送る

アクティブなタスクを確認したので、ハイコントラストモードの作業中にライトモードも追加するよう依頼します。

1. ハイコントラストモードのタスクに関連するセッションを選択します。Copilot が付けたタイトルや作業状況によって、正確な名前は異なります。

    ![mission control に表示されたアクセシビリティセッション](../../../_images/ex6-accessibility-session.png)

2. セットアップが完了して作業を開始したことを示すメッセージが表示されるまで、数分間セッションを確認します。
3. **Steer active session while Copilot is working** ダイアログに、次のプロンプトを入力します。

    ```plaintext
    While we are working on a high contrast mode, let's also add a light mode. There should be a switch for this mode as well where users can select their desired display mode.
    ```

    ![cloud agent タスクの steer active session while copilot is working ダイアログを強調表示したエージェントページのスクリーンショット](../../../_images/ex6-steer-cloud-agent-task.png)

4. <kbd>Enter</kbd> を押してプロンプトを送信します。
5. Copilot がプロンプトに応答し、作業計画に追加したことを確認します。

## Copilot に作業を任せる

前回と同じように、Copilot は更新されたタスクに取り組みます。メッセージを送信した時点で実行中の手順を完了した後、新しい依頼を作業に反映します。

今回も数分かかります。その間に一度立ち止まり、これまでに学んだ内容を振り返りましょう。

## まとめと次のステップ

Copilot cloud agent タスクを監視し、方向付ける中心的な場所である Copilot agents ページを確認しました。この mission control で、進行中と完了済みのタスクを追跡し、実行中の作業をレビューし、進行中のタスクに新しい指示を送って範囲を調整できます。

この演習では、次の作業を行いました。

- mission control とエージェントページで cloud agent のタスクを監視しました。
- 実行中のセッションに指示を送り、機能の追加を依頼しました。

Copilot がアクセシビリティ機能を実装している間に、[Copilot が作成したプルリクエストを改善する][next-lesson]方法を確認します。

## リソース

- [Copilot agents ページ][agents-page]
- [カスタムエージェントについて][custom-agents]

---

| [← 前のレッスン: カスタムエージェント][previous-lesson] | [次のレッスン: Copilot の作業を反復して改善する →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
