---
name: consulting-slide
description: 与えられたスライドドラフト（テキストベースの構成案）をベースに、MBB（McKinsey, BCG, Bain）スタイルの戦略コンサルティングスライド（.pptx）を生成するスキル。「論理の透過性」に特化し、Action Title・MECE図解・出典注釈を備えた1枚スライドを高品質に仕上げる。slide-draft-templateスキルの標準出力フォーマット（Tagline/Action Title/図解タイプ/Body/Accent/Footnotes）を直接受け取って高速にスライド化できる。ユーザーが「コンサルスライド」「MBBスタイル」「戦略スライド」「ドラフトからスライド作成」「スライド化して」などと言った場合、またはスライドドラフト（構成案・メモ・箇条書き・slide-draft-templateの出力）を渡してスライド化を依頼した場合にこのスキルを使用する。
---

# Consulting Slide Skill

## 概要

テキストベースのスライドドラフト（構成案・箇条書き・メモ等）を入力として受け取り、MBBクオリティの戦略コンサルティングスライド（.pptx）を生成する。

**このスキルは必ず `/mnt/skills/public/pptx/SKILL.md` と併用すること。** pptxgenjs の使い方・QA手順・画像変換はそちらを参照する。

## トリガー（使用タイミング）

- ユーザーがスライドドラフト（テキスト構成案）を渡して「スライドにして」と依頼した場合
- 「コンサルスライド」「MBBスタイル」「戦略スライド」「McKinseyスタイル」等の言及がある場合
- Action Title付きの論理構造スライドが求められている場合

---

## 思考プロセス（厳守）

### Step 0: ドラフトの解析

#### A. `slide-draft-template` スキルの標準出力フォーマットの場合（推奨ルート）

`slide-draft-template` スキルが生成したドラフトは以下の構造化フォーマットで届く。**このフォーマットを検出した場合、各フィールドを直接マッピングし、Step 0の自動判定をスキップして Step 1 へ進む。**

```markdown
## スライド [番号]: [タイトル]

### Tagline
[スライドが扱う領域]

### Action Title
[結論 = So what?]

### 図解タイプ
[タイプ名]

### Body
[構造化データ]

### Accent
[最も伝えたい1点]

### Footnotes
[出典・注釈]
```

**マッピング規則:**
| ドラフトフィールド | consulting-slideでの扱い |
|---|---|
| `Tagline` | Layer 1 Tagline にそのまま使用 |
| `Action Title` | Layer 2 Action Title にそのまま使用。「〜すべきである」等の主張形式になっているはずだが、主張が弱い場合は補強する |
| `図解タイプ` | 後述の図解タイプ対応表に従って実装パターンを選択 |
| `Body` | 図解タイプのサブフォーマットに従って構造化データを読み取る |
| `Accent` | Body 内の指定要素に Accent Color（`5B0404`）を適用。**1箇所のみ**。指定がない場合は自動判定 |
| `Footnotes` | Layer 4 Footnotes にそのまま使用。`[要確認]` プレースホルダーが含まれる場合はそのまま記載する |

#### B. 非構造化ドラフト（メモ・箇条書き・口頭指示）の場合

ユーザーから直接渡された非構造化テキストを解析し、以下を特定する：

1. **テーマ（Tagline）**: スライドが扱う領域（数単語）
2. **結論（Action Title）**: 「So what?」＝主張・提言。ドラフトに明示されていなければユーザーに確認する
3. **図解タイプ**: ドラフトの情報構造から以下の対応表で判定する
4. **出典・注釈**: ドラフト内のデータソース、補足情報

> **推奨:** 非構造化ドラフトが届いた場合、`slide-draft-template` スキルで先に整形することを提案してもよい。ただし、ユーザーが「そのままスライド化して」と言った場合はそのまま進む。

---

### 図解タイプ対応表

`slide-draft-template` スキルが出力する16タイプと、consulting-slideでの実装パターンの対応：

