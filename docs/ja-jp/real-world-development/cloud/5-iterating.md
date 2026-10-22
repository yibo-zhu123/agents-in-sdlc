---
title: "演習 5: GitHub Copilot の作業を反復して改善する"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 前のレッスン: エージェントの管理][previous-lesson] |
|:--|

## 作業をレビューする

このラボでは、ユーザー体験の改善や機能追加を目的に、GitHub Copilot と複数のタスクに取り組みました。コードへのドキュメント追加、デザインチームが改善できる関連ゲーム機能の構築、ハイコントラストモードとライトモードを含むアクセシビリティ機能の実装を依頼しました。コード変更を確認し、必要に応じて Copilot にフィードバックを返して作業を改善しましょう。その後、チームの残りのメンバーにもレビューを依頼します。

### シナリオ

これまで何度も確認したように、生成 AI を追加しても、ソフトウェア設計や DevOps の基本は変わりません。生成されたコードをレビューし、通常の DevOps プロセスに従います。チーム全体でレビューを有効にする前に、ドキュメント、関連ゲーム機能、アクセシビリティ機能について GitHub Copilot の提案を確認しましょう。

## GitHub Copilot cloud agent のセキュリティ

Copilot cloud agent は非同期で、監視なしにタスクを実行するため、安全を保つための制約があります。

- Copilot はリポジトリへの読み取りアクセス権を持ち、コードの作業に使うブランチに限って書き込みアクセス権を持ちます。
- Cloud agent は GitHub Actions 内で動作し、別の一時的な環境を作成して作業します。
- GitHub Actions ワークフローを実行するには、人間の承認が必要です。
- [外部リソースへのアクセスは既定で制限されています](https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall)。MCP サーバーも対象です。

## 生成されたドキュメントをレビューする

まず、GitHub Copilot cloud agent が最初に作成したプルリクエスト (PR)、つまりコードにドキュメントを追加する PR を確認します。GitHub.com の標準的な PR 画面で作業します。

> [!NOTE]
> PR の確認中に GitHub Copilot がファイアウォールでブロックされたという警告が表示されることがあります。既定では Copilot の外部リソースへのアクセスが制限されているため、これは想定どおりです。必要であれば、[Copilot cloud agent のファイアウォールをカスタマイズまたは無効化][agent-firewall]できます。

1. github.com のリポジトリに戻ります。
2. **Pull Requests** を選択して PR の一覧を開きます。
3. **Add missing documentation** などのタイトルが付いた PR を開きます。

> [!NOTE]
> Copilot がまだ作業中の場合、プルリクエストには **[WIP]** の表示があります。その場合は Copilot が完了するまで待ちます。数分かかることがあるため、休憩したり、学んだ内容を振り返ったりしてもよいでしょう。

4. 準備ができたら **Files changed** タブを選択して変更を確認します。

    ![Files changed タブ](../../../_images/shared-pr-files-changed.png)

5. 新たに更新されたコードを確認します。TSDoc ドキュメントコメントなどが追加されていますが、具体的な変更は異なる場合があります。

    変更を確認するときは、TSDoc コメント、TypeScript の規則、コメントヘッダーに注目してください。これらは演習 2 の最初に確認したカスタム指示ファイルに基づいています。

6. 問題がなければ **Conversation** タブに戻り、下にスクロールします。
7. ワークフローの承認待ちを示す表示を確認します。
8. 承認待ちの場合は **Approve and run workflows** を選択します。

   ![ワークフローを承認して実行する](../../../_images/shared-approve-workflows.png)

9. PR のチェック欄にワークフローが登録されたことを確認します。すべて順調に進めば、単一 Astro アプリのプロジェクトチェックが成功します。数分かかる場合があります。

## GitHub Copilot に変更を依頼する

