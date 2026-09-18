# 共有ナレッジベース (knowledge/)

6エージェントすべてが共通して参照・更新する「チームの記憶」。個々の投稿の下書きや分析レポート
（各エージェントフォルダの `outputs/` に蓄積する想定）とは異なり、ここには
**複数の投稿・複数のサイクルを通じて蓄積・再利用される知見** を置く。

新しいサイクルを始めるエージェントは、まずここを確認してから作業を始めること。
知見を得たエージェントは、都度ここを更新し、他のエージェントが次のサイクルで使えるようにすること。

## ファイル一覧

| ファイル | 内容 | 主な更新者 | 主な参照者 |
|---|---|---|---|
| `products.md` | 楽天商品データベース（紹介実績・成果込み） | `01-research`, `06-post-review-analysis` | `01-research`, `04-affiliate-writer` |
| `post-patterns.md` | 伸びた投稿の型・フック・時間帯の傾向 | `06-post-review-analysis` | `02-post-structure`, `03-value-empathy-writer`, `04-affiliate-writer` |
| `ng-expressions.md` | 法令NG表現・炎上回避ルール・広告表記ルール | `05-proofreading` | `03-value-empathy-writer`, `04-affiliate-writer`, `05-proofreading` |
| `hashtags.md` | カテゴリ別ハッシュタグ集と実績 | `06-post-review-analysis` | `02-post-structure`, `03-value-empathy-writer`, `04-affiliate-writer` |
| `audience-insights.md` | フォロワー・ターゲットの悩み/ニーズの蓄積 | `01-research`, `06-post-review-analysis` | `01-research`, `02-post-structure` |

## 運用ルール

- **追記型で育てる**: 既存の記述を安易に削除せず、古くなった情報は「※古い情報」等の注記を付けて
  残すか、明確に更新日を付けて上書きする。判断に迷う場合は追記に留める。
- **更新日を残す**: 各ファイル内の項目には、可能な範囲で更新日（例: `2026-09-18時点`）を添える。
  特に商品の価格・キャンペーン情報は鮮度が命なので必須とする。
- **一次情報ベース**: 推測や不確かな情報は「仮説」であることを明記する（例: 「〜という仮説」）。
  実データに基づく確定情報と混同しない。
- **法令・規約に関する情報は最優先で正確に**: `ng-expressions.md` は特に、景品表示法・薬機法・
  ステルスマーケティング規制に関わるため、誤りがあれば気づいた時点で誰でも即座に修正してよい。
- **肥大化を防ぐ**: 各ファイルが長くなりすぎたら、カテゴリごとに見出しを整理する。
  重複エントリは統合する。

## 各エージェントの使い方（サマリー）

- **01-research**: `audience-insights.md` と `products.md` を確認してからネタ出しを行い、
  新しく見つけた悩み・商品情報を追記する。
- **02-post-structure**: `post-patterns.md` を確認し、実績のある型を優先的に使う。
- **03-value-empathy-writer / 04-affiliate-writer**: `ng-expressions.md` で避けるべき表現を確認し、
  `hashtags.md` から実績のあるハッシュタグを選ぶ。
- **05-proofreading**: 校正の過程で新たに見つけたNG表現・リスク事例を `ng-expressions.md` に追記する。
- **06-post-review-analysis**: 分析結果を `post-patterns.md`・`hashtags.md`・`products.md` に反映し、
  次のサイクルに知見を還元する。
