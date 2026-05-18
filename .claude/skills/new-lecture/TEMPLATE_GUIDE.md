# 情報数理入門 — 講義スライド規約集

**参照モデル: `lec05/index.html`** (lec02-04 の流れを踏まえた最新版)。新しい回もこの規約に厳密に従う。違反するとサイドバー / 目次 / 既存スタイルの一貫性が崩れる。

旧版 (lec02-04) との主な違い:
- **冒頭に「これまでの振り返り」スライドを必須化** (前回までの主要グラフ 3 枚をカード化)
- 補助 UI として **`gemini-box`** (Gemini を使った教員向け補助説明)、**`submit-box`** (Gemini 連携の提出課題) を導入
- Canvas 描画は **`ensureSize` 高 DPI 対応**を必ず通す
- スライド間移動を `lec0N-slide-change` カスタムイベントで通知 (Web Audio 自動停止など)

---

## ファイル構成

各回は `lecNN/` 配下に独立した 3 ファイル:

```
lecNN/
  index.html                       # 対話型 web スライド (この規約の対象)
  情報数理入門_第N回.pptx          # 配布/印刷用 PowerPoint
  情報数理入門_第N回.xlsx          # 演習ワークブック
```

`index.html` は HTML / CSS / JS を 1 枚に埋め込む構成。共通モジュールは作らず、各回が独立。

---

## 外部依存 (CDN)

```html
<link href="https://fonts.googleapis.com/css2?family=Zen+Kaku+Gothic+New:wght@400;500;700;900&family=DM+Serif+Display&family=JetBrains+Mono:wght@400;500;700&display=swap" rel="stylesheet">
<script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.1/dist/chart.umd.min.js"></script>
```

**バージョンは固定**。Chart.js を上げる時は全回まとめて。

---

## :root CSS 変数 (必須・改変禁止)

新しい色や半径を導入する前に既存変数で表現できないか確認。

```css
:root {
  --bg: #FAFBFC;
  --surface: #FFFFFF;
  --surface-2: #F6F7F9;
  --surface-3: #EFF1F4;
  --ink: #1F2937;
  --ink-2: #4B5563;
  --ink-3: #9CA3AF;
  --line: #E5E7EB;
  --line-strong: #D1D5DB;
  --blue: #2563EB;
  --blue-dark: #1D4ED8;
  --blue-deep: #1E3A8A;
  --blue-bg: #EFF6FF;
  --blue-bg-2: #DBEAFE;
  --sky: #93C5FD;
  --amber: #F59E0B;
  --rose: #E11D48;
  --emerald: #059669;
  --purple: #7C3AED;
  --purple-bg: #F5F3FF;
  --radius-sm: 6px;
  --radius-md: 10px;
  --radius-lg: 16px;
  --shadow-sm: 0 1px 2px rgba(15, 23, 42, 0.04), 0 1px 3px rgba(15, 23, 42, 0.06);
  --shadow: 0 1px 2px rgba(15, 23, 42, 0.04), 0 4px 12px rgba(15, 23, 42, 0.06);
  --shadow-lg: 0 4px 6px rgba(15, 23, 42, 0.05), 0 20px 40px rgba(15, 23, 42, 0.08);
  --font-jp: "Zen Kaku Gothic New", sans-serif;
  --font-serif: "DM Serif Display", "Zen Kaku Gothic New", serif;
  --font-mono: "JetBrains Mono", ui-monospace, monospace;
}
```

---

## レイアウト

```html
<div class="shell">
  <aside class="sidebar"> ... ナビ ... </aside>
  <main class="main">
    <div class="slide-deck">
      <section class="slide active" id="part1"> ... </section>
      <section class="slide" id="part2"> ... </section>
      ...
    </div>
  </main>
</div>
```

- `.shell` は 280px サイドバー + 1fr メインの 2 カラム grid
- `.slide` は `position: absolute; inset: 0` で重ね、`.active` だけ `opacity: 1; visibility: visible`
- 切り替えはサイドバーの `.nav-link` クリックで `data-target` に対応する `.slide` を `active` にする JS

