---
name: init-project
description: 新規案件のPJフォルダを標準構成で自動作成する初期設定スキル。クライアント名・案件名を入力するだけで、スキル群が期待するファイル配置に準拠したフォルダツリーとREADME・PENDING_ACTIONSのテンプレートを一括生成する。ユーザーが「PJ作成」「案件フォルダ作成」「プロジェクト初期化」「init project」「新規案件セットアップ」などと言った場合にこのスキルを使用する。
---

# Init Project Skill

## 概要

新規案件（PJ）のフォルダを標準構成で自動作成する。既存のスキル群（post-meeting-pipeline, minute-to-gaiho, project-definition-checker, discussion-slide-draft, consulting-slide, concept-slide）が期待するファイル命名規則・配置に準拠した構成を一発で生成する。

**目的:**
- 案件ごとの情報を一箇所に集約し、スキル実行時に「どのファイルをどこから読むか」で迷わない
- Claude Code で `cd /path/to/project` → パイプライン実行、の流れを確立する
- 打ち合わせを重ねるごとにファイルが蓄積されても、構造が破綻しない

## トリガー（使用タイミング）

- 「PJ作成」「案件フォルダ作成」「プロジェクト初期化」「init project」「新規案件セットアップ」
- 新しいクライアントとの商談が始まった場合
- 既存クライアントで新しいテーマの商談が始まった場合

---

## PJフォルダ構成

### 設計原則

1. **時系列で蓄積される情報（議事録・外報）はフラットに並べる** — サブフォルダに分けると見通しが悪くなる。ファイル名のプレフィックスで時系列が自明になる設計にする
2. **スキルの出力物は専用フォルダにまとめる** — チェックリスト・スライド等の「加工物」は元の「生データ」と分離する
3. **クライアントから受領した資料は独立フォルダ** — 議事録と混在させない
4. **PJルートに状態管理ファイルを置く** — README.md（案件概要）、PENDING_ACTIONS.md（未決事項）

### フォルダツリー

```
{client_name}_{project_name}/
│
├── README.md                        # 案件概要（PJメタ情報の一元管理）
├── PENDING_ACTIONS.md               # 未決事項・要確認リスト（スキル横断で追記）
│
├── minutes/                         # 議事録（生データ）
│   ├── mtg_250215.md                # 第1回 打ち合わせメモ
│   ├── mtg_250228.md                # 第2回
│   └── ...
│
├── gaiho/                           # 外報（minute-to-gaiho の出力）
│   ├── gaiho_250215.md
│   ├── gaiho_250228.md
│   └── ...
│
├── checklist/                       # チェックリスト診断（project-definition-checker の出力）
│   ├── project_checklist_20250215.md
│   ├── project_checklist_20250228.md
│   └── ...
│
├── slides/                          # スライド関連（ドラフト + pptx）
│   ├── drafts/                      # discussion-slide-draft の出力
│   │   ├── slide_draft_20250310.md
│   │   └── ...
│   ├── pptx/                        # consulting-slide / concept-slide の出力
│   │   ├── meeting_slides_20250310.pptx
│   │   └── ...
│   └── assets/                      # スライドに使う画像・図・素材
│
├── client_docs/                     # クライアントから受領した資料
│   ├── sample_data/                 # サンプルデータ
│   ├── requirements/                # 要件・仕様書
│   └── others/                      # その他（組織図、既存システム資料等）
│
├── proposals/                       # 提案書・SoW・見積
│   ├── sow_draft_v1.md
│   ├── estimate_v1.xlsx
│   └── ...
│
├── internal/                        # 社内向け資料（外部共有不可）
│   ├── qualify_memo.md              # PJ-qualify判断メモ
│   ├── dev_brief.md                 # Dev相談用ブリーフ
│   ├── pipeline_summary_20250215.md # パイプライン実行サマリー
│   └── ...
│
└── archive/                         # アーカイブ（古いバージョン、不要になった資料）
```

### ファイル命名規則

| カテゴリ | パターン | 例 | 日付の意味 |
|---------|---------|-----|-----------|
| 議事録 | `mtg_YYMMDD.md` | `mtg_250215.md` | 打ち合わせ実施日 |
| 外報 | `gaiho_YYMMDD.md` | `gaiho_250215.md` | 打ち合わせ実施日 |
| チェックリスト | `project_checklist_YYYYMMDD.md` | `project_checklist_20250215.md` | 診断実行日 |
| スライドドラフト | `slide_draft_YYYYMMDD.md` | `slide_draft_20250310.md` | 次回MTG予定日 or 作成日 |
| スライド | `meeting_slides_YYYYMMDD.pptx` | `meeting_slides_20250310.pptx` | 次回MTG予定日 or 作成日 |
| サマリー | `pipeline_summary_YYYYMMDD.md` | `pipeline_summary_20250215.md` | パイプライン実行日 |