| 図解タイプ | 実装パターン | 備考 |
|---|---|---|
| `comparison` | 左右対比表 or 2列比較 | ゼブラストライプ、枠線なし |
| `process` | 水平シェブロン（3〜5ステップ） | RECTANGLE + 三角形で疑似矢印 |
| `hierarchy` | ピラミッド or ロジックツリー | 上位→下位の分解 |
| `kpi` | 数値コールアウト（2〜4個） | 大きな数値＋ラベル＋変化率 |
| `table` | 構造化テーブル | ゼブラストライプ、枠線なし |
| `matrix` | 2x2マトリクス | 4象限、軸ラベル付き |
| `timeline` | 1ストリームの時系列ライン | 上下交互にラベル配置 |
| `roadmap` | 横軸=フェーズ、縦軸=レーン | 複数ワークストリームの並行表示 |
| `flowchart` | 条件分岐フロー | 菱形（判断）＋矩形（処理）＋矢印 |
| `architecture` | レイヤー積み上げ図 | 各レイヤーを帯状に配置 |
| `venn` | ベン図（2〜3集合） | 楕円の重なりで共通領域を表現 |
| `before-after` | 左右対比＋中央に変革要因 | 矢印（→）で変化の方向を示す |
| `grouping` | カード型グルーピング | 2〜4カテゴリをボックスで整理 |
| `data-model` | エンティティ＋リレーション図 | 矩形ノード＋ラベル付きエッジ |
| `swimlane` | 横帯レーン＋フローステップ | レーン間の矢印でハンドオフを表現 |
| `process-deep-dive` | I/O＋技術手法の複合レイアウト | 左にInput/Output、右に技術解説 |

上記に対応する pptxgenjs 実装サンプルは「Body Area 実装例」セクションを参照。未掲載タイプについては、`/mnt/skills/public/pptx/SKILL.md` の `pptxgenjs.md` を参照しながら、同じデザインシステム（カラー・フォント・マージン）に従って実装する。

### Step 1: スライド4層構造の設計

スライドは **必ず** 上から順に以下4層で構成する：

```
┌─────────────────────────────────────────────┐
│ Tagline（左上、小さく）                       │
├─────────────────────────────────────────────┤
│ Action Title（2〜3行、結論＝So what?）        │
│─────────────── 区切り線 ─────────────────────│
├─────────────────────────────────────────────┤
│                                             │
│            Body Area（図解・グラフ）           │
│                                             │
├─────────────────────────────────────────────┤
│ Footnotes & Sources（8-9pt）                 │
└─────────────────────────────────────────────┘
```

**Action Title のルール:**
- 単なる状況説明ではなく「〜により、〜すべきである」「〜の結果、〜が必要」という**主張**を含める
- 下線は引かない。メッセージエリアの下に**細い区切り線（1pt, Light Gray）**を1本入れる

**Footnotes のルール:**
- 出典が無いスライドは戦略コンサルにおいて「信頼性ゼロ」。必ず記載する
- ドラフトに出典がない場合は「出典: [要確認]」等のプレースホルダーを入れる

### Step 2: 実装

**必ず先に `/mnt/skills/public/pptx/SKILL.md` → `pptxgenjs.md` を読んでから実装に入ること。**

---

## デザインシステム（厳守）

### カラー戦略

| 役割 | カラー | 用途 |
|------|--------|------|
| **Base** | `FFFFFF` (White) | 背景。余白を贅沢に使い、情報の密度とヌケ感を両立 |
| **Text** | `333333` (Charcoal Gray) | 本文テキスト。純黒は視覚疲労を防ぐため避ける |
| **Accent** | `5B0404`〜`8B0000` (Deep Red) | 結論・重要矢印・強調数値**のみ**に適用。乱用厳禁 |
| **Sub** | `F3F4F6` (Light Gray) | 背景ボックス・表の網掛け・区切り線 |

**カラー適用の鉄則:**
- Accent Color は「最も伝えたい1点」にのみ使う。複数箇所に散らさない
- グラフ使用時、強調したいデータポイントのみ Accent、それ以外は Sub（Light Gray）
- ユーザーがアクセントカラーを指定した場合はそれに従う

### タイポグラフィ

| 要素 | フォント | サイズ | スタイル |
|------|----------|--------|----------|
| **Tagline** | Yu Gothic UI / Hiragino Sans | 9-10pt | Regular, `999999` |
| **Action Title** | Yu Gothic UI / Hiragino Sans | 16-18pt | Bold, `333333` |
| **Section Header** | Yu Gothic UI / Hiragino Sans | 12-14pt | Bold, `333333` |
| **Body** | Yu Gothic UI / Hiragino Sans | 10-11pt | Regular, `333333` |
| **Footnote** | Yu Gothic UI / Hiragino Sans | 8-9pt | Regular, `999999` |
| **数値コールアウト** | Yu Gothic UI / Hiragino Sans | 28-36pt | Bold, Accent Color |