---

## ID 命名規約 (PART 別 IIFE と整合させる)

PART N に属する DOM 要素はすべて `cN-*` プレフィックス:

| 要素 | パターン | 例 (PART 1) |
|---|---|---|
| スライダー | `cN-{key}` | `c1-p`, `c1-r`, `c1-y` |
| 表示値 | `cN-{key}-val` | `c1-p-val` |
| 統計値 | `cN-stat-{key}` | `c1-stat-final` |
| Canvas | `chartN` | `chart1` |

**別 PART の ID と衝突させない**こと。新規入力やラベルを追加する時もこの規約に揃える。

---

## スクリプト構成 — PART 単位の IIFE

ページ末尾の単一 `<script>` 内に PART ごと独立した IIFE を並べる:

```js
(() => {
  const p = document.getElementById('cN-p');
  const pVal = document.getElementById('cN-p-val');
  const ctx = document.getElementById('chartN').getContext('2d');

  const chart = new Chart(ctx, { /* ... */ });

  function update() {
    pVal.textContent = p.value;
    chart.data.datasets[0].data = /* ... */;
    chart.update();
  }

  p.addEventListener('input', update);
  update();  // 初期描画
})();
```

グローバルに置いてよいのは:
- `fmtYen`, `fmtMan` (金額フォーマッタ)
- Chart.js のデフォルト設定 (`Chart.defaults.font.family` など)

---

## 共通 PART 構造 (5 ステップ)

各 PART は同じ流れを踏むこと:

1. **身近なお題** (例: バイト代、料金プラン、ガチャ)
2. **スライダー** (`<input type="range">`、`.controls .control-row` 内)
3. **数式表示** (`<div class="formula">` 内、和文 + 数式)
4. **Chart.js グラフ** (`<canvas>` を `<div class="chart-wrap">` で包む、高さは親 div の CSS で固定)
5. **解釈テキスト** (`<div class="interpret">` 等で、現在のスライダー値の意味を言語化)

---

## Chart.js 規約

```js
new Chart(ctx, {
  type: 'line',                  // 折れ線
  // type: 'scatter',            // 座標プロット（lec03 の PART 1, 2, 4, 5）
  data: { /* ... */ },
  options: {
    responsive: true,
    maintainAspectRatio: false,  // 高さは親 div の CSS で（折れ線・棒・通常の散布図）
    // maintainAspectRatio: true, aspectRatio: 1,  // 円・領域は正方形（後述）
    plugins: {
      tooltip: {
        backgroundColor: '#1F2937',
        titleFont: { family: 'Zen Kaku Gothic New' },
        bodyFont: { family: 'Zen Kaku Gothic New' },
      }
    },
    scales: {
      y: {
        ticks: {
          callback: v => '¥' + fmtYen.format(v)  // 金額の場合
        }
      }
    }
  }
});
```

PART 3 / 4 のように補助線・交点マーカーを描きたい時は **カスタムプラグイン** を定義 (`currentMarker`, `intersectionPlugin`, `labelPlugin`, `regionPlugin` 等を参考)。

### 円・領域は aspectRatio: 1 で正方形に固定

円や領域判定など、**縦横比が崩れると意味が変わる**グラフは canvas を正方形に固定する:

```js
options: {
  responsive: true,
  maintainAspectRatio: true,
  aspectRatio: 1,
  scales: {
    x: { min: -10, max: 10 },
    y: { min: -10, max: 10 },  // x と y の数値範囲も等しく
  }
}
```

加えて HTML 側の `.chart-wrap` を `max-width: 520px; margin: 0 auto;` 等で幅を絞ると、画面が広いときも楕円化しない。

### 円を描くときは「閉じた点列」で 1 dataset

scatter の dataset を 2 つ（上半・下半）に分けて `fill: '+1'` でつなぐと、塗りつぶしが暴れる（lec03 で発覚）。**円周を 1 周分の閉じた点列**として 1 dataset に入れて描く:

