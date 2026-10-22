---
title: "演習 1 - カスタム指示 (VS Code)"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: 前提条件][previous-lesson] · [次のレッスン: エージェントモード →][next-lesson]

生成 AI を使ううえで、コンテキストは重要です。特定の方法でタスクを進めたい場合や、Copilot に知っておいてほしい背景情報がある場合は、その情報を参照できるようにします。Copilot に固有のコンテキストを共有する方法はいくつかあります。特に重要なのは、コード生成の指針を伝える[指示ファイル][instruction-files]です。

この演習では、次の作業を行います。

- リポジトリのカスタム指示とパス別の指示ファイルを使って、プロジェクト固有のコンテキスト、コーディングガイドライン、ドキュメント標準が Copilot に伝わる方法を確認します。
- 現在の指示を使って、フィルタリング機能のデータ層の最初の要素 (パブリッシャーヘルパー) を作成します。
- `.github/copilot-instructions.md` にリポジトリ全体に適用する新しい標準を追加します。
- 同じプロンプトを再送し、生成コードに新しい標準が反映される様子を確認します。
- 次の演習で Copilot が更新済みの指示を利用できるよう、指示の更新とフィルタリングの最初の要素を `main` にコミットします。

> [!CAUTION]
> 生成されたコードが設定した標準と異なる場合があります。Copilot は確率的に動作します。この演習では、出力が文字単位で一致することではなく、指示を更新した後に動作がどう変わるかを確認します。

## 指示ファイル

### シナリオ

Tailspin Toys には、開発方法に関するガイドラインと要件があります。

- データ層には必ずユニットテストを追加します。
- UI はダークモードにし、モダンな印象にします。
- コードには TSDoc のドキュメントコメントを追加します。
- 各ファイルの先頭に、そのファイルの役割を説明するコメントブロックを追加します。

指示ファイルを使って、Copilot がこれらの開発慣行に沿って作業できるようにします。

### カスタム指示

カスタム指示を使うと、コーディングスタイルや要件を Copilot に伝え、より適切な提案やコードスニペットを得られます。好みのコーディング規則、ライブラリ、コメントの書き方などを指定できます。リポジトリ全体に適用する指示も、特定のファイルやタスクにだけ適用する指示も作成できます。

指示ファイルには次の 2 種類があります。

- `.github/copilot-instructions.md` は、リポジトリへのすべてのリクエストで Copilot に送られる単一の指示ファイルです。技術スタック、作成するものの概要、ベストプラクティスなど、ほとんどのチャットや CLI リクエストに関係するプロジェクト全体の情報を記載します。
- `.github/instructions/*.instructions.md` は、特定のタスクやファイル形式向けに作成します。たとえば TypeScript や Astro、UI コンポーネントの作成、単体テストの追加などに対応する指示を作成できます。

> [!NOTE]
> IDE では、指示ファイルは Copilot Chat でのコード生成にのみ使われ、コード補完や次の編集候補には使われません。
>
> Copilot Chat、Copilot CLI、Copilot cloud agent は、リポジトリレベルの指示と、`applyTo` フロントマターを含む `*.instructions.md` ファイルを使います。
>
> Copilot は AGENTS.md や CLAUDE.md など、ほかの規格の指示ファイルにも対応しています。[カスタム指示の対応状況][custom-instructions-support]を参照してください。

### 指示ファイルを管理するためのベストプラクティス

指示ファイルの作成方法を詳しく説明するのはこの演習の範囲外ですが、サンプルプロジェクトのファイルは一般的な方法を示しています。

- `copilot-instructions.md` には、作成するもの、プロジェクトの構成、全体に適用するコーディング標準など、プロジェクト全体に関する指針を記載します。
- `*.instructions.md` には、ユニットテスト、Astro コンポーネント、データ層など、特定のファイル形式やタスクの指示を記載します。
- 自然な言葉で明確に書き、コードの良い例と避けるべき例を示します。

AI の使い方に決まった正解はありません。プロジェクトに合わせて試しながら、自分に合った方法を見つけてください。

> [!TIP]
> GitHub Copilot を使うプロジェクトには、十分な指示ファイルを用意しましょう。このプロジェクトには [UI の更新][ui-instructions]や [Astro][astro-instructions] など、多くのタスク向けのファイルがあります。
>
> Copilot に指示ファイルの作成を依頼することもできます。各環境で手順は異なります。たとえば VS Code では **Configure Chat → Generate Agent Instructions**、Copilot CLI では `/init` を使います。演習では該当する操作を説明します。
>
> テンプレートや出発点を探すには、多数の指示ファイル、カスタムエージェント、リソースを集めた [awesome-copilot][awesome-copilot] を参照してください。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## このプロジェクトのカスタム指示ファイルを確認する

