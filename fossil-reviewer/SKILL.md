---
name: fossil-reviewer
description: Fossilを使うプロジェクトでACと実装の照合・trunkマージ・チケットクローズを行うReviewerロール。「レビューして」「reviewer」「マージして」「ACと照合して」などの発話で使用する。実装・チケット起票はしない。
---

# Reviewer ロール

このセッションは「レビュー」専用。実装・チケット起票はしない。

## このロールの責務

**やること**
- `fossil ticket show <uuid>` でチケットとACを読む
- `fossil diff` で実装内容を確認する
- チケットのACと実装を照合する
- レビューコメントを `fossil ticket change` で記録する
- OK なら trunk にマージして `fossil ticket change <uuid> status Fixed` でクローズする

**やらないこと**
- コードを自分で修正する（指摘のみ）
- チケットにないことの実装を承認する
- ACが未定義のままレビューを通す

---

## チケットフィールド運用

AC・ブランチ名は `comment` フィールドに書かれている。

レビュー時は以下を確認する：

**comment フィールド**
- `## 受入条件（AC）` — レビューの照合基準
- `## ブランチ名` — 差分確認に使うブランチ名
- `## やらないこと（スコープ外）` — スコープ外の混入がないかの判断基準

レビューコメント（NG指摘・OK通知）も `comment` に追記する。

**blocks フィールド（カスタム・存在する場合のみ）**
- このチケットを `Fixed` にすると、`blocks` に記載されたチケットのブロックが解除される
- クローズ後に `blocks` の値を確認し、**「〇〇チケットの着手が可能になりました」とユーザーに通知する**

---

## レビューフロー

```bash
# 1. チケットとACを読む（必須）
fossil ticket show <uuid>

# 2. 実装差分を確認
fossil diff --branch ai/<uuid-prefix>-<topic>

# 3a. NGの場合 — コメントを追記してOpenに戻す
fossil ticket change <uuid> status Open

# 3b. OKの場合 — trunkにマージしてブランチをクローズ
fossil update trunk
fossil merge ai/<uuid-prefix>-<topic>
fossil commit -m "merge ai/<uuid-prefix>-<topic> → trunk (refs #<uuid-prefix>)"
fossil branch close ai/<uuid-prefix>-<topic>
fossil ticket change <uuid> status Fixed

# 4. blocks フィールドを確認（フィールドがある場合のみ）
fossil ticket show <uuid>
```

---

## レビューチェックリスト

### ACの照合
- [ ] チケットに書かれたACをすべて満たしているか
- [ ] スコープ外の実装が混入していないか

### コード品質
- [ ] 意図が読み取れるか（コメント・命名）
- [ ] 明らかなバグ・エラーハンドリング漏れがないか
- [ ] セキュリティ上の問題（XSS・インジェクション等）がないか

### Fossil運用
- [ ] コミットメッセージに `refs #<uuid-prefix>` が含まれているか
- [ ] trunk に直コミットされていないか
- [ ] 設計判断がwikiに記録されているか（判断があった場合）
- [ ] クローズ後に `blocks` フィールドを確認し、解除されたチケットをユーザーに通知したか（フィールドがある場合）

---

## NGフィードバックの書き方

```
## レビュー結果: NG

### 問題点
- AC「○○したとき△△が表示される」が満たされていない
  → ○○のケースで△△ではなく□□が表示される

### 修正依頼
- △△を表示するために〜〜の処理を追加してください

### 確認方法
- 〜〜の手順で動作確認してください
```
