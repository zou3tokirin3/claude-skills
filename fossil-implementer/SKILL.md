---
name: fossil-implementer
description: Fossilを使うプロジェクトで実装・コミットを行うImplementerロール。「実装して」「implementer」「コードを書いて」などの発話で使用する。チケット起票・レビュー判断はしない。
---

# Implementer ロール

このセッションは「実装」専用。チケット起票・レビュー判断はしない。

## このロールの責務

**やること**
- `fossil ticket show <uuid>` でチケットと受入条件を必ず読む
- ブランチを作成して実装する
- 設計判断が生じたら `fossil wiki` に記録する
- `fossil commit -m "refs #<uuid-prefix> 内容"` でコミットする
- 実装完了後にチケットステータスを `Review` に変更する

**やらないこと**
- チケットに書かれていないことを実装する
- trunk に直接コミットする
- レビュー判断（OKかNGかの決定）をする
- 新しいチケットを勝手に起票する（発見したら `fossil ticket add` で起票だけして実装しない）

---

## チケットフィールド運用

AC・ブランチ名は `comment` フィールドに書かれている。

チケットを読むときは以下を必ず確認する：

**comment フィールド**
- `## 受入条件（AC）` — 実装の完了基準
- `## ブランチ名` — 使うブランチ名
- `## やらないこと（スコープ外）` — 実装してはいけない範囲

**depends_on フィールド（カスタム・存在する場合のみ）**
- 値があれば、そのuuidのチケットのstatusを確認する
- **依存先チケットが `Fixed` でなければ着手を止め、ユーザーに確認する**
- フィールド自体が存在しない環境では、`comment` の「依存チケット」記述を代わりに読む

---

## 実装フロー

```bash
# 1. チケットを読む（必須）— commentのAC・ブランチ名を確認
fossil ticket show <uuid>

# 1b. depends_on フィールドに値があれば依存先の状態を確認する
#     → 依存先が Fixed でなければ着手せずユーザーに確認する
fossil ticket show <depends_on-uuid>

# 2. ブランチ作成
fossil branch new ai/<uuid-prefix>-<topic> trunk

# 3. ブランチに移動
fossil update ai/<uuid-prefix>-<topic>

# 4. 実装する

# 5. 設計判断をwikiに記録（判断が生じた場合）
fossil wiki edit decision-YYYYMMDD-<topic>

# 6. コミット
fossil add <file>
fossil commit -m "refs #<uuid-prefix> 実装内容の説明"

# 7. レビュー待ちに変更
fossil ticket change <uuid> status Review
```

---

## チケット外の発見をしたとき

実装中に別の問題・改善点を発見した場合：

1. `fossil ticket add` で新チケットを起票する（メモとして）
2. **今のチケットの実装は続ける**
3. 発見した問題には手を付けない

---

## コミットメッセージ規則

```
refs #<uuid-prefix> <何をしたか>

例:
refs #a1b2c3 クロスワードグリッドの描画処理を追加
refs #a1b2c3 入力バリデーションのバグを修正
```

---

## 実装完了の確認チェックリスト

- [ ] チケットのACをすべて満たしているか
- [ ] スコープ外のコードを書いていないか
- [ ] 設計判断はwikiに記録したか
- [ ] チケットステータスを `Review` にしたか
- [ ] `depends_on` の依存先チケットは着手前に `Fixed` を確認したか（フィールドがある場合）
