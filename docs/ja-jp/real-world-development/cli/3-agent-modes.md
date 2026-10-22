---
title: "レッスン 3 - エージェントモード: Plan と Autopilot"
description: "Plan で進め方に合意し、Autopilot で Issue に基づいてフィルタリング機能を構築し、Interactive モードで結果をレビューします。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

まず、プロジェクトに小さな機能を追加しました。大きな変更には、より堅牢なプロセスが必要です。GitHub Copilot CLI は組織の既存のワークフローに沿って動作し、適切な方法で必要なものを構築できるようにします。このレッスンから、エージェントを使った一般的な開発プロセスに沿って進めます。Issue を基に機能を作成し、コードが有効で動作することを確認して、最終的にプロジェクトへマージします。

> [!NOTE]
> 機能のワークフローを進める間は、同じ会話とブランチを使います。通常はファイルの種類ごとに異なるブランチや PR を作りますが、ここでは主な概念に集中できるよう簡略化しています。

このレッスンでは、次の作業を行います。

- GitHub Issue から Copilot の新しい会話を始めます。
- Plan モードで要件を定義します。
- Autopilot モードで機能を実装します。
- コードをレビューします。
- 転送されたブラウザーで機能を手動で検証します。

この機能の作業を続ける中で、リポジトリの指示を更新し、既存の `quality-checks` スキルをカスタマイズし、MCP による検証を追加し、QA エージェントを作成して、機能の PR を開きます。

## シナリオ

Tailspin Toys のカタログは拡大しており、訪問者はカテゴリとパブリッシャーでゲームを絞り込める必要があります。バックログの Issue には機能が記載されていますが、カテゴリの組み合わせなど、実装前に合意が必要な詳細があります。Plan モードでこれらの判断を整理し、その後、範囲を決めて Autopilot に実装を許可します。

## 背景

AI コーディングエージェントを開発ワークフローに取り入れても、基本は変わりません。むしろ、より重要になります。多くの開発者は次のような流れで作業します。

1. 実装内容を説明する Issue から始めます。
2. 構築するものを計画します。
3. コードを作成してレビューします。
4. テストを実行してコードを検証します。
5. 新しい機能を手動で検証します。
6. プルリクエスト (PR) を作成します。
7. コードレビューと継続的インテグレーションが成功したら、コードをマージします。

> [!NOTE]
> 手順の詳細はチームや組織によって異なりますが、多くの場合、ここに示した流れと同様です。

この標準的なアプローチを守ることで、AI が生成したコードが要件を満たし、手書きのコードと同じ審査プロセスを通るようにできます。

## 会話モード

**会話モード**は、エージェントにどの程度の自律性を与えるかを制御します。<kbd>Shift</kbd>+<kbd>Tab</kbd> を押してモードを切り替えます。

- **Interactive**: エージェントと一緒に作業します。エージェントは変更を提案し、次に進む前に入力を待ちます。
- **Plan**: エージェントが計画を作成します。プロジェクトファイルの編集は行いません。
- **Autopilot**: エージェントが自律的にコードを書き、テストを実行し、タスクが完了するまで反復します。

まず Plan モードで計画を確認し、その後 Autopilot で実装します。

## Issue から始める

フィルタリング機能の作業を始める前に、Codespace に戻ってリポジトリとターミナルを準備します。

1. Codespace に戻ります。停止している場合は再起動します。
2. 星評価の PR がマージ済みであることを確認します。
3. ターミナルが開いていない場合は、<kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押します。
4. `main` を更新して、フィルタリング作業用のブランチを作成します。

   ```bash
   git checkout main
   git pull --ff-only
   git checkout -b game-filters-cli
   ```

5. Copilot CLI を起動します。

   ```bash
   copilot --yolo
   ```

6. <kbd>Tab</kbd> を 2 回押して **Issues** タブを開きます。
7. <kbd>A</kbd> を押してすべての Issue を表示します。
8. 矢印キーで **Allow users to filter games by category and publisher** というタイトルの Issue を選びます。
9. <kbd>C</kbd> を押して Issue をプロンプトに追加し、**Session** タブに戻ります。