```js
const ring = [];
for (let i = 0; i <= N; i++) {
  const t = (i / N) * 2 * Math.PI;
  ring.push({ x: A + R * Math.cos(t), y: B + R * Math.sin(t) });
}
// dataset: { data: ring, showLine: true, fill: true, ... }
```

### 領域モード（チェックボックスでオン/オフ）

スライダーで動かす自分の点に「許容範囲」を重ねるオプションが、座標 → 領域 の橋渡しに有効（lec03 PART 1-2）。実装は `beforeDatasetsDraw` のカスタムプラグインで半透明矩形/円を描画:

```js
const regionPlugin = {
  id: 'cNRegion',
  beforeDatasetsDraw(chart) {
    if (!regionToggle.checked) return;
    // 自分の点 (X, Y) を中心に ±DX, ±DY の長方形を半透明で塗る
    const left = chart.scales.x.getPixelForValue(X - DX);
    const right = chart.scales.x.getPixelForValue(X + DX);
    const top = chart.scales.y.getPixelForValue(Y + DY);
    const bottom = chart.scales.y.getPixelForValue(Y - DY);
    chart.ctx.fillStyle = 'rgba(225, 29, 72, 0.10)';
    chart.ctx.fillRect(left, top, right - left, bottom - top);
  }
};
```

---

## HTML 内の Excel-box は Google スプレッドシート前提で書く

各 PART の `<div class="excel-box">` は「**スプレッドシートで再現してみる**」と表示し、Google スプレッドシートの UI 用語で書く（Excel ではない）:

| ✗ Excel 寄り | ✓ Sheets 寄り |
|---|---|
| 散布図（直線あり） | 散布図 → ダブルクリック → [カスタマイズ] → [系列] → 「データポイントを線で結ぶ」にチェック |
| グラフ右クリック → データの選択 → 系列追加 | グラフをダブルクリック → [設定] → 「系列を追加」 |
| グラフの軸の最小最大を固定 | ダブルクリック → [カスタマイズ] → [横軸/縦軸] → 最小値・最大値 |
| セル右下の■をドラッグ | セル右下の **フィルハンドル**（青い四角）をドラッグ |
| フィルター | メニュー [データ] → [フィルタを作成] |

**散布図で「座標」を再現するときは、選択範囲を数値列だけ**にする（名前列を含めると X 軸が文字列カテゴリになって座標として機能しない）。

## 共通汎用クラス (PART 間で共有)

これらを書き換えると全 PART に波及するので注意:

- `.block` — セクションのカード枠
- `.controls` — スライダー群のコンテナ
- `.control-row` — スライダー 1 本 + ラベル + 表示値
- `.stat` — 統計値カード
- `.stat-row` — 統計値の横並び

---

## 過去の回の構成 — 参考

### lec02 (24 スライド)

```
PART 1   複利            お題: 100万円を r% で n 年運用
ASIDE    浮動小数点誤差   小話
PART 2   1次方程式        お題: バイト代の損益分岐
PART 3   1次不等式        お題: 料金プラン比較
PART 4   連立方程式       お題: 2 商品の最適購入数
PART 5   指数爆発         お題: 30 日チャレンジ (倍々)
SUMMARY  まとめ
NEXT     次回予告
```

### lec03 (23 スライド) — 推奨パターン

```
イントロ 3 枚    表紙 / 全体像 / ティーザー
PART 1-1         身近な現象（地図のお店）で「座標」を導入
PART 1-2         同じ発想で金融題材（リスクリターン散布図）
PART 2           概念の主軸（距離 = 三平方の定理）
PART 3           発展（円 = 等距離の点）
PART 4           発展（領域 = 不等号で内・外）
PART 5 GATE      AI/データサイエンスへの締め PART の扉
PART 5-A         シンプル例（映画レコメンド散布図）
PART 5-B         リッチ例 1（Spotify 風カードグリッド + リアルタイム並び替え）
PART 5-C         リッチ例 2（SVG パラメトリック顔の認証シミュレーション）
SUMMARY          3 つの統一原理で締め
                 ＊ NEXT は廃止
```

### 新しい回を作るときの骨格

