# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## プロジェクト概要

平成国際大学 情報デザイン学部「情報数理入門」の講義スライド集（monorepo）。各回は方程式・不等式・連立方程式・指数関数などを、スライダー操作 + Chart.js のグラフでインタラクティブに見せる、単一ページの教材。本文・UI 文言はすべて日本語。

## ディレクトリ構成

```
/                     ルート目次ページ（各回へのリンク）
  index.html
  lec02/              第2回 — 式で世界を動かす
    index.html        対話型 web スライド (本体)
    情報数理入門_第2回.pdf    配布/印刷用スライド (将来 pptx に置き換え予定)
    情報数理入門_第2回.xlsx   演習ワークブック
  lecNN/              （今後の回。同じ構造で追加）
```

各回は完全に独立した `lecNN/index.html` 1 枚で完結する（共通モジュールは持たない）。新しい回を作る時は既存の `lec02/index.html` をコピーして開始するのが最短。ルートの `index.html` は単なる目次なので、回を追加したら `.lec-list` にカードを 1 つ足す。

## 新しい回を作る時のルール — 必ず守る

**この順番・段階で進める。同時並行や順序入れ替えは禁止。** 詳細は `.claude/skills/new-lecture/SKILL.md` を参照。

1. **HTML (`lecNN/index.html`) を最初に作る** — テーマと参考資料 (web/画像/URL) を取り込み、`lec02/index.html` をコピーしてベースにする。これが講義の本体・真実の源 (source of truth)
2. **ユーザー確認** — HTML が完成したら必ず停止し、ユーザーに「OK か / 修正点があるか」を聞く。承認なしに次へ進まない
3. **Excel (`lecNN/情報数理入門_第N回.xlsx`) を作る** — HTML の内容と必ずタイアップさせる (PART 構成・お題・数式を一致させる)。HTML の例題より一段レベルアップした演習問題を含める
4. **再度ユーザー確認** — Excel 完成後も停止し、確認をもらう
5. **PPTX (`lecNN/情報数理入門_第N回.pptx`) は要求があった時のみ作る** — ユーザーから「pptx も作って」など明示的に頼まれない限り着手しない。聞かれてないのに作らない

参考資料の取り込み方:
- 参考 URL → WebFetch / WebSearch
- 既存 PDF / 画像 / pptx → Read
- 教科書・シラバス指定 → ユーザーに確認

## ビルドと実行

ビルド工程・依存関係マネージャ・テストランナーは無い。`index.html`（目次）または `lecNN/index.html`（各回）をブラウザで直接開けば動作する（macOS なら `open index.html`）。Chart.js は CDN（`cdn.jsdelivr.net/npm/chart.js@4.4.1`）から読み込み、Google Fonts も同様に外部読み込み。オフラインで開いた場合はグラフとフォントが効かない点に注意。

## アーキテクチャ（各回の `lecNN/index.html`）

ファイルは `lecNN/index.html` 1 枚に HTML / CSS / JS をすべて埋め込む構成。

**レイアウト:** `.shell` グリッドが `aside.sidebar`（左固定ナビ）と `main.main`（本編）の2カラム構成を作る。サイドバーのアンカーリンクは `#part1`〜`#part5` 等のセクション ID にスクロール。`#overview` の `.tocbar` も同じアンカー集合を指す。

**セクション構成:** PART 1 複利 / ASIDE 0.1+0.2 浮動小数点 / PART 2 1次方程式 / PART 3 1次不等式 / PART 4 連立方程式 / PART 5 指数爆発 / SUMMARY / NEXT。各 PART は「身近なお題（バイト代・料金プラン等）→ スライダー → 数式表示 → グラフ → 解釈テキスト」の同じ流れを踏む。

**スクリプト構成:** ページ末尾の単一 `<script>` 内に、各 PART ごと独立した IIFE（`(() => { ... })();`）を並べる。グローバルに置くのは `fmtYen` / `fmtMan`（金額フォーマッタ）と Chart.js のデフォルト設定のみ。各 IIFE は自分の DOM ID を `getElementById` で掴み、`input` イベントで `update()` を呼ぶ ＋ 初期化時に一度 `update()` を呼んで描画する、というパターン。

**ID 命名規約:** PART N に属する DOM 要素は `cN-*` プレフィックスを付ける（例：PART 1 のスライダーは `c1-p` `c1-r` `c1-y`、表示値は `c1-p-val`、統計値は `c1-stat-*`、Canvas は `chart1`）。新しい入力やラベルを追加する時はこの規約に揃える。IIFE はこのプレフィックスで分離されているため、別 PART の ID と衝突させない限り独立して編集できる。

**スタイリング:** `<style>` 冒頭の `:root` に CSS 変数（カラーパレット `--blue/--ink/--surface*` 等、`--radius-*`、`--shadow-*`、`--font-jp/serif/mono`）を集約。新しい色や半径を導入する前に既存変数で表現できないか確認する。クラス命名は `.block` `.controls` `.control-row` `.stat` `.stat-row` 等の汎用パーツが PART 間で共有されているため、見た目を変える時は全 PART への波及を意識する。

**Chart.js の使い方:** すべて `type: 'line'`、`responsive: true, maintainAspectRatio: false`（高さは親 div の CSS で決める）、ツールチップは濃灰背景（`#1F2937`）、Y 軸 ticks は `'¥' + fmtYen.format(v)`。PART 3 / PART 4 はカスタムプラグイン（`currentMarker`、`intersectionPlugin`）でグラフ上に補助線・交点マーカーを直接描画している。