---

## README.md テンプレート

PJルートに生成される `README.md` は案件のメタ情報を一元管理するファイルとなる。

```markdown
# {案件名}

## 基本情報

| 項目 | 内容 |
|------|------|
| **クライアント** | {クライアント名} |
| **案件名** | {案件名} |
| **当社担当** | {担当者名}（要入力） |
| **先方担当** | （初回MTG後に記入） |
| **商談開始日** | {YYYY-MM-DD} |
| **現在のフェーズ** | 初回ヒアリング |
| **PJフォルダ** | `{フォルダパス}` |

## 商談ステータス

### チェックリスト充足率
（project-definition-checker 実行後に自動更新）

| カテゴリ | ステータス |
|---------|-----------|
| ASIS | 【未】 |
| TOBE | 【未】 |
| HOW | 【未】 |
| Roadmap | 【未】 |
| Technical | 【未】 |

### 社内アクション
（project-definition-checker 実行後に自動更新）

| アクション | ステータス |
|-----------|-----------|
| A. NDA締結 | ⚪未該当 |
| B. PJ-qualify | ⚪未該当 |
| C. Buddy アサイン | ⚪未該当 |
| D. Dev相談 | ⚪未該当 |
| E. SoW作成依頼 | ⚪未該当 |
| F. 概算review | ⚪未該当 |
| G. PJ-review | ⚪未該当 |

## 打ち合わせ履歴

| # | 日付 | 参加者 | 議事録 | 外報 | チェックリスト |
|---|------|--------|--------|------|---------------|
| 1 | {YYYY-MM-DD} | （MTG後記入） | `minutes/mtg_YYMMDD.md` | `gaiho/gaiho_YYMMDD.md` | `checklist/project_checklist_YYYYMMDD.md` |

## メモ
（案件固有の注意事項、背景情報、競合情報などを自由記述）

```

---

## PENDING_ACTIONS.md テンプレート

```markdown
# PENDING_ACTIONS — {案件名}

> スキル実行時に `(要確認)` と判定された未決事項を集約するファイル。
> 各スキル（minute-to-gaiho, project-definition-checker 等）が自動追記する。
> 解決したら ✅ を付けて残す（削除しない）。

## 未解決

（まだ項目はありません。パイプライン実行後に自動追記されます。）

## 解決済み

（なし）
```

---

## 思考プロセス（厳守）

### Step 1: 必要情報の収集

ユーザーに以下を確認する（チャットの文脈から読み取れる場合は確認不要）：

| 項目 | 必須/任意 | 例 |
|------|----------|-----|
| クライアント名 | 必須 | 「東京電力」 |
| 案件名（テーマ） | 必須 | 「請求書OCR自動化」 |
| 当社担当者名 | 任意（後で記入可） | 「田中」 |
| PJ作成先の親ディレクトリ | 必須 | `/home/user/projects/` |

**フォルダ名の生成ルール:**
- `{client_name}_{project_name}` の形式
- 日本語はそのまま使用可（ファイルシステムがUTF-8対応の場合）
- スペースはアンダースコアに置換
- 例: `東京電力_請求書OCR自動化`、`ABC商事_在庫管理AI`

### Step 2: フォルダ・ファイルの一括生成

以下をすべて一括で生成する：

```bash
# 1. PJルートフォルダ
mkdir -p "${PJ_ROOT}"

# 2. サブフォルダ
mkdir -p "${PJ_ROOT}/minutes"
mkdir -p "${PJ_ROOT}/gaiho"
mkdir -p "${PJ_ROOT}/checklist"
mkdir -p "${PJ_ROOT}/slides/drafts"
mkdir -p "${PJ_ROOT}/slides/pptx"
mkdir -p "${PJ_ROOT}/slides/assets"
mkdir -p "${PJ_ROOT}/client_docs/sample_data"
mkdir -p "${PJ_ROOT}/client_docs/requirements"
mkdir -p "${PJ_ROOT}/client_docs/others"
mkdir -p "${PJ_ROOT}/proposals"
mkdir -p "${PJ_ROOT}/internal"
mkdir -p "${PJ_ROOT}/archive"

# 3. テンプレートファイル
# README.md — 案件メタ情報（上記テンプレートを変数置換して生成）
# PENDING_ACTIONS.md — 未決事項リスト（上記テンプレートで生成）
```

