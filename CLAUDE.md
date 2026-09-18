# maru-threads プロジェクト全体概要

## プロジェクトの目的

ママ向けThreadsアカウント（愛称: まる、"maru-threads"）を運用し、楽天アフィリエイト
（楽天ROOM／楽天アフィリエイトリンク）を通じて **月間10万円の収益** を達成する。
そのために、4つの専門サブエージェントがチームとして連携し、企画から執筆、事実確認、
最終編集までの投稿制作サイクルを回す。

アカウント・ターゲット・コンセプト・文体・法令ルールの詳細は `knowledge/` 以下の各ファイルを
必ず参照すること（本ファイルは全体のオーケストレーションのみを扱う）。作業前に必要に応じて
`knowledge/` を参照し、特に**実際に使用した商品の使用感（実体験）は `knowledge/products/` の
記載を正とする**。`fact-checker.md`・`editor.md` 内に実体験の抜粋（例: POLBAN ADVANCEの使用感）
が直接記載されている場合も、内容に齟齬が出ないよう `knowledge/products/` 側を更新の起点とする。

## サブエージェント構成（`.claude/agents/`）

Claude Codeのサブエージェント機能を使い、投稿制作は以下の順序で4エージェントが連携する
パイプラインとして進める。各エージェントの詳細な役割・システムプロンプトは
`.claude/agents/*.md` を参照。

| # | ファイル | エージェント名 | 役割概要 |
|---|----------|----------------|----------|
| 1 | `.claude/agents/content-strategist.md` | 投稿企画エージェント | `knowledge/00〜03` を読み、投稿テーマ・切り口・想定読者の悩みを企画提案する（Read/Grep/Globのみで、ファイル書き込みや外部検索は行わない） |
| 2 | `.claude/agents/post-writer.md` | 投稿執筆エージェント | 企画案をもとにThreads投稿本文の初稿を執筆する。出力には本文本体に加え、冒頭の別案2つ・楽天ROOMへの自然な導線・事実確認が必要な箇所を含める |
| 3 | `.claude/agents/editor.md` | 編集エージェント | post-writerの初稿を読者目線（冒頭の強さ・1投稿1テーマ・本音レビュー・読みやすさ・「まる」らしい文体・ROOM導線）で編集する。事実確認の完了判断はfact-checkerに委ねる |
| 4 | `.claude/agents/fact-checker.md` | 事実確認エージェント（最終ゲート） | editor編集後の投稿を対象に、実体験の裏付け・商品情報・断定表現・広告表記の要否を点検し、「公開可能／修正推奨／情報不足」の判定と修正文案を出力する |

## 投稿制作フロー

```
content-strategist (企画提案: テーマ・悩み・切り口・目的・必要資料を出力)
   → 人間が採用した企画を planning/ideas.md, planning/content_calendar.md に記録
       → post-writer (初稿執筆: 本文・冒頭別案2つ・ROOM導線・要確認事項を出力し、人間が drafts/ に保存)
           → editor (編集: 読者目線での本文編集・冒頭別案2つ・確認が必要な情報・fact-checkerへの引き継ぎ事項を出力)
               → 人間が編集結果を drafts/ に反映
                   → fact-checker (最終ゲート: 実体験・商品情報・断定表現・広告表記を点検し判定と修正文案を出力)
                       → 人間が指摘を踏まえて drafts/ を最終修正
                           → 人間が公開可否を確定し outputs/threads/ または outputs/rakuten_room/ に格納
                               → 投稿 (人間が最終承認して実際にThreads/楽天ROOMへ投稿)
                                   → analytics/post_results.csv に結果を記録
                                       → 人間が結果を踏まえて次回のcontent-strategistへの依頼内容に反映
```

エージェント間の役割分担（`editor.md` に明記のとおり）:
content-strategistが目的・テーマを決め、post-writerが初稿を作り、editorが読者目線で編集し、
fact-checkerが事実関係・誇張表現・未確認情報を確認する。**editorはfact-checkerの代わりに
事実確認が完了したと判断してはいけない**。

## ディレクトリ構成