- **「身近な現象 → 数式 → 金融への展開 → AI/データサイエンスへの締め」** が骨格
- **メインの PART を 1-1 / 1-2 に分割**（身近 → 金融）するパターンが効く
- **最後の PART は AI で締める** — 距離 / 確率 / 関数など概念の汎用性を、リッチなインタラクティブ（カードグリッド、SVG パラメトリック描画）2-3 枚で示す
- 各 PART は「**身近なお題 → 抽象化 → グラフで体感 → 解釈**」のループ
- **NEXT スライドは作らない**。SUMMARY で締める

## リッチなインタラクティブのパターン (lec03 で確立)

最後の PART で「AI/DS への接続」を見せるとき、座標散布図だけでは弱いので、以下のパターンを使い分け。

### カードグリッド + リアルタイム並び替え (Spotify 風)

- N 個のアイテム（曲、映画、商品）を CSS Grid で並べる
- 各アイテムに複数の特徴量を持たせ、ユーザーの 2-3 軸スライダーから距離を計算
- 距離が近い順に CSS の `order` で並び替え
- 上位 3 件にバッジ・縁取り・グロー
- ダーク背景 + ゴールド系アクセントで「メディアアプリらしさ」を出す

### SVG パラメトリック描画 (顔認証風)

- 4-5 個のスライダーで特徴量を直接動かす
- スライダー値に応じて SVG（楕円、円、パス）を再描画
- 登録済み N 人と多次元距離を計算 → 上位 3 件をハイライト
- しきい値で「認証 OK / NG」を判定する UI を添える

### 共通設計

- 通常の Chart.js グラフ（散布図）と並べて使うことで、「同じ式でも見せ方が変わると印象が変わる」を体感させる
- 「**実物の◯◯（Spotify, 顔認証）は数十〜数百次元を使うが、距離の式は今日と同じ**」という締めの一文を必ず入れる

---

## 冒頭の「これまでの振り返り」スライド (lec05 で必須化)

第 N 回 (N ≥ 2) では、 **表紙の直後 (slide-2 位置)** に「これまで学んだ主要グラフ／概念を 3 カードで思い出す」 スライドを必ず入れる。 詳細は **`lec-review-slide` skill** を参照。

最低限の規約:
- 3 カード横並び (`<div class="review-grid">`)
- 各カードは: ミニ SVG グラフ + 出典タグ + カテゴリ名 + 代表例 + 「ほかの例を見る ▼」 ボタン + アコーディオン
- アコーディオンの開閉は `[aria-expanded]` 属性 + `.rev-extra.open` クラスで制御
- カードの上ボーダー色で 3 種類を識別 (青/橙/赤 等、 回のテーマに合わせる)
- レスポンシブ: 1100px 以下で 1 カラムに

```html
<section class="slide" id="slide-2" data-title="これまでの振り返り">
  <div class="slide-inner">
    <div class="eyebrow">lec02 〜 lec0(N-1) を振り返る</div>
    <h2>身の回りの現象を、<br>3 種類のグラフで表してきた。</h2>
    <p class="section-sub">...</p>
    <div class="review-grid">
      <div class="rev-card rev-line"> ... </div>
      <div class="rev-card rev-quad"> ... </div>
      <div class="rev-card rev-exp">  ... </div>
    </div>
  </div>
</section>
```

JS:
```js
(() => {
  document.querySelectorAll('#slide-2 .rev-toggle').forEach((btn) => {
    btn.addEventListener('click', () => {
      const panel = document.getElementById(btn.dataset.target);
      if (!panel) return;
      const open = panel.classList.toggle('open');
      btn.setAttribute('aria-expanded', open ? 'true' : 'false');
      const label = btn.querySelector('.rev-toggle-label');
      if (label) label.textContent = open ? '閉じる' : 'ほかの例を見る';
    });
  });
})();
```

---

## `gemini-box` — Gemini を使った教員向け補助説明 (lec05 で導入)

「学生がここで詰まりやすい / 静止画では理解しにくい」 ポイントで、 教員が Gemini に投げると視覚化が助かるプロンプトを置く枠。