Copilot と PR で作業するときは、コメントで **@copilot** に言及できます。チームメンバーに依頼するのと同じように、プルリクエストやコードへのインラインコメントに追加すると、Copilot がコメントを確認して、対応する新しいセッションを開始します。結果は確率的に変化するため、必ずこう書けばよいという決まった文章はありません。たとえば、次の更新を依頼できます。

- 各コードファイルの先頭に、ファイルの役割を説明するコメントヘッダーを追加します。
- TypeScript と Astro ファイルに TSDoc ドキュメントコメントを追加します。
- Astro アプリの構成を説明する README を作成します。

1. 上記の案や別の提案を使って、生成されたドキュメントに変更を依頼するコメントを追加し、ほかのユーザーと同様に **@copilot** を指定します。
2. **View Session** を選択して Copilot の作業を確認します。Copilot が更新用の新しいセッションを始める様子を確認できます。
3. **Back to pull request** を選択してプルリクエストに戻ります。

    ![プルリクエストに戻る](../../../_images/ex7-back-to-pr.png)

4. Copilot が変更を完了すると、プルリクエストに新しいコミットが追加されています。
5. **Files changed** タブを選択して変更を確認します。

満足できるまで反復して改善してください。完了したらドラフト PR をレビュー可能な状態にして、`main` ブランチにマージできます。

![PR をレビュー可能な状態にする](../../../_images/ex7-ready-for-review.png)

## 関連ゲーム機能をレビューする

ゲーム詳細ページに関連ゲームを表示する Issue に対して Copilot が作成した PR を確認します。

1. GitHub.com のリポジトリに戻ります。
2. **Pull Requests** タブを選択します。
3. **Show related games on the game details page** などのタイトルが付いた PR を選択します。
4. **Files changed** タブを選択して、生成されたコードを確認します。
5. 問題がなければ **Conversation** タブに戻り、下にスクロールします。
6. ワークフローの承認待ちを示す表示を確認します。
7. 承認待ちの場合は **Approve and run workflows** を選択します。

   ![ワークフローを承認して実行する](../../../_images/shared-approve-workflows.png)

8. PR のチェック欄にワークフローが登録されたことを確認します。すべて順調に進めば、単一 Astro アプリのプロジェクトチェックが成功します。数分かかる場合があります。
9. **任意:** Codespace でこのブランチに切り替え、関連ゲーム機能を手動でテストできます。Codespace に移動してターミナルを開き、Copilot が作成したブランチ名 (例: **copilot/fix-8**) に `<branch-name>` を置き換えて、次のコマンドを実行します。

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

Copilot が関連ゲーム機能を作成しました。前と同じように、Copilot cloud agent に更新を依頼して改善できます。たとえば表示する関連ゲームの数の調整や、カスタム指示の更新前に割り当てた作業に対するコメントヘッダーと TSDoc ドキュメントコメントの追加を依頼できます。**Conversation** タブにコメントを追加すると、Copilot が確認して新しいセッションを開始します。

## アクセシビリティ機能をレビューする

最後に、アクセシビリティのカスタムエージェントで実装した機能を確認します。この PR には、演習 3 で依頼したハイコントラストモードと、演習 4 で mission control から追加を依頼したライトモードの両方が含まれているはずです。

1. GitHub.com のリポジトリに戻ります。
2. **Pull Requests** タブを選択します。
3. **Add high contrast mode to website** などのタイトルが付いた PR を選択します。

> [!NOTE]
> Copilot がまだ作業中の場合、プルリクエストには **[WIP]** の表示があります。数分かかることがあるため、Copilot が完了するまで待ちます。

4. **Files changed** タブを選択して、生成されたコードを確認します。
5. 実装をレビューするときは、次の点に注目します。
   - モードを切り替える UI コンポーネント
   - ユーザーの設定を保存する local storage の利用
   - ハイコントラストとライトモードの CSS またはスタイルの変更
   - アクセシビリティ属性 (ARIA ラベル、キーボード操作など)
   - モード切り替えを管理する JavaScript/TypeScript コード