**視覚的優先順位:** 常に `Action Title > Header > Body > Footnote` の順。

### レイアウト原則

- **Alignment**: 全要素をグリッドに合わせる。1ピクセルのズレも許容しない
- **No Borders**: ボックスに枠線は引かない。色の塗り分けか余白で境界を表現
- **余白**: スライド端から最低 0.4" のマージン。要素間は 0.15"〜0.3" の余白
- **3Dグラフ厳禁**: すべてフラットデザイン
- **凡例はグラフ内に直接配置**: グラフ外の凡例は視線移動が増えるため禁止

---

## 図解パターン（Body Area）

ドラフトの論理構造に応じて、以下のパターンから最適なものを選択する：

### パターン1: 比較（Comparison）
```
左右対比表 or 2軸マトリクス
- 2〜3列の比較カラム
- ヘッダー行は Sub Color で網掛け
- 差異がある箇所のみ Accent Color
```

### パターン2: プロセス（Process / Chevron）
```
3〜5ステップの水平シェブロン
- 各ステップは矢印形状で連結
- 現在のステップ or 重要ステップのみ Accent Color
- 各ステップ下に補足テキスト
```
**実装:** pptxgenjs の `CHEVRON` シェイプ、または RECTANGLE + 三角形の組み合わせで表現。

### パターン3: 階層（Hierarchy / Logic Tree）
```
ピラミッド or ロジックツリー
- 上位概念 → 下位要素への分解
- MECE（漏れなくダブりなく）を視覚化
```

### パターン4: 数値コールアウト（KPI Highlight）
```
大きな数字（28-36pt, Accent Color）
+ 小さなラベル（9-10pt, Text Color）
+ 前年比・変化を示す矢印
```

### パターン5: 表形式（Structured Table）
```
- ヘッダー行: Sub Color 背景、Bold
- データ行: 交互に白/Sub Color（ゼブラストライプ）
- 枠線なし（色の塗り分けで境界を表現）
- 強調セルのみ Accent Color
```

**共通ルール:**
- 左→右、上→下の論理流動を徹底
- MECE を意識した分類・配置
- 情報の密度は「余白50%以上」を目安に

---

## pptxgenjs 実装テンプレート

```javascript
const pptxgen = require("pptxgenjs");

// === カラー定数 ===
const COLOR = {
  BASE:    "FFFFFF",
  TEXT:    "333333",
  ACCENT:  "5B0404",  // ユーザー指定があれば上書き
  SUB:     "F3F4F6",
  MUTED:   "999999",
  LINE:    "E5E7EB",
};

// === フォント設定 ===
const FONT = "Yu Gothic UI";  // Windows向け。Mac: "Hiragino Sans"

// === レイアウト定数 ===
const LAYOUT = {
  MARGIN_X: 0.45,        // 左右マージン
  MARGIN_TOP: 0.3,       // 上マージン
  TAGLINE_Y: 0.3,        // Tagline Y位置
  ACTION_TITLE_Y: 0.55,  // Action Title Y位置
  SEPARATOR_Y: 1.15,     // 区切り線 Y位置（Action Titleの行数で調整）
  BODY_Y: 1.35,          // Body開始 Y位置
  BODY_H: 3.6,           // Body高さ（Footnoteまでの空間）
  FOOTNOTE_Y: 5.15,      // Footnote Y位置
  CONTENT_W: 9.1,        // コンテンツ幅（10 - MARGIN_X*2）
};

let pres = new pptxgen();
pres.layout = "LAYOUT_16x9";

let slide = pres.addSlide();
slide.background = { color: COLOR.BASE };

// --- Layer 1: Tagline ---
slide.addText("テーマをここに", {
  x: LAYOUT.MARGIN_X, y: LAYOUT.TAGLINE_Y,
  w: LAYOUT.CONTENT_W, h: 0.25,
  fontSize: 9, fontFace: FONT, color: COLOR.MUTED,
  bold: false, margin: 0,
});

// --- Layer 2: Action Title ---
slide.addText("結論メッセージ（So what?）をここに記述する。\n主張を含む2〜3行のリードメッセージ。", {
  x: LAYOUT.MARGIN_X, y: LAYOUT.ACTION_TITLE_Y,
  w: LAYOUT.CONTENT_W, h: 0.55,
  fontSize: 16, fontFace: FONT, color: COLOR.TEXT,
  bold: true, margin: 0, valign: "top",
});

// --- 区切り線 ---
slide.addShape(pres.shapes.LINE, {
  x: LAYOUT.MARGIN_X, y: LAYOUT.SEPARATOR_Y,
  w: LAYOUT.CONTENT_W, h: 0,
  line: { color: COLOR.LINE, width: 1 },
});

// --- Layer 3: Body Area ---
// ここにドラフトの内容に応じた図解を配置
// （比較表、シェブロン、ロジックツリー、KPIコールアウト、テーブル等）

// --- Layer 4: Footnotes ---
slide.addText("出典: [データソース] | 注: [補足事項]", {
  x: LAYOUT.MARGIN_X, y: LAYOUT.FOOTNOTE_Y,
  w: LAYOUT.CONTENT_W, h: 0.3,
  fontSize: 8, fontFace: FONT, color: COLOR.MUTED,
  margin: 0, valign: "bottom",
});

pres.writeFile({ fileName: "/home/claude/output.pptx" });
```