スタータープロジェクトには、中心となる `copilot-instructions.md` と、さまざまなタスク用の `*.instructions.md` ファイルがあります。変更を加える前に確認して、効果を比べられるようにします。

1. `.github/copilot-instructions.md` を開きます。
2. プロジェクトの簡単な説明、**Agent notes**、**Code standards**、**Scripts**、**Repository Structure** などのセクションを確認します。**Code standards** 内の **GitHub Actions Workflows** に関するガイダンスも、Copilot とのすべてのやりとりに適用されます。
3. `.github/instructions` フォルダーを開き、Astro ファイル、Drizzle データ層、テストなどの指示を確認します。
4. `.github/instructions/unit-tests.instructions.md` を開きます。先頭の `applyTo` フィールドは、指示が適用されるファイルの glob を指定します。この例では `**/*.test.ts` に一致する TypeScript テストファイルが対象です。
5. プロジェクトのユニットテストを作成するときの指示を確認します。
6. 最後に `.github/instructions/drizzle.instructions.md` の末尾を確認します。`unit-tests.instructions.md` などの指示ファイルや、プロジェクトの既存ファイルへのリンクがあります。パスはリポジトリのルートではなく、指示ファイルからの相対パスです。

> [!NOTE]
> `copilot-instructions.md` の **Code formatting requirements** セクションにはプロジェクトのコーディング標準が記載されていますが、コード内ドキュメントの要件はまだありません。次に、TSDoc コメントとファイル先頭のコメントを追加します。

## 変更用ブランチを作成する

ベストプラクティスに沿って、変更用のブランチを作成します。

1. 前の演習で使用した Codespace に戻ります。
2. <kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押して新しいターミナルを開きます。
3. 新しいブランチを作成して切り替えます。

   ```bash
   git checkout -b custom-instructions
   ```

## 指示を更新する前に Copilot Chat を使う

> [!TIP]
> **Copilot Chat を開く**
>
> 次の演習を始める前に Codespace に戻り、Copilot Chat パネルを開いて **New Chat** を選択し、新しい会話を開始します。モードとモデルの選択は演習ごとに異なります。必要な場合は手順内で説明します。

カスタム指示の効果を確認するため、まず既存の指示ファイルのままプロンプトを送信します。後で指示を更新し、同じプロンプトを再送して違いを確認します。

1. 前の演習で開いたエディターのタブを閉じ、Copilot に必要なコンテキストだけを渡します。
2. `src/lib/publishers.ts` を開き、ヘルパーの配置先を Copilot に伝えます。
3. Chat ビューのエージェント一覧から **Agent** を選択し、Copilot がファイルを変更できるようにします。

   ![Chat ビューの agent picker を示すスクリーンショット](../../../_images/shared-chat-mode-selector.png)

4. 次のプロンプトを送信します。

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

5. Copilot がプロジェクトを調べてコードを更新します。ヘルパーファイルとテストに変更が及ぶこともあります。
6. 提案されたヘルパーが、型付けされた関数として実装されていることを確認します。最初の引数に `db` クライアントを取り、型付けされたパブリッシャーの配列を返します。これは `src/lib/*.ts` に適用される `.github/instructions/drizzle.instructions.md` のデータ層の規則に基づいています。
7. 提案されたコードに TSDoc ドキュメントコメントとファイルレベルのコメントヘッダーが**ない**ことを確認します。

> [!CAUTION]
> Copilot は確率的に動作するため、指示がなくてもドキュメントコメントを追加することがあります。その場合も問題ありません。指示を更新した後に動作の一貫性が改善することを確認するのが、この演習の目的です。

## リポジトリ標準を追加する

`.github/copilot-instructions.md` は、Copilot にプロジェクト全体の情報を伝えます。コードの提案が改善されるよう、コーディング標準にドキュメントの要件を記載します。

1. `.github/copilot-instructions.md` を再度開きます。
2. **Code formatting requirements** セクションを探します (27 行目付近)。ここにはコーディング標準が記載されていますが、コード内ドキュメントの規則がありません。そのため、生成されたヘルパーにはドキュメントコメントがありませんでした。
3. 既存の標準の直下に次の Markdown を追加します。

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. `copilot-instructions.md` を保存します。

> [!TIP]
> 前のレッスンで確認したように、`.github/copilot-instructions.md` はリポジトリ全体の指示に、`*.instructions.md` は言語、ファイル形式、タスク固有の指示に使います。ドキュメントコメントの規則のようなプロジェクト全体の標準には、リポジトリレベルのファイルが適しています。

## プロンプトを再送して変化を確認する

1. Copilot Chat に戻り、**New Chat** を選択して会話を新しくします。
2. `src/lib/publishers.ts` を選択して、Copilot が対象ファイルに集中できるようにします。
3. 前と**同じプロンプト**を送信します。

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

