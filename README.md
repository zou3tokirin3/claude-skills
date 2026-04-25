# claude-skills

My personal Claude Code skills, managed via [skills.sh](https://skills.sh/).

## Install

```bash
npx skills add zou3tokirin3/claude-skills
```

または特定のプロジェクトにインストールする場合：

```bash
cd your-project
npx skills add zou3tokirin3/claude-skills
```

インストール後、`skills-lock.json` が生成されます。これをコミットしておくと環境を再現できます。

```bash
git add skills-lock.json
git commit -m "add claude skills"
```

## Skills

### fossil-planner

Fossil を使うプロジェクトでチケット起票・受入条件定義を行う **Planner** ロール。

「チケット作って」「要件整理して」「起票して」などの発話で自動的に呼び出されます。  
チケットのACとブランチ名の定義まで行い、実装・レビューはしません。

### fossil-implementer

Fossil を使うプロジェクトで実装・コミットを行う **Implementer** ロール。

「実装して」「コードを書いて」などの発話で自動的に呼び出されます。  
チケットのACに従って実装し、`status: Review` まで進めます。起票・レビュー判断はしません。

### fossil-reviewer

Fossil を使うプロジェクトでACと実装の照合・trunkマージ・チケットクローズを行う **Reviewer** ロール。

「レビューして」「マージして」「ACと照合して」などの発話で自動的に呼び出されます。  
実装の修正はせず、指摘のみ行います。

> **fossil 三ロールについて**  
> planner → implementer → reviewer の順に使うことで、チケット駆動の開発フローを Claude Code 上で完結できます。

### zunda-agent

Claude Code の Stop フックとして動作し、タスク完了時に最後のアシスタントメッセージを **ずんだもん** スタイルで要約・読み上げるセットアップガイドスキル。

VOICEVOX（ずんだもん）が利用可能な場合は音声合成で読み上げ、利用できない場合は macOS の `say` にフォールバックします。

元リポジトリ: [zunda-agent](https://github.com/mokuhei-puka/zunda-agent) ※セットアップ方法を Claude Code から参照するためのスキルです。

## Update

```bash
npx skills add zou3tokirin3/claude-skills
```

再実行することで最新版に更新できます（`skills update` コマンド実装後はそちらを推奨）。