プロンプトの先頭に `#7` (または同様の番号) が表示されます。`#` を使うと、GitHub 上の Issue やプルリクエスト (PR) をコンテキストに追加できます。

## フィルタリング機能を計画する

計画を立てると、実装を開始する前に機能の進め方やタスクを相談できます。複雑な作業は、少し時間をかけて計画することをお勧めします。Plan モードに切り替えて、Copilot に計画を作成してもらいます。

1. <kbd>Shift</kbd>+<kbd>Tab</kbd> を押して Plan モードに切り替えます。プロンプトの下にあるモード表示が **Plan** になっていることを確認します。
2. 前の手順で追加した Issue の参照の後に、次のプロンプトを入力します。

   ```plaintext
   Create a plan for implementing this feature.
   ```

Copilot はプロジェクトを調査して、適切な方法を判断しながら計画を作成します。

3. フィルタリング機能の動作について質問されたら、希望に沿って回答します。どの選択肢にも間違いはありません。
4. 計画が完成したら、<kbd>Control</kbd>+<kbd>E</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>E</kbd> (Windows/Linux) を押して計画を展開します。
5. 計画を上下にスクロールして確認します。
6. 判断した内容と合わない部分があれば、Copilot に修正を依頼します。

## Autopilot を承認する

計画を作成してレビューできたので、実装に進みます。Autopilot を使って Copilot に作業を任せましょう。

Autopilot を使うと、Copilot は問題が解決したと判断するまで作業を反復します。

1. **Accept plan and build on autopilot (recommended)**、またはインストール済みバージョンで同様の名前が付いた選択肢を選びます。
2. プロンプトの下にあるモード表示が **Autopilot** になっていることを確認します。
3. Copilot が計画に沿って反復し、コードを生成してテストを実行する様子を確認します。

> [!NOTE]
> 計画を承認すると、実装がすぐに始まることがあります。承認前に計画をレビューしてください。Copilot が依存関係の不足やポートの競合を報告した場合は、チェックが完了したと判断する前にセットアップの問題を解決します。

## 実装をレビューして検証する

ほかのコードと同様、生成されたコードもマージする前にレビューする必要があります。コードを確認し、サイトを起動して正常に動作することを確認します。

1. <kbd>Shift</kbd>+<kbd>Tab</kbd> を押して Interactive モードに切り替えます。モード表示が **Plan** または **Autopilot** ではないことを確認します。
2. `/diff` を入力し、フィルタリングの実装とテストを確認します。
3. 結果が Issue と計画時に決めた内容に合っているか確認します。
4. レビューしたら <kbd>Esc</kbd> を押して差分ビューを閉じます。
5. プロジェクトのチェック結果を確認し、失敗があれば Copilot に修正を依頼します。

## 新機能を試す

コードはよさそうですが、実際に動作するでしょうか。前と同様にアプリを起動し、Codespaces の転送ポートからサイトを開きます。

1. Copilot にアプリの起動を依頼します。

   ```plaintext
   Start the app so I can try the filtering feature in my browser. Tell me the URL and leave the server running.
   ```

2. Codespaces でポート `4321` が利用可能になったら **Open in Browser** を選択します。
3. カテゴリのフィルタリング、パブリッシャーのフィルタリング、および計画で合意した組み合わせを試します。
4. リセットと該当結果がない場合の動作が、Issue と決定事項に合っていることを確認します。
5. Codespace に戻り、Copilot に起動した開発サーバーの停止を依頼します。

## まとめと次のステップ

異なる会話モードを使って機能を構築し、レビューしました。このレッスンでは、次の作業を行いました。

- GitHub Issue から Copilot の新しい会話を始めました。
- Plan モードで要件を定義しました。
- Autopilot モードで機能を実装しました。
- コードをレビューしました。
- 転送されたブラウザーで機能を手動で検証しました。

次に、[カスタム指示を使って][next-lesson]コーディング規則に沿ったコードが生成されるようにします。

## リソース

- [GitHub Copilot CLI の Autopilot][autopilot]
- [Copilot CLI コマンドリファレンス][cli-reference]

[autopilot]: https://docs.github.com/copilot/concepts/agents/copilot-cli/autopilot
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[previous-lesson]: ../2-add-star-rating/
[next-lesson]: ../4-custom-instructions/