```
maru-threads/
├── CLAUDE.md                       ← 本ファイル（プロジェクト全体指示・オーケストレーション）
│
├── .claude/
│   └── agents/
│       ├── content-strategist.md   # ① 投稿企画
│       ├── post-writer.md          # ② 投稿執筆（初稿）
│       ├── editor.md               # ③ 編集（読者目線での仕上げ）
│       └── fact-checker.md         # ④ 事実確認（最終ゲート）
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
├── drafts/                         ← 執筆途中の下書き（post-writerの出力を人間が保存→editorが編集→fact-checkerが最終点検→人間が最終反映）
│
├── outputs/                        ← 完成原稿（投稿用の最終稿）
│   ├── threads/
│   └── rakuten_room/
│
└── analytics/
    └── post_results.csv            # 投稿ごとの実績データ
```

## 全エージェント共通ルール

- **一次情報の確認**: 商品情報・価格・在庫・キャンペーン内容は必ず最新情報を確認する。古い情報のまま投稿しない。`fact-checker` は「未確認」の洗い出しと判定までを担い、実際の最新情報の調査・`knowledge/products/` の更新は人間（または`content-strategist`への依頼）が行う。
- **ステルスマーケティング規制への対応**: アフィリエイト投稿には必ず「#PR」「#広告」「#楽天ROOM」など、広告であることが一目でわかる表記を入れる（景品表示法のステマ規制対応）。詳細は `knowledge/04_compliance.md`。`editor` が導線確認時にチェックし、`fact-checker` が最終ゲートとして必ず確認する。
- **薬機法・医療表現への注意**: 健康・美容・子どもの発達に関する断定的な効果表現（「治る」「必ず痩せる」等）は使わない。
- **個人情報・子どもの顔写真**: 子どもの顔や特定できる情報を安易に出さない。プライバシーに配慮する。
- **炎上リスクの回避**: 特定の育児方針・宗教・政治・比較批判につながる表現は避け、共感ベースのトーンを保つ。
- **文体**: `knowledge/03_writing_rules.md` に従う（一人称は「私」、語りかけるような柔らかい口語体を基本とする）。
- **絵文字**: 使いすぎない。1投稿につき1〜3個程度を目安に、読みやすさを優先する。
- **実体験を創作しない**: ユーザーから確認が取れていない実体験・感想・使用実績を創作しない。
  実際に使った商品と未使用の商品を明確に区別する。
- **不明な情報はユーザーに確認する**: 商品情報・価格・キャンペーン状況・楽天ROOM掲載状況などが
  未確認の場合は、推測で埋めず「要確認」と明示してユーザーに確認する。
- **完成物の定義**: `fact-checker` が「公開可能」と判定するまでは下書き・編集中の扱いとし、
  完成原稿（`outputs/`格納対象）として扱わない（詳細は「ファイル配置のルール」を参照）。
- **投稿の最終承認**: 完成原稿であっても、実際にThreads・楽天ROOMへ投稿するのはユーザーが
  最終承認してから。エージェントやユーザーの承認なしに自動でSNSへ投稿することはない。

## ファイル配置のルール

- 企画段階のネタ・スケジュールは `planning/` に置く。
- 執筆中（未確定）の原稿は `drafts/` に置く。ファイル名は日付＋概要を推奨（例: `2026-09-18_離乳食時短.md`）。
- `post-writer`・`editor`・`fact-checker` はいずれもファイルを直接編集しない。各エージェントは
  会話上でテキストを出力するので、人間がその内容を `drafts/` の該当ファイルに反映しながら
  post-writer → editor → fact-checker の順で引き継ぐ。
- `fact-checker` が「公開可能」と判定した原稿のみ、人間が `outputs/threads/` または
  `outputs/rakuten_room/` に格納する。
- 投稿後の実績は `analytics/post_results.csv` に追記する（フォーマットは同ファイルのヘッダーを参照）。
- 商品ごとの情報は `knowledge/products/` に1商品1ファイルで管理し、価格・レビュー件数などは
  確認するたびに更新日を付けて更新する。