---

## Body Area 実装例

### 比較表の実装

```javascript
// ヘッダー行
const headers = ["項目", "現状（As-Is）", "目標（To-Be）"];
const colW = [2.5, 3.3, 3.3];

headers.forEach((text, i) => {
  const xPos = LAYOUT.MARGIN_X + colW.slice(0, i).reduce((a, b) => a + b, 0);
  slide.addText(text, {
    x: xPos, y: LAYOUT.BODY_Y,
    w: colW[i], h: 0.35,
    fontSize: 10, fontFace: FONT, color: COLOR.TEXT,
    bold: true, fill: { color: COLOR.SUB },
    margin: [0, 0.1, 0, 0.1], valign: "middle",
  });
});

// データ行（ゼブラストライプ、枠線なし）
const rows = [
  ["売上", "100億円", "150億円"],
  ["利益率", "5%", "12%"],
];

rows.forEach((row, ri) => {
  const rowY = LAYOUT.BODY_Y + 0.35 + ri * 0.35;
  const bgColor = ri % 2 === 0 ? COLOR.BASE : COLOR.SUB;
  row.forEach((text, ci) => {
    const xPos = LAYOUT.MARGIN_X + colW.slice(0, ci).reduce((a, b) => a + b, 0);
    const isHighlight = ci === 2; // To-Be列を強調
    slide.addText(text, {
      x: xPos, y: rowY,
      w: colW[ci], h: 0.35,
      fontSize: 10, fontFace: FONT,
      color: isHighlight ? COLOR.ACCENT : COLOR.TEXT,
      bold: isHighlight,
      fill: { color: bgColor },
      margin: [0, 0.1, 0, 0.1], valign: "middle",
    });
  });
});
```

### シェブロン（プロセスフロー）の実装

```javascript
const steps = ["現状分析", "課題特定", "施策立案", "実行計画"];
const stepW = 2.0;
const stepH = 0.6;
const gap = 0.2;
const startX = LAYOUT.MARGIN_X + (LAYOUT.CONTENT_W - (steps.length * stepW + (steps.length - 1) * gap)) / 2;
const accentIdx = 2; // 強調するステップ

steps.forEach((text, i) => {
  const x = startX + i * (stepW + gap);
  const isAccent = i === accentIdx;

  // シェブロン背景（RECTANGLE + 右側に三角形で擬似矢印）
  slide.addShape(pres.shapes.RECTANGLE, {
    x: x, y: LAYOUT.BODY_Y + 0.5,
    w: stepW, h: stepH,
    fill: { color: isAccent ? COLOR.ACCENT : COLOR.SUB },
  });

  slide.addText(text, {
    x: x, y: LAYOUT.BODY_Y + 0.5,
    w: stepW, h: stepH,
    fontSize: 10, fontFace: FONT,
    color: isAccent ? COLOR.BASE : COLOR.TEXT,
    bold: true, align: "center", valign: "middle",
    margin: 0,
  });

  // ステップ番号
  slide.addText(`Step ${i + 1}`, {
    x: x, y: LAYOUT.BODY_Y + 0.5 + stepH + 0.05,
    w: stepW, h: 0.2,
    fontSize: 8, fontFace: FONT,
    color: COLOR.MUTED, align: "center", margin: 0,
  });
});
```