### Step 3: 生成結果の表示

生成したフォルダツリーを `tree` コマンド（または同等の表示）でユーザーに見せ、以下を案内する：

```
PJフォルダを作成しました。

📁 {PJ_ROOT}/
├── README.md           ← 案件概要（担当者名等を追記してください）
├── PENDING_ACTIONS.md  ← 未決事項（パイプライン実行時に自動追記）
├── minutes/            ← 議事録を mtg_YYMMDD.md で保存
├── gaiho/              ← 外報（パイプラインが自動生成）
├── checklist/          ← チェックリスト（パイプラインが自動生成）
├── slides/             ← スライド（パイプラインが自動生成）
├── client_docs/        ← クライアントから受領した資料を格納
├── proposals/          ← 提案書・SoW・見積
├── internal/           ← 社内向け資料
└── archive/            ← アーカイブ

次のステップ:
1. 議事録メモを minutes/mtg_YYMMDD.md として保存
2. 「商談処理」と入力してパイプラインを開始
```

---

## Claude Code での運用イメージ

### パイプライン実行時のファイル参照

Claude Code でパイプラインを実行する際、PJフォルダ内のファイルがスキルの入力となる。以下のように参照パスを指定する：

```
# PJフォルダをカレントディレクトリにして作業
cd /path/to/東京電力_請求書OCR自動化

# パイプライン実行（議事録を指定）
# → minutes/ 内の議事録 + gaiho/ 内の過去外報 + checklist/ 内の過去診断
#   をすべて読み込んでパイプラインを実行

# パイプライン完了後の出力先:
# → gaiho/gaiho_YYMMDD.md
# → checklist/project_checklist_YYYYMMDD.md
# → slides/drafts/slide_draft_YYYYMMDD.md
# → slides/pptx/meeting_slides_YYYYMMDD.pptx
# → internal/pipeline_summary_YYYYMMDD.md
```

### PJフォルダとスキルのファイルパス対応表

| スキル | 入力元（PJフォルダ内） | 出力先（PJフォルダ内） |
|--------|---------------------|---------------------|
| minute-to-gaiho | `minutes/mtg_YYMMDD.md` | `gaiho/gaiho_YYMMDD.md` |
| project-definition-checker | `gaiho/` + `minutes/` + `checklist/`（全量） | `checklist/project_checklist_YYYYMMDD.md` |
| discussion-slide-draft | `checklist/`（最新） + `minutes/` + `gaiho/` | `slides/drafts/slide_draft_YYYYMMDD.md` |
| consulting-slide | `slides/drafts/slide_draft_YYYYMMDD.md` | `slides/pptx/meeting_slides_YYYYMMDD.pptx` |
| concept-slide | `slides/drafts/slide_draft_YYYYMMDD.md` | `slides/pptx/meeting_slides_YYYYMMDD.pptx` |
| post-meeting-pipeline | PJフォルダ全体 | 上記すべて + `internal/pipeline_summary_YYYYMMDD.md` |

---

## 処理ルール（厳守事項）

1. **フォルダ名の一意性**: 同一の `{client_name}_{project_name}` が既に存在する場合は、上書きせずユーザーに確認する
2. **テンプレートの変数置換**: README.md の `{案件名}` `{クライアント名}` 等は、Step 1 で取得した情報で必ず置換する。プレースホルダーのまま残さない
3. **空フォルダの維持**: 各サブフォルダには `.gitkeep` を配置し、Git管理時に空フォルダが消えないようにする
4. **既存PJへの追加実行禁止**: 本スキルは新規作成専用。既存PJのフォルダ構成を変更する場合は手動で行う

---

## 関連スキルとの連携

| スキル | 関係 |
|--------|------|
| **post-meeting-pipeline** | 本スキルで作成したPJフォルダ上でパイプラインを実行する。入出力パスが整合する設計 |
| **minute-to-gaiho** | `minutes/` → `gaiho/` のファイルフローを前提 |
| **project-definition-checker** | `gaiho/` + `minutes/` + `checklist/` を全量読み込み |
| **discussion-slide-draft** | `checklist/`（最新）を起点にドラフト生成 |
| **consulting-slide / concept-slide** | `slides/drafts/` のドラフトから `slides/pptx/` に出力 |
