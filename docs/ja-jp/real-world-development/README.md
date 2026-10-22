---
title: "実践的な開発"
slug: ja-jp/real-world-development
authors:
  - geektrainer
lastUpdated: 2026-09-16
---

現実的なシナリオ、完成したアプリケーション、共有バックログを使って、ソフトウェア開発ライフサイクル全体で GitHub Copilot を活用します。変更を段階的に計画してコードを作成し、動作をテストし、プルリクエストをレビューし、エージェントと作業を管理します。

## シナリオ

あなたは、開発者向けテーマのボードゲーム向けクラウドファンディングを提供する架空の企業 Tailspin Toys に新しく加わった開発者です。チームのバックログは GitHub Issue として登録済みで、すぐに作業を始められます。バックログには、フィルタリングやページネーションといった機能開発に加え、アクセシビリティやコーディング標準の改善も含まれています。

## 作業環境を選ぶ

GitHub Copilot は、どの環境でも利用できます。自分に合った環境を選んで開発しましょう。各ワークショップには独自のセットアップ手順があり、共通の Tailspin Toys シナリオを使用します。

### [GitHub Copilot CLI][cli]

ターミナルでエージェント型アシスタントを使います。Plan と Autopilot の各モード、指示、スキル、カスタムエージェント、Playwright MCP、Agent Merge、実践的なスラッシュコマンドを扱います。

### [GitHub Copilot app][app]

デスクトップアプリでエージェントセッションを並行して実行し、セッションモードを切り替え、キャンバスで共同作業し、GitHub Issue とプルリクエストを管理します。

### [GitHub Copilot cloud agent][cloud]

GitHub Issue を非同期エージェントに割り当て、作業を案内し、進行状況を確認して、エージェントが作成したプルリクエストをレビューします。

### [Visual Studio Code][vscode]

Visual Studio Code と GitHub Codespaces で GitHub Copilot を使います。エディターを離れずに Copilot Chat のエージェントモード、MCP サーバー、カスタムエージェントを利用します。

[cli]: cli/
[app]: app/
[cloud]: cloud/
[vscode]: vscode/
