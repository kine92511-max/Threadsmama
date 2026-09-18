# maru-threads プロジェクト全体概要

## プロジェクトの目的

ママ向けThreadsアカウント（愛称: まる、"maru-threads"）を運用し、楽天アフィリエイト
（楽天ROOM／楽天アフィリエイトリンク）を通じて **月間10万円の収益** を達成する。
そのために、4つの専門サブエージェントがチームとして連携し、企画から執筆、事実確認、
最終編集までの投稿制作サイクルを回す。

アカウント・ターゲット・コンセプト・文体・法令ルールの詳細は `knowledge/` 以下の各ファイルを
必ず参照すること（本ファイルは全体のオーケストレーションのみを扱う）。

## サブエージェント構成（`.claude/agents/`）

Claude Codeのサブエージェント機能を使い、投稿制作は以下の順序で4エージェントが連携する
パイプラインとして進める。各エージェントの詳細な役割・システムプロンプトは
`.claude/agents/*.md` を参照。

| # | ファイル | エージェント名 | 役割概要 |
|---|----------|----------------|----------|
| 1 | `.claude/agents/content-strategist.md` | 投稿企画エージェント | `knowledge/00〜03` を読み、投稿テーマ・切り口・想定読者の悩みを企画提案する（Read/Grep/Globのみで、ファイル書き込みや外部検索は行わない） |
| 2 | `.claude/agents/copywriter.md` | 投稿執筆エージェント | 企画案とテンプレートをもとに、有益・共感系／アフィリエイト系の本文を執筆 |
| 3 | `.claude/agents/fact-checker.md` | 事実確認エージェント | `drafts/` の下書きを `knowledge/products/`・`knowledge/04_compliance.md` と照合し、実体験の裏付け・商品仕様・誇張表現・広告表記の要否を点検して指摘する（Read/Grep/Globのみで、ファイル修正や外部検索は行わない） |
| 4 | `.claude/agents/editor.md` | 最終編集エージェント | 文章・トーン・表記統一、コンプライアンス最終チェック、公開可否判断 |

## 投稿制作フロー

```
content-strategist (企画提案: テーマ・悩み・切り口・目的・必要資料を出力)
   → 人間が採用した企画を planning/ideas.md, planning/content_calendar.md に記録
       → copywriter (執筆: templates/ を使い drafts/ に下書き作成)
           → fact-checker (事実確認: 商品情報・実体験・誇張表現・広告表記の要否を点検し指摘事項を出力)
               → 人間(またはcopywriter)が指摘を踏まえて drafts/ を修正
                   → editor (最終編集: 文体統一・コンプライアンス最終確認)
                       → outputs/threads/ または outputs/rakuten_room/ に完成稿を格納
                           → 投稿 (人間が最終承認して実際にThreads/楽天ROOMへ投稿)
                               → analytics/post_results.csv に結果を記録
                                   → 人間が結果を踏まえて次回のcontent-strategistへの依頼内容に反映
```

## ディレクトリ構成

```
maru-threads/
├── CLAUDE.md                       ← 本ファイル（プロジェクト全体指示・オーケストレーション）
│
├── .claude/
│   └── agents/
│       ├── content-strategist.md   # ① 投稿企画
│       ├── copywriter.md           # ② 投稿執筆
│       ├── fact-checker.md         # ③ 事実確認
│       └── editor.md               # ④ 最終編集
│
├── knowledge/                      ← チーム共通のナレッジベース
│   ├── 00_account.md               # アカウント基本情報（名前・プロフィール・リンク等）
│   ├── 01_target.md                # ターゲット像・ペルソナ・悩みリスト
│   ├── 02_concept.md               # アカウントコンセプト・KPI・投稿比率
│   ├── 03_writing_rules.md         # 文体・トーン・表記ルール
│   ├── 04_compliance.md            # 法令NG表現・広告表記・炎上回避ルール
│   └── products/                   # 個別商品ナレッジ（アフィリエイト対象商品）
│       ├── polban_advance.md
│       ├── cybex_stroller.md
│       ├── ergobaby_carrier.md
│       ├── diaper_bag.md
│       └── joie_car_seat.md
│
├── templates/                      ← 投稿テンプレート
│   ├── threads_empathy.md          # 共感・あるある系Threads投稿の型
│   ├── threads_review.md           # 商品レビュー系Threads投稿の型
│   └── rakuten_room.md             # 楽天ROOM投稿の型
│
├── planning/                       ← 企画・スケジュール管理
│   ├── ideas.md                    # ネタ案ストック
│   └── content_calendar.md         # 投稿カレンダー
│
├── drafts/                         ← 執筆途中の下書き（copywriterが作成→fact-checkerが点検→人間が修正反映→editorが仕上げ）
│
├── outputs/                        ← 完成原稿（投稿用の最終稿）
│   ├── threads/
│   └── rakuten_room/
│
└── analytics/
    └── post_results.csv            # 投稿ごとの実績データ
```

## 全エージェント共通ルール

- **一次情報の確認**: 商品情報・価格・在庫・キャンペーン内容は必ず最新情報を確認する。古い情報のまま投稿しない。`fact-checker` はRead/Grep/Globのみのため、`knowledge/products/` との照合と「未確認」の洗い出しまでを担い、実際の最新情報の調査・更新は人間（または`content-strategist`への依頼）が行う。
- **ステルスマーケティング規制への対応**: アフィリエイト投稿には必ず「#PR」「#広告」「#楽天ROOM」など、広告であることが一目でわかる表記を入れる（景品表示法のステマ規制対応）。詳細は `knowledge/04_compliance.md`。これは `editor` エージェントが必ず最終チェックする。
- **薬機法・医療表現への注意**: 健康・美容・子どもの発達に関する断定的な効果表現（「治る」「必ず痩せる」等）は使わない。
- **個人情報・子どもの顔写真**: 子どもの顔や特定できる情報を安易に出さない。プライバシーに配慮する。
- **炎上リスクの回避**: 特定の育児方針・宗教・政治・比較批判につながる表現は避け、共感ベースのトーンを保つ。
- **文体**: `knowledge/03_writing_rules.md` に従う（一人称は「私」、語りかけるような柔らかい口語体を基本とする）。
- **絵文字**: 使いすぎない。1投稿につき1〜3個程度を目安に、読みやすさを優先する。

## ファイル配置のルール

- 企画段階のネタ・スケジュールは `planning/` に置く。
- 執筆中（未確定）の原稿は `drafts/` に置く。ファイル名は日付＋概要を推奨（例: `2026-09-18_離乳食時短.md`）。
- `fact-checker` はファイルを直接編集しない（Read/Grep/Globのみ）。指摘事項（問題のある文章・理由・
  修正案・確認事項）を出力するので、人間または`copywriter`がそれを`drafts/`に反映してから`editor`に渡す。
- `editor` が最終承認した原稿のみ `outputs/threads/` または `outputs/rakuten_room/` に移動する。
- 投稿後の実績は `analytics/post_results.csv` に追記する（フォーマットは同ファイルのヘッダーを参照）。
- 商品ごとの情報は `knowledge/products/` に1商品1ファイルで管理し、価格・レビュー件数などは
  確認するたびに更新日を付けて更新する。
