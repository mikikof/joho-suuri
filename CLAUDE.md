# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## プロジェクト概要

平成国際大学 情報デザイン学部「情報数理入門」の講義スライド集（monorepo）。各回は方程式・不等式・連立方程式・指数関数・三角関数などを、スライダー操作 + Chart.js / 素の Canvas でインタラクティブに見せる、単一ページの教材。本文・UI 文言はすべて日本語。

**参照モデル: `lec05/index.html`** (最新の規約と構成を反映)。 lec02-04 は過渡期の素材として参照する。

## ディレクトリ構成

```
/                     ルート目次ページ（各回へのリンク）
  index.html
  lec02/              第2回 — 式で世界を動かす
  lec03/              第3回 — 図形と方程式
  lec04/              第4回 — 1 次関数と 2 次関数
  lec05/              第5回 — 周期と、波 (★ 参照モデル)
    index.html        対話型 web スライド (本体)
    情報数理入門_第N回.pdf    配布/印刷用スライド (将来 pptx に置き換え予定)
    情報数理入門_第N回.xlsx   演習ワークブック
  lecNN/              （今後の回。同じ構造で追加）
```

各回は完全に独立した `lecNN/index.html` 1 枚で完結する（共通モジュールは持たない）。新しい回を作る時は **`lec05/index.html` をコピーして開始**するのが最短。ルートの `index.html` は単なる目次なので、回を追加したら `.lec-list` にカードを 1 つ足す。

## 新しい回を作る時のルール — 必ず守る

**この順番・段階で進める。同時並行や順序入れ替えは禁止。** 詳細は `.claude/skills/new-lecture/SKILL.md` / `TEMPLATE_GUIDE.md` / `EXCEL_GUIDE.md` を参照。

1. **HTML (`lecNN/index.html`) を最初に作る** — テーマと参考資料 (web/画像/URL) を取り込み、 **`lec05/index.html` をコピーしてベース**にする。 これが講義の本体・真実の源 (source of truth)
   - **冒頭の「これまでの振り返り」 スライド (slide-2) は必須** (各回 N ≥ 2)。 `lec-review-slide` skill が担当
2. **HTML 完成直後・ユーザー確認の前に sub-skill 2 つを必ず通す**:
   - **`lec-voice-audit`** — AI 臭・大主語+抽象動詞メタファー・スローガン調・二重修飾の検出と修正
   - **`lec-content-check`** — 数値・概念・用語統一・SUMMARY パラメータ漏れの検出と修正
3. **ユーザー確認** — sub-skill 修正後に「OK か / 修正点があるか」を聞く。承認なしに次へ進まない
4. **Excel (`lecNN/情報数理入門_第N回.xlsx`) を作る** — HTML の内容と必ずタイアップさせる (PART 構成・お題・数式を一致させる)。HTML の例題より一段レベルアップした演習問題を含める
5. **再度ユーザー確認** — Excel 完成後も停止し、確認をもらう
6. **PPTX (`lecNN/情報数理入門_第N回.pptx`) は要求があった時のみ作る** — ユーザーから「pptx も作って」など明示的に頼まれない限り着手しない。聞かれてないのに作らない

参考資料の取り込み方:
- 参考 URL → WebFetch / WebSearch
- 既存 PDF / 画像 / pptx → Read
- 教科書・シラバス指定 → ユーザーに確認

### sub-skill 一覧 (`.claude/skills/` 配下)

| skill | 役割 | 呼び出しタイミング |
|---|---|---|
| `new-lecture` | hub orchestrator。 全体フロー (Phase 1-3) | 新しい回を作るとき |
| `lec-review-slide` | 冒頭 slide-2「これまでの振り返り」 を生成 | Phase 1 HTML 構築中 |
| `lec-voice-audit` | voice 自己審査 (AI 臭の検出) | Phase 1 完了直後・ユーザー確認の前 |
| `lec-content-check` | 内容正確性チェック (数値・概念) | Phase 1 完了直後・ユーザー確認の前 |
| `examplus` | インタラクティブな事例追加 | 必要に応じて |
| `brushup` / `visual` | リッチデザイン / ビジュアル一点突破 | 必要に応じて |

## スライド構成の方針 (lec03 で確立 + lec05 で拡張)