4. 提案されたファイルの先頭に、次のようなコメントブロックが追加されたことを確認します。

   ```typescript
   /**
    * Publisher data-access helpers for the Tailspin Toys Crowd Funding platform.
    * Provides functions to retrieve publisher information from the database.
    */
   ```

5. 関数に次のような TSDoc コメントが追加されたことを確認します。

   ```typescript
   /**
    * Returns a list of all publishers with their id and name.
    *
    * @param db - The Drizzle database client.
    * @returns A promise that resolves to an array of publisher objects.
    */
   ```

Copilot に新しいプロジェクト標準を伝え、次の演習で使う実際のコードに適用できました。

## 指示をコミットしてブランチをプッシュする

指示ファイルもほかの成果物と同じように、ソース管理で扱います。変更をリポジトリにコミットします。

1. <kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押して新しいターミナルウィンドウを開きます。
2. ターミナルで `git status` を実行し、指示の更新とヘルパーの変更があることを確認します。

   ```bash
   git status
   ```

3. 指示の更新とヘルパーの基盤を作成するために Copilot が変更したすべてのファイルをステージしてコミットします。

   ```bash
   git add .github/copilot-instructions.md src/lib/publishers.ts
   # If git status shows additional supporting updates (for example tests), add those files too.
   git commit -m "Add doc comment standards and publishers helper foundation"
   ```

4. ブランチをリポジトリにプッシュします。

   ```bash
   git push -u origin custom-instructions
   ```

## プルリクエストを作成してマージする

ブランチをプッシュしたら、指示ファイルの標準に関連する Issue とひも付けてプルリクエストを作成します。手動で行う代わりに、GitHub のツールに接続済みの Copilot に Issue を探して PR を作成し、マージしてもらいます。

> [!NOTE]
> Copilot に GitHub 操作を依頼するのはこの演習が初めてです。プロジェクトテンプレートには GitHub のツールが事前に構成されているため、セットアップは不要です。Copilot が初めてツールを使うときに、VS Code から GitHub へのサインインを求められることがあります。表示される手順に従ってください。この接続がどのように機能するかは後の演習で学びます。

1. Codespace で Copilot Chat を開きます。
2. <kbd>Control</kbd>+<kbd>Command</kbd>+<kbd>I</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>I</kbd> (Windows/Linux) を押して Copilot Chat ビューを開き、agent picker で **Agent** が選択されていることを確認します。
3. 指示ファイルの更新に関係する Issue を探し、現在のブランチから PR を作成して、指示とパブリッシャーヘルパーの基盤の両方を説明するよう Copilot に依頼します。

   ```
   Find the issue related to updating the instructions file. Create a new PR from the current branch, highlight that the PR closes that issue, and include that we also added the publishers helper foundation for the upcoming filtering work.
   ```

4. Copilot が Issue を探して PR を作成します。
5. Copilot が GitHub 操作を実行するための **Allow** を求めたら、コマンドを確認して適切に許可します。
6. PR が作成されたら、次のプロンプトを使って PR をマージし、ブランチを `main` に戻すよう依頼します。

   ```
   Merge the PR into main. Then return to main locally, and pull the latest code so we are up to date.
   ```

7. Copilot が GitHub 操作とシェルコマンドを実行するための **Allow** を求めたら、内容を確認して適切に許可します。

これで GitHub Copilot を使ってプルリクエストを作成し、マージできました。

## まとめと次のステップ

このプロジェクトで Copilot が指示を受け取る方法を確認し、VS Code の Copilot Chat で次の作業を行いました。

- 既存の指示のままコード生成プロンプトを送り、Copilot の出力を確認しました。
- `.github/copilot-instructions.md` にリポジトリ全体の標準を追加しました。
- 同じプロンプトを再送し、提案されたコードが新しい標準を採用することを確認しました。
- 次の演習で使えるよう、指示とヘルパーの基盤を `main` にコミットしました。

次は[エージェントモード][next-lesson]で指示を活用し、コードベース全体にまたがる機能を追加します。

## リソース

- [GitHub Copilot のカスタマイズ用指示ファイル][instruction-files]
- [カスタム指示を作成するためのベストプラクティス][instructions-best-practices]
- [Copilot のカスタム指示を書くための 5 つのヒント][copilot-instructions-five-tips]
- [GitHub Copilot の個人用カスタム指示][personal-instructions]
- [Awesome Copilot — 指示ファイルなどのリソース集][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-agent-mode/
[instruction-files]: https://code.visualstudio.com/docs/copilot/copilot-customization
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[personal-instructions]: https://docs.github.com/copilot/customizing-copilot/adding-personal-custom-instructions-for-github-copilot
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
