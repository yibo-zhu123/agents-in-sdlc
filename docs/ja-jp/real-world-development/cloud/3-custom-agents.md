---
title: "演習 3 - カスタムエージェント"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

## カスタムエージェントとは

GitHub Copilot の[カスタムエージェント][custom-agents-concept]を使うと、開発ワークフロー内の特定のタスクや分野に合わせた AI アシスタントを作成できます。リポジトリの `.github/agents` フォルダーに Markdown ファイルを作成し、Copilot が特定の作業を効果的に行えるように、集中的な指示、ベストプラクティス、コーディングパターン、分野固有の知識を与えます。チームの専門知識を再利用可能なエージェントにできます。たとえば、[WCAG][wcag] 準拠を確認するアクセシビリティエージェント、安全なコーディングを確認するセキュリティエージェント、一貫したテストパターンを維持するテストエージェントなどです。

カスタムエージェントは、プロジェクトの `.github/agents` フォルダー、またはグローバルな `~/.copilot/agents` に置く Markdown ファイルで定義します。各ファイルには、少なくとも `name` と `description` を含む YAML フロントマターと、エージェントの動作、専門知識、指示を定義する Markdown プロンプトが必要です。

### カスタムエージェントと agent skill の違い

カスタムエージェントと [agent skill][agent-skills-concept] には共通点があります。どちらも主に Markdown ファイルで定義され、AI に作業方法を伝えます。違いを簡潔に表すと、**カスタムエージェントは作業者、スキルはツール**です。

カスタムエージェントは独自のコンテキストウィンドウを持ち、スキルやほかのエージェントを調整できます。このラボでは、アクセシビリティのカスタムエージェントがガイドラインに沿ってサイトをレビューし、更新します。その際に、プルリクエストのワークフローやテストの実行・管理などのスキルを呼び出すことができます。

> [!NOTE]
> カスタムエージェントの作成方法に決まった正解はありません。AI のほかの使い方と同様に、自分の環境やシナリオに合わせて試し、改善してください。

[custom-agents-concept]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
[agent-skills-concept]: https://docs.github.com/copilot/concepts/agents/about-agent-skills
[wcag]: https://www.w3.org/WAI/standards-guidelines/wcag/

この演習では、次のことを学びます。

- カスタムエージェントの定義方法
- カスタムエージェントへのタスクの割り当て

## シナリオ

Tailspin Toys は、視覚的な能力や好みに関係なく、すべてのユーザーがクラウドファンディングプラットフォームを利用できるように取り組んでいます。最近、現在のダークテーマはテキストと背景のコントラストが十分でなく、読みにくいというフィードバックがありました。このアクセシビリティ上の課題に対応するため、デザインチームはユーザーが切り替えられるハイコントラストモードを求めています。

アクセシビリティは重要なので、できるだけ早く実装します。機能の生成にカスタムエージェントを利用しましょう。

## アクセシビリティのカスタムエージェントを確認する

アクセシビリティ用のカスタムエージェントはすでに用意されています。どのような指針で Copilot を案内するか確認します。

codespace に戻り、アクセシビリティのカスタムエージェントファイルを確認します。

1. `.github/agents/accessibility.md` を開きます。
2. `name` と `description` を含む YAML フロントマターを確認します。

> [!CAUTION]
> カスタムエージェントには `name` と `description` を含むフロントマターが必要です。

3. 続くセクションを確認します。次の内容が含まれています。
   - アクセシブルな Web サイトのコードを生成するときの主な責任
   - アクセシビリティのベストプラクティス
   - HTML、CSS、JavaScript のコード例
   - よくある落とし穴や誤り

## Issue を作成して割り当てる

Mission control は、環境内のエージェントを操作する中心的な場所です。Copilot cloud agent にタスクを割り当て、進行状況を監視し、追加の指示を送れます。まず、ハイコントラストモードの作成を Copilot に依頼します。

1. リポジトリに移動します。
2. **Issues** タブを選択します。
3. **New issue** を選択して Issue の作成画面を開きます。
4. **Blank issue** を選択します。
5. **Title** に `Add high contrast mode to website` と入力します。
6. **Description** に次の内容を入力します。

    ```plaintext
    We need a high contrast mode for the site. There should be a toggle for high contrast which the user can set. It should store the setting in local storage on the browser.
    ```

7. **Create** を選択して Issue を作成します。
8. 右側の **Assign to Copilot** を選択して割り当て画面を開きます。
9. 一覧から **Accessibility agent** を選択します。

    ![カスタムエージェントとアクセシビリティエージェントが強調表示された cloud agent 割り当て画面](../../../_images/ex5-select-custom-agent.png)

10. **Assign** を選択します。
11. Copilot がバックグラウンドで作業を始めます。

## まとめと次のステップ

[GitHub Copilot のカスタムエージェント][custom-agents]は、特定のタスクや分野に対応する専門の AI アシスタントです。カスタムエージェントを使うと、チームの専門知識や標準を再利用可能な形にして、Copilot が特定の種類の作業を効果的に進められるようにできます。

この演習では、次のことを学びました。

- カスタムエージェントの定義方法
- カスタムエージェントへのタスクの割り当て

Copilot がハイコントラストモードを実装している間に、[mission control からエージェントセッションを監視して方向付ける][next-lesson]方法を確認します。

## リソース

- [カスタムエージェントについて][custom-agents]
- [組織でカスタムエージェントを利用するための準備][org-custom-agents]
- [エンタープライズでカスタムエージェントを利用するための準備][enterprise-custom-agents]

---

| [← 前のレッスン: GitHub Copilot cloud agent][previous-lesson] | [次のレッスン: エージェントの監視と管理 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../2-cloud-agent/
[next-lesson]: ../4-managing-agents/
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
[org-custom-agents]: https://docs.github.com/copilot/how-tos/administer-copilot/manage-for-organization/prepare-for-custom-agents
[enterprise-custom-agents]: https://docs.github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/prepare-for-custom-agents
