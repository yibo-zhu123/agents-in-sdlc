---
title: "レッスン 10 - 振り返りと次のステップ"
description: "Copilot CLI のワークフロー、2 つのプルリクエスト、再利用可能なカスタマイズ、参考リソースを振り返ります。"
authors:
  - geektrainer
lastUpdated: 2026-09-29
---

GitHub Copilot CLI を使い、Tailspin Toys の一連のワークフローに取り組みました。次の作業を行いました。

- Codespace を準備し、Copilot CLI をインストールしてプロジェクトを調査し、登録済みのフィルタリング Issue を確認しました。
- 星評価を追加し、転送されたブラウザーで結果を確認して、最初のプルリクエスト (PR) を手動でマージしました。
- フィルタリングの Issue を起点に、Plan モードで進め方を決め、Autopilot モードで構築し、Interactive モードでレビューしました。
- カスタム指示でエージェントを案内し、既存の `quality-checks` スキルをカスタマイズして、ユニットテスト、lint、型チェックを実行しました。
- Playwright Model Context Protocol (MCP) サーバーを追加し、実際のブラウザーでフィルタリング機能を確認しました。
- 品質保証 (QA) のカスタムエージェントを作成して選択し、要件、テストカバレッジ、スキルの結果、ブラウザーでの証拠を評価しました。
- フィルタリングの変更全体をレビューし、Agent Merge がフィルタリングの PR をマージすることを承認しました。
- コンテキスト、モデル、共有、cloud agent への任意の委任に使うスラッシュコマンドを確認しました。

## リリースした内容

ワークショップには 2 つの PR のマイルストーンがあります。それぞれ更新済みの `main` からブランチを作成しました。

1. **星評価:** 既存の `starRating` と、評価がない状態をゲームカードに明示します。
2. **フィルタリングと品質ワークフロー:** フィルタリング機能を実装し、指示を更新して機能に適用し、`quality-checks` のレポートをカスタマイズして、QA プロファイルと関連テストを追加します。

フィルタリングの計画から PR の作成まで、同じ会話とブランチを使いました。ワークショップを効率化するため、その作業を 1 つの PR にまとめています。

## 検証方法の違い

自動テスト、自分でのブラウザー確認、MCP を使った Copilot のブラウザー操作など、複数の方法でコードを確認しました。`quality-checks` スキルでは、ユニットテスト、lint、型チェックを実行し、新しい形式で結果を報告しました。QA では、その結果に要件とテストカバレッジのレビューを加えて、PR 作成前に全体を確認しました。

テストは、実際に不足している箇所を補う必要があります。QA を実行しても新しいテストが不要な場合は、問題ありません。マージを承認する前に、コードと証拠を確認し、変更後は影響を受ける証拠を更新してください。

## ベストプラクティス

Copilot に与えるコンテキストとツールは、その作業に影響します。このワークショップでは、指示を更新し、スキルをカスタマイズし、QA プロファイルを作成し、MCP サーバーを構成しました。会話を変えてもカスタマイズを再利用し、チームの必要に合わせて調整できます。指示は標準を定め、スキルは繰り返し実行するタスクを説明し、カスタムエージェントは専門家の役割を定義し、MCP サーバーは外部ツールを接続します。エージェントの要約だけでなく、実際の変更やツールの結果を確認してください。

タスクに合った**モードとモデル**を選びます。構築前に方法を考えるには **Plan**、対象を絞った変更を一緒に進めるには **Interactive**、範囲が明確なタスクには **Autopilot** を使います。定型的な編集には速いモデル、複雑な作業には高性能なモデルを選びます。

インフラストラクチャと同様に、コンテキストも重要です。*何を*、*なぜ*、*どのように*構築したいかを明確に伝えると、結果が大きく変わります。

## さらに試す

中心的なワークフローを学びました。Copilot CLI の機能をさらに試してみましょう。

- `/review` でコードレビューエージェントに変更を分析させます。
- `/rubber-duck` で問題について対話し、別の視点を得ます。
- `/fleet` で独立したサブタスクを並列に調整します。
- `/worktree` で別のタスクを分離します。
- `/delegate` でタスクを Copilot cloud agent に送信します。

## 次のステップ

ツールを上達させるには、使い続けるのが一番です。本番コード、趣味のコード、何年も前から作りたいと思っていた小さなアプリなどに活用してみましょう。学んだことをチームと共有し、チームのメンバーからも学びます。いつものように、ドキュメントも確認してください。

ターミナルから Tailspin Toys を拡張するには、任意の [Foundry Backer Concierge シリーズ][foundry]に進みます。ほかの環境と比べるには、[VS Code ワークショップ][vscode]、[GitHub Copilot app ワークショップ][app]、[Copilot cloud agent ワークショップ][cloud]を確認してください。

## リソース

- [GitHub Copilot CLI について][about-cli]
- [Copilot CLI コマンドリファレンス][cli-reference]
- [Copilot CLI をカスタマイズする][customize-cli]
- [Copilot CLI でプルリクエストを管理する][manage-prs]

[previous-lesson]: ../9-cli-power-tools/
[foundry]: ../8-foundry-agent/
[vscode]: ../../vscode/
[app]: ../../app/
[cloud]: ../../cloud/
[about-cli]: https://docs.github.com/copilot/concepts/agents/about-copilot-cli
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[customize-cli]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot
[manage-prs]: https://docs.github.com/copilot/how-tos/copilot-cli/use-copilot-cli/manage-pull-requests