```html
<div class="gemini-box">
  <div class="gb-head">Gemini を補助に使う ｜ {見出し}</div>
  <div class="gb-title">{使い所の一文}</div>
  <div class="gb-body">{なぜこれが効くかの説明}</div>
  <div class="gb-prompt">{Gemini に投げるプロンプト案}</div>
</div>
```

CSS (`--amber` 系の左ボーダー + 暖色の背景) は lec05 を踏襲。

---

## `submit-box` — Gemini 連携の提出課題 (lec05 で導入)

最終 PART (Lissajous / 提出課題系) で、 学生が Gemini と協働して制作 → HTML 提出するセクション。

```html
<div class="submit-box">
  <div class="sb-head">課題 ／ Lec0N ASSIGNMENT</div>
  <div class="sb-title">{課題タイトル}</div>
  <div class="sb-body">{課題の本文}</div>
  <div class="sb-steps">
    <div class="sb-step"><div class="sn">1</div><div class="desc">テーマを決める</div></div>
    <div class="sb-step"><div class="sn">2</div><div class="desc">Gemini に依頼する</div></div>
    ...
  </div>
  <div class="sb-prompt">{Gemini 発注プロンプトのひな型}</div>
  <div class="sb-note">{評価の観点・締切}</div>
</div>
```

紫系 (`--purple`) の背景 + 強調枠で目立たせる (lec05 を踏襲)。

---

## SUMMARY — FLOW 図 + 三つの統一原理 (lec02 〜)

各回の最終スライドは「FLOW 図 (横並び 3 ブロック + 矢印)」 + 「三つの統一原理 card」 で構成する。 FLOW 図は SVG で直接描く (Chart.js ではない)。

```html
<section class="slide" id="slide-{最終番号}" data-title="まとめ">
  <div class="slide-inner">
    <div class="hr-section"><span class="label">SUMMARY</span></div>
    <div class="eyebrow">本日のまとめ</div>
    <h2>三つの統一原理</h2>
    <div class="flow-card"> <svg viewBox="0 0 800 160">...</svg> </div>
    <div class="principle-card"> <div class="num">01</div> ... </div>
    <div class="principle-card"> <div class="num">02</div> ... </div>
    <div class="principle-card"> <div class="num">03</div> ... </div>
  </div>
</section>
```

**注意**: 三つの統一原理の本文で「あらゆる ... は X・Y・Z の 3 つで記述できる」 のように **パラメータ数を書くときは、本文の式と矛盾しないように確認**する。 例: 周期現象は **4 パラメータ** (中心 C・振幅 A・周期 T・位相 φ) で表される。 「3 つ」 と書くと中心 C が漏れる。 `lec-content-check` skill で自動検出。

---

## Canvas (低レベル描画) — `ensureSize` 高 DPI 対応 (lec05 で確立)

Chart.js を使わない素の Canvas (観覧車・人と影・Lissajous 等) は、 必ず高 DPI 対応のヘルパを通す:

```js
function ensureSize(canvas) {
  const dpr = window.devicePixelRatio || 1;
  const rect = canvas.getBoundingClientRect();
  const targetW = Math.max(1, Math.round(rect.width * dpr));
  const targetH = Math.max(1, Math.round(rect.height * dpr));
  if (canvas.width !== targetW || canvas.height !== targetH) {
    canvas.width = targetW; canvas.height = targetH;
  }
  const ctx = canvas.getContext('2d');
  ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
  return { ctx, w: rect.width, h: rect.height };
}
```

呼び出し側:
```js
const { ctx, w, h } = ensureSize(canvas);
ctx.clearRect(0, 0, w, h);
// ... 通常の Canvas API で描画
```

ピル背景つきラベルを描くヘルパも共通化 (`pillLabel`, `roundRectPath`) — lec05 のコードを参照。

---

## スライド遷移と動的キャンバスのリサイズ

スライド切替時に各スライドが自身の Canvas/Chart をリサイズできるよう、 メインの slide-deck IIFE で **`lec0N-slide-change` カスタムイベント**を発火する (N は回番号):