6. 問題がなければ **Conversation** タブに戻り、下にスクロールします。
7. ワークフローの承認待ちを示す表示を確認します。
8. 承認待ちの場合は **Approve and run workflows** を選択します。

   ![ワークフローを承認して実行する](../../../_images/shared-approve-workflows.png)

9. PR のチェック欄にワークフローが登録されたことを確認します。すべて順調に進めば、単一 Astro アプリのプロジェクトチェックが成功します。数分かかる場合があります。
10. **任意:** Codespace でこのブランチに切り替え、アクセシビリティ機能を手動でテストできます。Codespace に移動してターミナルを開き、Copilot が作成したブランチ名に `<branch-name>` を置き換えて、次のコマンドを実行します。

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

    次にアプリケーションを起動して、ブラウザーでハイコントラストモードとライトモードをテストします。ページを再読み込みしても設定が保持されることを確認してください。

アクセシビリティのカスタムエージェントが、アクセシビリティのベストプラクティスに沿った実装を Copilot に案内したことを確認しました。アクセシビリティ上の懸念や改善点があれば、前の PR と同様にコメントで **@copilot** に変更を依頼できます。

## 任意の演習 — さらに作業を委任する

Cloud agent には、バックログの実際のタスクを任せ、別の作業に集中できるという利点があります。いくつかの Issue をリポジトリに追加して Copilot に割り当て、この方法に慣れていきましょう。次のようなアイデアがあります。

- ゲーム詳細ページに支援者向けの関心フォームを作成します。
- ゲーム一覧ページにページネーションを実装します。
- データアクセスヘルパーに入力検証とエラー処理を追加します。

## まとめ

Cloud agent ハーネスを完了しました。このレッスンを通して、次の作業を行いました。

- **このリポジトリに含まれるカスタム指示ファイルを確認し**、後で cloud agent の出力に与える影響を確認しました。
- **Issue を Copilot cloud agent に割り当て**、環境の準備、計画、非同期の作業を見ました。
- **カスタムエージェントを作成して使用し**、ハイコントラストとライトモードを追加しました。
- **Copilot agents ページを mission control として使用し**、アクセシビリティセッションを監視して途中で指示を送りました。
- **cloud agent のプルリクエストをレビューして改善し**、`@copilot` に変更を依頼してワークフローを承認しました。

## 振り返りと次のステップ

Cloud agent ハーネスはこれで完了です。これまでの内容を別の環境で試したい場合は、次のハーネスを確認してください。

- 🖥️ **[VS Code ハーネス](../../vscode/)** — IDE で直接 Copilot Chat のエージェントモードと MCP 統合を試します。
- 💻 **[CLI ハーネス](../../cli/)** — Plan と Autopilot モード、agent skill、カスタム QA エージェント、Playwright MCP、Agent Merge を使い、ターミナルから 2 つのレビュー済み変更をリリースします。cloud agent に戻るための `/delegate` ブリッジも任意で試せます。

自分のリポジトリでは、次のアイデアを試してください。

- リファクタリングまたはテストカバレッジに関する Issue を cloud agent に割り当てます。
- ほかの分野に対応するカスタムエージェントを作成します。
- 別の技術スタック向けに `copilot-setup-steps.yml` を設定します。

## リソース

- [GitHub Copilot][github-copilot]
- [Copilot agents について][copilot-agents]
- [GitHub Issue を Copilot に割り当てる][assign-issue]
- [Copilot cloud agent のセットアップワークフローのベストプラクティス][cloud-agent-best-practices]
- [Copilot cloud agent のファイアウォールを設定する][agent-firewall]

---

| [← 前のレッスン: エージェントの管理][previous-lesson] |
|:--|

[github-copilot]: https://github.com/features/copilot
[cloud-agent-overview]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[agent-firewall]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall

[previous-lesson]: ../4-managing-agents/