- **「身近な現象 → 数式 → 金融への展開 → AI/データサイエンスへの締め」** が骨格。各 PART で「身の回りのもの」を入口に置き、「同じ式は金融でも使える」へ繋ぎ、最後の PART で「同じ式は AI でも動いている」で締める
- **冒頭に「これまでの振り返り」 スライド (slide-2) を必ず入れる** (lec05 で必須化) — 前回までに学んだ主要グラフ/概念を 3 カードで思い出させる。 詳細は `lec-review-slide` skill 参照
- **メインの PART を 1-1 / 1-2 に分割するパターンが効く** — 1-1 で身の回りの現象（地図、教室、Wi-Fi 等）で概念を導入、1-2 でその発想を金融題材（リスクリターン散布図等）に転用する。1 つの式の射程が広いことを体感させる
- **最後の PART は AI / データサイエンスのリッチなインタラクティブで締める** — 距離 → 似ている度 のような形で、Spotify 風カードグリッド、SVG 顔認証、 Lissajous 図形など 2-3 スライド分のリッチ実装。座標にこだわりすぎず、シミュレーションとしての面白さを優先する
- **(オプション) 最終 PART に「提出課題」 セクション (`submit-box`) を入れる** — Gemini と協働して Canvas/HTML 作品を制作する課題 (lec05 PART 5 で導入)
- **SUMMARY は FLOW 図 + 3 つの統一原理 (`principle-card` × 3) で締める**
- **NEXT スライド（次回予告）は作らない** — SUMMARY で締める。次回の内容を縛るとフローが固くなるので不要

## voice ルール (既存メモリ参照)

講義スライドの voice は以下のメモリに従う。 `lec-voice-audit` skill が自動チェック:

- [feedback_no_ai_tone_in_lectures](../../../../../.claude/projects/-Users-mikiokofune-my-company/memory/feedback_no_ai_tone_in_lectures.md): AI 臭・スローガン調・大主語+抽象動詞メタファー (「世界を書く」 等) の禁止
- [feedback_natural_spoken_japanese_for_lectures](../../../../../.claude/projects/-Users-mikiokofune-my-company/memory/feedback_natural_spoken_japanese_for_lectures.md): 命令形/体言止め/「君ら+演出動詞」 NG、 「皆さん」+ 中立動詞・宣言文へ
- [feedback_column_abstract_verbs](../../../../../.claude/projects/-Users-mikiokofune-my-company/memory/feedback_column_abstract_verbs.md): 「立ち上がる」 等の抽象動詞メタファー禁止

## 内容正確性チェック (lec05 で確立)

`lec-content-check` skill が自動チェック:

- **数値ミス**: 例題の答え、 統計値、 物理量を手計算で検算
- **概念混同**: 「中心 C ≠ データの算術平均」 など (lec05 で実例: sin フィット中心 16.1℃ vs 年間平均 15.8℃)
- **パラメータ漏れ**: SUMMARY の「○つで記述できる」 が本文の式と一致するか (lec05 で実例: 「振幅 A・周期 T・位相 φ の 3 つ」 と書いたが、 中心 C を含めて 4 つが正しい)
- **用語統一**: PART 4「重ね合わせ」 vs SUMMARY「足し合わせ」 のような表記揺れ
- **語りの整合**: 「観覧車のときと同じ 3 つ」 と書いたら、 実際に同じ 3 つか確認

## Google スプレッドシート前提の表記方針

ターゲット環境は Google スプレッドシート（Excel ではない）。HTML の Excel-box 内・Excel 演習シートの操作手順は Sheets の UI 用語で書く。

- **メニュー [挿入] → [グラフ]** （Sheets はデフォルトで縦棒グラフ — 散布図に変更する手順を明示）
- **散布図はデータ範囲を「数値列だけ」で選ぶ**。名前列を含めると X 軸が文字列カテゴリになって座標が再現できない
- **散布図に線を引く** = グラフをダブルクリック → [カスタマイズ] → [系列] → 「データポイントを線で結ぶ」にチェック（Excel の「散布図（直線あり）」の置き換え）
- **軸の最小最大を固定** = ダブルクリック → [カスタマイズ] → [横軸/縦軸] → 最小値・最大値
- **別の系列を追加** = ダブルクリック → [設定] タブ → 「系列を追加」（Excel の「右クリック → データの選択」の置き換え）
- **ドラッグの起点** = セル右下の **フィルハンドル**（青い四角）
- **フィルター** = メニュー [データ] → [フィルタを作成]

## ビルドと実行

