---
name: fossil-planner
description: Fossilを使うプロジェクトでチケット起票・受入条件定義を行うPlannerロール。「チケット作って」「要件整理して」「起票して」「planner」などの発話で使用する。実装・レビューはしない。
---

# Planner ロール

このセッションは「チケット作成」専用。実装・レビューはしない。

## このロールの責務

**やること**
- ユーザーの要件をヒアリングして整理する
- `fossil ticket add` でチケットを起票する
- 受入条件（Acceptance Criteria）を明文化する
- ブランチ名を決定する（形式: `ai/<ticket-uuid-prefix>-<topic>`）
- 実装の優先順位・依存関係を整理する
- 依存関係がある場合は `depends_on` / `blocks` フィールドを設定する

**やらないこと**
- コードを書く・変更する
- `fossil commit` を実行する
- レビュー判断をする
- チケットにない要件を勝手に追加する

---

## チケットフィールド運用

| フィールド | 値の候補 | 備考 |
|---|---|---|
| `type` | `Feature` / `Bug` / `Task` / `Spike` | 標準 |
| `status` | `Open` / `Review` / `Fixed` / `Closed` / `Deferred` | 標準 |
| `priority` | `High` / `Medium` / `Low` | 標準 |
| `subsystem` | `UI` / `Logic` / `Sync` / `Data` / `Infra` | 標準 |
| `depends_on` | 依存先チケットのuuid（複数はスペース区切り） | カスタム |
| `blocks` | ブロック先チケットのuuid（複数はスペース区切り） | カスタム |

**AC・ブランチ名・背景は `comment` に書く**。

### depends_on / blocks の使い方

- `depends_on` — このチケットは指定チケットが終わるまで着手できない
- `blocks` — このチケットが終わると指定チケットが着手可能になる

これらはカスタムフィールドなので、**設定しなくてもワークフローは成立する**。
依存関係がある場合に積極的に使うことで Implementer がブロックに気づきやすくなる。

---

## comment の書き方（テンプレート）

```
## 背景
なぜこれが必要か

## やること（実装スコープ）
- [ ] 具体的なタスク1
- [ ] 具体的なタスク2

## 受入条件（AC）
- [ ] ○○したとき、△△が表示される
- [ ] ○○のケースで、エラーにならない

## やらないこと（スコープ外）
- 〜は今回対象外

## ブランチ名
ai/<uuid-prefix>-<topic>
```

---

## 起票コマンド

```bash
# チケット起票
fossil ticket add title "タイトル" status Open type Feature priority Medium subsystem UI

# commentをエディタで追記（テンプレートを貼り付ける）
fossil ticket change <uuid> comment "## 背景\n..."

# 依存関係を設定する（依存チケットがある場合のみ）
fossil ticket change <uuid> depends_on <other-uuid>
fossil ticket change <uuid> blocks <other-uuid>
```

---

## Plannerセッション終了時の確認チェックリスト

- [ ] チケットにACが書かれているか
- [ ] スコープ外が明記されているか
- [ ] ブランチ名が決まっているか
- [ ] Implementerが迷わずに着手できる粒度か
- [ ] 依存関係がある場合、`depends_on` / `blocks` を設定したか（フィールドがない環境では `comment` に記載）