```js
window.dispatchEvent(new CustomEvent('lec0N-slide-change', { detail: { target } }));
```

各 PART の IIFE は:
```js
window.addEventListener('lec0N-slide-change', () => requestAnimationFrame(draw));
```
を購読する。 さらに Web Audio を使う PART では「自分のスライド以外に移ったら自動で stop」 を組み込む:
```js
window.addEventListener('lec0N-slide-change', (e) => {
  if (playing && e.detail && e.detail.target !== {自スライド番号}) playBtn.click();
});
```

---

## Web Audio + マイク録音 (lec05 PART 2 で確立)

純音再生 (Web Audio) と マイク録音 + 速度・振幅操作 (Web Audio + ScriptProcessor) のパターンを規約化:

### 純音再生
- `AudioContext` を click ハンドラ内で生成 (Safari/iOS の autoplay 制限対策)
- `OscillatorNode` (sine) + `GainNode` (0 → 振幅へリニアランプ 40 ms) で耳保護
- stop も 40 ms フェードアウト後に `oscillator.stop()`
- スライド離脱時に自動停止 (`lec0N-slide-change` イベント購読)

### マイク録音 + 再生
- **MediaRecorder + decodeAudioData は使わない** (codec 依存で失敗するため)
- `getUserMedia` → `MediaStreamAudioSourceNode` → `ScriptProcessorNode` で生 PCM を捕獲し、 `AudioContext.createBuffer` で直接 AudioBuffer 化
- ScriptProcessor は `destination` に繋がないと `onaudioprocess` が発火しないので、 `gain=0` の silent sink を経由
- 録音は最大 3 秒で自動停止 (耳保護)
- 再生は `AudioBufferSourceNode` + `playbackRate` + 後段 `GainNode`

### 波形描画の挙動
- 横軸 = 録音時間 (固定)、 canvas 全幅 = recDur 秒
- `envWidthFrac = 1 / rate` で波形を伸び縮みさせる:
  - `rate > 1` → canvas 内に収まる + 右側に「再生終了」 破線
  - `rate < 1` → canvas からはみ出す + 右端に「波形は右へ伸びる →」 マーカ
- ループ上限を `drawMax = Math.min(envPixels, totalPixels)` で打ち切る (canvas 外を skip)

```js
const envWidthFrac = 1 / rate;
const envPixels = Math.max(2, Math.floor(totalPixels * envWidthFrac));
const drawMax = Math.min(envPixels, totalPixels);
for (let px = 0; px < drawMax; px++) { /* 描画 */ }
```

---

## 過去の回の構成 (アーカイブ)

### lec02 — 式で世界を動かす (23 スライド)
1 次方程式・1 次不等式・連立方程式・指数爆発。 NEXT スライドあり (lec03 以降は廃止)。

### lec03 — 図形と方程式 (23 スライド) — **「身近 → 金融 → AI/DS」 骨格を確立**
座標・距離・円・領域・レコメンド/顔認証。

### lec04 — 1 次関数と 2 次関数 (20 スライド) — **回帰の入口**
気温→アイスコーヒー / 放物線・最小二乗法・AI 同じ仕組み。

### lec05 — 周期と、波 (23 スライド) — **最新の参照モデル**
冒頭「これまでの振り返り」 (NEW) + 観覧車 sin/cos / 音 440 Hz + マイク録音 / 季節と経済の sin フィット / うなり・矩形波 / Lissajous + 提出課題 (Gemini 連携)。

---

## voice / content 自己審査 — HTML 完成後に必須

Phase 1 (HTML) が完成したら **ユーザー確認の前**に、 以下 2 つの sub-skill を必ず通す:

1. **`lec-voice-audit`** — AI 臭・大主語+抽象動詞メタファー・二重修飾・スローガン調を検出
2. **`lec-content-check`** — 数値・概念・用語統一・SUMMARY パラメータ漏れを検出

両方の指摘を修正してからユーザーに「HTML はこれで OK か」 と聞く。 これにより自分で気付いた問題を事前に潰せる。
