# DXコンサルティング 管理リポジトリ

## このリポジトリについて

DXコンサルティング業務における全クライアント管理リポジトリ。
営業提案・法務対応・PJデリバリーを一元管理する。

## クライアント一覧

| ディレクトリ | クライアント | フェーズ |
|---|---|---|
| clients/sammy/ | サミー | 要更新 |
| clients/houmu/ | 法務クライアント | 要更新 |
| clients/hitachikenki/ | 日立建機 | 要更新 |
| clients/cyplus/ | サイプラス | 要更新 |

## ディレクトリ構成

```
consulting/
├── CLAUDE.md                   # このファイル
├── .claude/
│   ├── commands/               # プロジェクトskills（スラッシュコマンド）
│   └── skills/                 # オリジナル.skillファイル
├── clients/
│   └── [client]/               # クライアントごとのPJフォルダ
│       ├── CLAUDE.md           # クライアント固有コンテキスト
│       ├── README.md           # 案件概要・打ち合わせ履歴
│       ├── PENDING_ACTIONS.md  # 未決事項
│       ├── minutes/            # 議事録 (mtg_YYMMDD.md)
│       ├── gaiho/              # 外報 (gaiho_YYMMDD.md)
│       ├── checklist/          # チェックリスト診断
│       ├── slides/             # スライド（drafts/pptx/assets）
│       ├── client_docs/        # クライアント受領資料
│       ├── proposals/          # 提案書・SoW・見積
│       ├── internal/           # 社内向け資料
│       └── archive/            # アーカイブ
└── _shared/
    └── templates/              # 共通テンプレート
```

## Skills一覧

| コマンド | 用途 |
|---|---|
| `/init-project` | 新規案件のPJフォルダを標準構成で作成 |
| `/transcript-cleaner` | 書き起こし → 議事録Markdown生成 |
| `/minute-to-gaiho` | 議事録 → 外報作成 |
| `/proposal-story-designer` | AI検証開発の提案ストーリー設計 |
| `/enterprise-system-proposal-story` | 基幹システムの提案ストーリー設計 |
| `/discussion-slide-draft` | 次回打ち合わせ用ディスカッションスライド生成 |
| `/slide-draft-template` | スライドドラフト構成案の整形 |
| `/consulting-slide` | ドラフト → MBBスタイル .pptx生成 |
| `/sow-generator-poc` | PoC向けSoW（作業明細書）生成 |
| `/estimate-review-request` | 見積レビュー依頼 |
| `/daily-action-planner` | 日次アクション計画 |

## 作業ルール

- クライアント作業時は `clients/[client]/` ディレクトリ内で作業する
- 議事録は `minutes/mtg_YYMMDD.md` 形式で保存
- 商談後は `/transcript-cleaner` → `/minute-to-gaiho` の順で処理
- 新規案件開始時は `/init-project` を使用