### マトリクス（2x2）の実装

```javascript
// 軸ラベルと4象限ラベルをドラフトの Accent 指定に従って配色
const quadrants = [
  { label: "Question Mark", x: LAYOUT.MARGIN_X, y: LAYOUT.BODY_Y, accent: false },
  { label: "Star",          x: LAYOUT.MARGIN_X + LAYOUT.CONTENT_W / 2, y: LAYOUT.BODY_Y, accent: true },
  { label: "Dog",           x: LAYOUT.MARGIN_X, y: LAYOUT.BODY_Y + LAYOUT.BODY_H / 2, accent: false },
  { label: "Cash Cow",      x: LAYOUT.MARGIN_X + LAYOUT.CONTENT_W / 2, y: LAYOUT.BODY_Y + LAYOUT.BODY_H / 2, accent: false },
];
const qW = LAYOUT.CONTENT_W / 2;
const qH = LAYOUT.BODY_H / 2;

quadrants.forEach(q => {
  slide.addShape(pres.shapes.RECTANGLE, {
    x: q.x, y: q.y, w: qW, h: qH,
    fill: { color: q.accent ? COLOR.ACCENT : COLOR.SUB },
    line: { color: COLOR.BASE, width: 2 },
  });
  slide.addText(q.label, {
    x: q.x, y: q.y, w: qW, h: qH,
    fontSize: 11, fontFace: FONT,
    color: q.accent ? COLOR.BASE : COLOR.TEXT,
    bold: q.accent, align: "center", valign: "middle", margin: 0,
  });
});
// X軸・Y軸ラベルは addText で外側に配置
```

### Before-After の実装

```javascript
const colW = (LAYOUT.CONTENT_W - 0.6) / 3; // Before / 矢印 / After
const bodyY = LAYOUT.BODY_Y;

// Before ヘッダー
slide.addText("Before", {
  x: LAYOUT.MARGIN_X, y: bodyY, w: colW, h: 0.35,
  fontSize: 11, fontFace: FONT, color: COLOR.TEXT,
  bold: true, fill: { color: COLOR.SUB }, align: "center", valign: "middle", margin: 0,
});
// After ヘッダー
slide.addText("After", {
  x: LAYOUT.MARGIN_X + colW + 0.6, y: bodyY, w: colW, h: 0.35,
  fontSize: 11, fontFace: FONT, color: COLOR.BASE,
  bold: true, fill: { color: COLOR.ACCENT }, align: "center", valign: "middle", margin: 0,
});

const rows = [
  { before: "手作業で目視比較（2日/件）", after: "AI自動比較（10分/件）", highlight: true },
  { before: "漏れ率 15%", after: "漏れ率 0.5%", highlight: false },
];
rows.forEach((row, ri) => {
  const rowY = bodyY + 0.35 + ri * 0.45;
  // Before
  slide.addText(row.before, {
    x: LAYOUT.MARGIN_X, y: rowY, w: colW, h: 0.4,
    fontSize: 10, fontFace: FONT, color: COLOR.TEXT,
    fill: { color: ri % 2 === 0 ? COLOR.BASE : COLOR.SUB }, margin: [0, 0.1, 0, 0.1], valign: "middle",
  });
  // 矢印
  slide.addText("→", {
    x: LAYOUT.MARGIN_X + colW, y: rowY, w: 0.6, h: 0.4,
    fontSize: 14, fontFace: FONT, color: row.highlight ? COLOR.ACCENT : COLOR.MUTED,
    align: "center", valign: "middle", bold: row.highlight, margin: 0,
  });
  // After
  slide.addText(row.after, {
    x: LAYOUT.MARGIN_X + colW + 0.6, y: rowY, w: colW, h: 0.4,
    fontSize: 10, fontFace: FONT,
    color: row.highlight ? COLOR.ACCENT : COLOR.TEXT,
    bold: row.highlight,
    fill: { color: ri % 2 === 0 ? COLOR.BASE : COLOR.SUB }, margin: [0, 0.1, 0, 0.1], valign: "middle",
  });
});
```

