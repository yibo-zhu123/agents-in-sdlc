---
title: "レッスン 5 - quality-checks スキルをカスタマイズして使う"
description: "既存の quality-checks スキルを確認し、レポート形式をカスタマイズして、フィルタリング機能を検証します。"
authors:
  - geektrainer
lastUpdated: 2026-09-29
---

コードを書くことは、コードだけに取り組むことではありません。コードが動作することを手動で検証し、指示ファイルを使って標準に沿っていることも確認しました。では、テスト、lint、そのほかの継続的インテグレーション (CI) の作業はどうでしょうか。

このような作業には、**agent skill** が適しています。スキルを使うと、Copilot がこうした操作を適切に実行する方法を理解できます。

このレッスンでは、次の作業を行います。

- 既存の `quality-checks` スキルを確認します。
- 結果の形式をカスタマイズします。
- スキルを再読み込みして実行します。

## シナリオ

Tailspin Toys は、ユニットテスト、lint、型チェックに `quality-checks` スキルを使っています。チームは結果を読みやすくするため、レポートを改善したいと考えています。

## 指示、スクリプト、リソース

Agent skill は、エージェントが必要に応じて読み込む、再利用可能なタスクの指示、実行可能なスクリプト、補助リソースをまとめます。基本的には、スキル名を付けたフォルダーに `SKILL.md` ファイルを配置します。この Markdown ファイルには、スキルの内容を定義する `name` と `description` を持つフロントマター、スキルの概要、呼び出すタイミングに関するガイダンスを記載します。フォルダーにサブフォルダーを作り、スキルで使うスクリプトやほかのリソースを含めることもできます。

> [!NOTE]
> スキルにサブフォルダーや追加ファイルは必須ではありません。Tailspin Toys の `quality-checks` スキルには、プロジェクトの既存コマンドを使うため、`SKILL.md` だけが含まれています。

スキルをプロジェクトの `.github/skills` フォルダーに置くと、リポジトリの成果物としてチームで共有して再利用できます。ユーザーのスキルフォルダー `~/.copilot/skills` に置くこともできます。

## スキルを確認する

Tailspin Toys チームがユニットテスト、lint、型チェックの実行用に作成した `quality-checks` スキルを確認します。

1. Codespace に戻り、Codespaces エディターで `.github/skills/quality-checks/SKILL.md` を開きます。
2. 先頭の `name` と `description` を読みます。description は、Copilot がスキルを呼び出すタイミングを判断するのに役立ちます。
3. 指示を読み、テストと lint の手順がどのように説明されているか確認します。
4. スキルに **Results output formatting** セクションがまだないことを確認します。

## 変更前にスキルを実行する

Copilot CLI や自然言語でスキルを直接呼び出せます。Copilot に 3 つのチェックを実行してもらいましょう。

1. Interactive モードでフィルタリングの会話に戻ります。
2. 次のプロンプトを使います。

   ```plaintext
   Run the quality-checks skill for unit tests, lint, and type checks.
   ```

3. 最後に表示されるレポートを確認します。

## レポートをカスタマイズする

実行した作業、その成否、ツールが報告した内容が分かるレポートに改善しましょう。スキルを更新して、レポートを作成できるようにします。

1. `.github/skills/quality-checks/SKILL.md` に戻ります。
2. ファイルの末尾に次のセクションを追加します。

   ```markdown
   ## Results output formatting

   Upon completion, report each command that ran and whether it passed, failed, or was blocked. Include test counts, durations, errors, warnings, and other metrics only when the tool reports them. Identify the next action for any failure or blocker, and never describe a skipped or incomplete check as passed.
   ```

3. ファイルは自動的に保存されます。

## 更新したスキルを実行する

変更を加えたので、実際に動かしてみます。Copilot CLI では会話を再起動せずに編集したスキルを再読み込みできます。

1. 次のコマンドを入力します。

   ```plaintext
   /skills reload
   ```

2. 前とまったく同じプロンプトを使います。

   ```plaintext
   Run the quality-checks skill for unit tests, lint, and type checks.
   ```

3. 最後に表示されるレポートを確認し、最初のレポートと比較します。

## まとめと次のステップ

既存の agent skill をカスタマイズして使いました。このレッスンでは、次の作業を行いました。

- ユニットテスト、lint、型チェックを実行する `quality-checks` スキルを確認しました。
- 結果の形式をカスタマイズしました。
- スキルを再読み込みして実行しました。

この変更は、機能 PR でフィルタリング機能と一緒にリリースします。次に、[Playwright MCP サーバーを使って Copilot がサイトを直接操作できるようにします][next-lesson]。

## ほかのスキルの例

次のコミュニティの例は参考資料です。追加の作業ではありません。

- [Agent Skills 仕様][skill-spec]
- [コントリビューションワークフロー: `make-repo-contribution`][contribution-example]
- [要件ドキュメント: `prd`][prd-example]
- [図と同梱のエクスポートスクリプト: `drawio`][drawio-example]
- [ブラウザーテスト: `webapp-testing`][browser-example]

[previous-lesson]: ../4-custom-instructions/
[next-lesson]: ../6-mcp-playwright/
[skill-spec]: https://agentskills.io/specification
[contribution-example]: https://github.com/github/awesome-copilot/tree/main/skills/make-repo-contribution
[prd-example]: https://github.com/github/awesome-copilot/tree/main/skills/prd
[drawio-example]: https://github.com/github/awesome-copilot/tree/main/skills/drawio
[browser-example]: https://github.com/github/awesome-copilot/tree/main/skills/webapp-testing
