---
title: "レッスン 4 - カスタム指示で Copilot を案内する"
description: "リポジトリの指示を確認し、ドキュメント標準を追加して、フィルタリングのコードに適用します。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

生成 AI を使ううえで、コンテキストは重要です。特定の方法でタスクを進めたい場合は、Copilot がその指針を参照できるようにします。[指示ファイル][instruction-files]は、必要なコードだけでなく、その構成方法も伝えます。フィルタリング機能を構築したので、Copilot が参照した指示を確認し、ドキュメント標準を追加してコードに適用します。

このレッスンでは、次の作業を行います。

- リポジトリの指示とパス別の指示ファイルがエージェントに伝わる方法を確認します。
- 指示ファイルを更新して、コーディング標準に従うようにします。
- 指示ファイルがコードに与える影響を確認します。

## シナリオ

Tailspin Toys には、開発方法に関するガイドラインと要件があります。

- コメントには、コードの言い換えではなく、意図や自明でない判断を記載します。
- `db/` と `src/lib/` のエクスポートされた関数には、目的、パラメーター、戻り値を説明する TSDoc/JSDoc を記載します。`db` 引数を注入できる場合は、そのことも説明します。
- 再利用可能な Astro コンポーネントには `Props` の契約を記載し、関連するコードを変更した場合はコメントも最新の状態にします。
- 既存の書式設定と lint の指示を維持します。

## 指示ファイル

カスタム指示を使うと、コーディングスタイルや要件を Copilot に伝え、より適切な提案やコードスニペットを得られます。好みのコーディング規則、ライブラリ、コメントの書き方などを指定できます。リポジトリ全体に適用する指示も、特定のファイルやタスクにだけ適用する指示も作成できます。

指示ファイルには次の 2 種類があります。

- `.github/copilot-instructions.md` は、リポジトリへのすべてのリクエストで Copilot に送られる単一の指示ファイルです。ほとんどのリクエストに関係するプロジェクト全体の情報を記載します。
- `.github/instructions/*.instructions.md` は、特定の言語、ファイル形式、タスク向けのガイドラインを記載します。

> [!NOTE]
> ほかの指示形式への対応状況は環境によって異なります。特定の形式を使う前に、[カスタム指示の対応状況][custom-instructions-support]を確認してください。

## このプロジェクトのカスタム指示ファイルを確認する

スタータープロジェクトには指示ファイルが用意されています。変更前に内容を確認して、効果を比べられるようにします。

1. Codespace に戻ります。
2. Codespaces エディターで (ターミナルではなく) `.github/copilot-instructions.md` を開きます。
3. プロジェクトの簡単な説明とコーディングに関するガイダンスを確認します。この指示は、このリポジトリでの Copilot とのすべてのやりとりに適用されます。
4. `.github/instructions` フォルダーを開いて内容を確認します。Astro ファイル、Drizzle データ層、テストなどの指示があります。
5. `.github/instructions/unit-tests.instructions.md` を開きます。先頭の `applyTo` フィールドは、その指示が適用されるファイルの glob を指定します。この例では `**/*.test.ts` に一致する TypeScript テストファイルが対象です。
6. `.github/instructions/drizzle.instructions.md` を開き、ほかの指示ファイルやプロジェクト内の既存ファイルへのリンクを確認します。これらのリンクを使うと、指示を再利用可能な小さなファイルに分割し、Copilot に例を示せます。

## チームのガイダンスに合わせて指示ファイルを更新する

現在のファイルはよい出発点ですが、不足している点があります。中心となる `copilot-instructions.md` にルールを追加して、新しく生成する TypeScript に TSDoc コメントを付けるようにします。

1. `.github/copilot-instructions.md` を開きます。
2. 35 行目付近にある **Code formatting guidance** セクションを探します。
3. 次の箇条書きを追加します。

   ```markdown
   - All new TypeScript should contain TSDocs comments for documentation purposes.
   ```

4. ファイルは自動的に保存されます。

## 更新した指示を適用する

Copilot CLI は会話の開始時にリポジトリの指示を読み込みます。指示を保存した後、機能のコンテキストを失わないよう、フィルタリングの会話を終了して再開します。

1. フィルタリングの会話が開いている場合は `/exit` を入力して閉じます。Copilot CLI が会話を自動的に保存します。
2. `game-filters-cli` ブランチでリポジトリのルートから、保存した会話を再開します。

   ```bash
   copilot --resume
   ```

   選択肢が表示されたらフィルタリングの会話を選び、`game-filters-cli` ブランチがチェックアウトされていることを確認します。

3. Copilot に、更新した指示を適用するよう依頼します。

   ```plaintext
   We just updated our instructions and code guidance. Can you please update the code you generated to match that guidance?
   ```

4. `/diff` を入力し、変更された TypeScript ファイルを確認します。新しく追加された TSDoc コメントがコードを正確に説明していることを確認します。

## まとめと次のステップ

このプロジェクトで Copilot が指示を受け取る方法を確認し、新しい標準を機能に適用しました。次の作業を行いました。

- リポジトリの指示とパス別の指示ファイルがエージェントに伝わる方法を確認しました。
- 指示ファイルを更新して、コーディング標準に従うようにしました。
- 指示ファイルがコードに与える影響を確認しました。

次に、[再利用可能な `quality-checks` スキルをカスタマイズして実行します][next-lesson]。lint とテストを一貫して実行できるようにします。

## リソース

- [Copilot CLI にカスタム指示を追加する][instruction-files]
- [カスタム指示の対応状況][custom-instructions-support]
- [カスタム指示を作成するためのベストプラクティス][instructions-best-practices]
- [Awesome Copilot — 指示ファイルなどのリソース集][awesome-copilot]

[instruction-files]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions
[instructions-best-practices]: https://docs.github.com/copilot/concepts/prompting/response-customization#writing-effective-custom-instructions
[awesome-copilot]: https://awesome-copilot.github.com/
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support
[previous-lesson]: ../3-agent-modes/
[next-lesson]: ../5-agent-skills/