### KPI コールアウトの実装

```javascript
const kpis = [
  { value: "150億", label: "目標売上", sub: "+50% YoY" },
  { value: "12%", label: "営業利益率", sub: "+7pp" },
  { value: "3.2x", label: "ROI", sub: "業界平均 1.8x" },
];

const kpiW = LAYOUT.CONTENT_W / kpis.length;

kpis.forEach((kpi, i) => {
  const x = LAYOUT.MARGIN_X + i * kpiW;

  // 数値（大きく、Accent Color）
  slide.addText(kpi.value, {
    x: x, y: LAYOUT.BODY_Y + 0.8,
    w: kpiW, h: 0.6,
    fontSize: 32, fontFace: FONT, color: COLOR.ACCENT,
    bold: true, align: "center", valign: "bottom", margin: 0,
  });

  // ラベル
  slide.addText(kpi.label, {
    x: x, y: LAYOUT.BODY_Y + 1.4,
    w: kpiW, h: 0.3,
    fontSize: 10, fontFace: FONT, color: COLOR.TEXT,
    bold: true, align: "center", valign: "top", margin: 0,
  });

  // サブテキスト（変化率等）
  slide.addText(kpi.sub, {
    x: x, y: LAYOUT.BODY_Y + 1.7,
    w: kpiW, h: 0.25,
    fontSize: 9, fontFace: FONT, color: COLOR.MUTED,
    align: "center", valign: "top", margin: 0,
  });
});
```

### process-deep-dive・swimlane・architecture の実装方針

これら3タイプは情報量が多く、実装パターンが案件ごとに大きく異なる。以下の方針で実装する：

- **共通原則**: 左→右の情報流動、`/mnt/skills/public/pptx/SKILL.md` の pptxgenjs.md を参照しながら同じカラー・フォント・マージン定数を使用
- **process-deep-dive**: Bodyエリアを左右2分割。左にInput→Output（`SUB`背景の矩形）、右に技術手法・選定理由（テキストブロック）を配置。Accentで指定された強調ポイントにAccent Colorを適用
- **swimlane**: 水平帯（レーン）を縦に積み上げ、各レーン内にフローステップを矩形で並べる。レーン境界は `LINE` カラーの横線。ハンドオフポイントにAccent Color矢印
- **architecture**: レイヤーを水平帯として縦に積み上げ（上が上位層）。各帯内に構成要素を `SUB` 背景の小矩形で並べる。Accent指定のレイヤーのみ `ACCENT` 背景

---

## QA チェックリスト（コンサルスライド固有）

通常の pptx QA（`/mnt/skills/public/pptx/SKILL.md` の QA セクション参照）に加え、以下を確認：

### 構造チェック
- [ ] 4層構造（Tagline → Action Title → Body → Footnote）が揃っているか
- [ ] Action Title が「主張」を含んでいるか（単なる説明になっていないか）
- [ ] Footnote / 出典が記載されているか

### デザインチェック
- [ ] Accent Color の使用箇所は「最も伝えたい1点」に限定されているか
- [ ] 枠線（Border）を使っていないか → 色の塗り分けか余白で表現
- [ ] 純黒（#000000）を使っていないか → `333333` を使用
- [ ] 3Dグラフを使っていないか → フラットデザインのみ
- [ ] 全要素がグリッド整列しているか

### 論理チェック
- [ ] 左→右 or 上→下の論理流動が一貫しているか
- [ ] MECE（漏れなくダブりなく）が視覚的に表現されているか
- [ ] グラフの凡例がグラフ内に直接配置されているか（外部凡例は禁止）

---

## 依存関係

このスキルは `/mnt/skills/public/pptx/SKILL.md` に依存する。
実装前に必ず pptx スキルの `pptxgenjs.md` を読み、以下をインストールすること：

```bash
npm install -g pptxgenjs
```

日本語フォント（Yu Gothic UI）は環境に依存する。フォントが利用できない場合は `Calibri` にフォールバックする。