ビルド工程・依存関係マネージャ・テストランナーは無い。`index.html`（目次）または `lecNN/index.html`（各回）をブラウザで直接開けば動作する（macOS なら `open index.html`）。Chart.js は CDN（`cdn.jsdelivr.net/npm/chart.js@4.4.1`）から読み込み、Google Fonts も同様に外部読み込み。オフラインで開いた場合はグラフとフォントが効かない点に注意。

## アーキテクチャ（各回の `lecNN/index.html`）

ファイルは `lecNN/index.html` 1 枚に HTML / CSS / JS をすべて埋め込む構成。

**レイアウト:** `.shell` グリッドが `aside.sidebar`（左固定ナビ）と `main.main`（本編）の2カラム構成を作る。サイドバーのアンカーリンクは `#part1`〜`#part5` 等のセクション ID にスクロール。`#overview` の `.tocbar` も同じアンカー集合を指す。

**セクション構成:** lec02 は「PART 1 複利 / ASIDE 0.1+0.2 浮動小数点 / PART 2 1次方程式 / PART 3 1次不等式 / PART 4 連立方程式 / PART 5 指数爆発 / SUMMARY / NEXT」。lec03 以降は **NEXT を廃止**し、最終 PART を「AI / データサイエンスのリッチなインタラクティブ」に充てている。各 PART は「身近なお題 → スライダー → 数式表示 → グラフ → 解釈テキスト」の同じ流れを踏む。

**スクリプト構成:** ページ末尾の単一 `<script>` 内に、各 PART ごと独立した IIFE（`(() => { ... })();`）を並べる。グローバルに置くのは `fmtYen` / `fmtMan`（金額フォーマッタ）と Chart.js のデフォルト設定のみ。各 IIFE は自分の DOM ID を `getElementById` で掴み、`input` イベントで `update()` を呼ぶ ＋ 初期化時に一度 `update()` を呼んで描画する、というパターン。

**ID 命名規約:** PART N に属する DOM 要素は `cN-*` プレフィックスを付ける（例：PART 1 のスライダーは `c1-p` `c1-r` `c1-y`、表示値は `c1-p-val`、統計値は `c1-stat-*`、Canvas は `chart1`）。新しい入力やラベルを追加する時はこの規約に揃える。IIFE はこのプレフィックスで分離されているため、別 PART の ID と衝突させない限り独立して編集できる。

**スタイリング:** `<style>` 冒頭の `:root` に CSS 変数（カラーパレット `--blue/--ink/--surface*` 等、`--radius-*`、`--shadow-*`、`--font-jp/serif/mono`）を集約。新しい色や半径を導入する前に既存変数で表現できないか確認する。クラス命名は `.block` `.controls` `.control-row` `.stat` `.stat-row` 等の汎用パーツが PART 間で共有されているため、見た目を変える時は全 PART への波及を意識する。

**Chart.js の使い方:** 折れ線は `type: 'line'`、座標プロット系は `type: 'scatter'`、`responsive: true`。高さは原則 `maintainAspectRatio: false` + 親 div の CSS で決めるが、**円・領域など縦横比が崩れると意味が変わるグラフは `maintainAspectRatio: true, aspectRatio: 1`** で canvas を正方形に固定する（lec03 PART 3 / PART 4）。ツールチップは濃灰背景（`#1F2937`）、Y 軸 ticks は金額なら `'¥' + fmtYen.format(v)`。PART 3 / PART 4 はカスタムプラグイン（`currentMarker`、`intersectionPlugin`、`labelPlugin`、`regionPlugin` 等）でグラフ上に補助線・交点マーカー・領域・ラベルを直接描画している。

**円を描くときの注意（lec03 で確立）:** scatter の dataset を 2 つ（上半・下半）に分けて `fill: '+1'` でつなぐと、x の連続性が崩れて塗りつぶしが暴れる。**1 周分の閉じた点列**（`{x: A + R*cos(t), y: B + R*sin(t)}` を `t` を 0 → 2π で N+1 点）を 1 dataset に入れて `showLine: true, fill: true` で描く。

**チェックボックスでの「領域モード」パターン（lec03 PART 1-2）:** スライダーで動かす自分の点に対し、「許容範囲」（軸並行の薄い長方形 / 円）をオプションで重ねるチェックボックス UI が、座標 → 領域 の橋渡しに有効。カスタムプラグインで `beforeDatasetsDraw` 時に半透明矩形を描画。
