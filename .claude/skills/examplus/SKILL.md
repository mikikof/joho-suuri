---
name: examplus
description: 情報数理入門の lecNN/index.html に「日常で見たことがある事例」 + 「動的なインタラクティブ」 を追加してリッチ化する。 物理 / スポーツ / 金融 / 食 / ゲーム / 音楽 / 幾何 の 7 軸から事例を選択し、 静的紹介スライドと動的操作スライドを組み合わせて挿入する。 ユーザーが /examplus を実行したとき、 もしくは「もっと身近な例で導入したい」 「インタラクティブにして」 「もう一段リッチに」 と言ったときに起動する。
---

# /examplus — 「日常事例 + インタラクティブ」 追加スキル

`/brushup`(全方向の総合改善)、 `/visual`(ビジュアル一点突破)とは独立した、 **「現象の入口拡張」 + 「操作の動的化」 の二点突破** スキル。

数式や抽象概念を見せる前に、 「学生が実生活で見たことがある現象」 を入口として置き、 そこから自分の手で動かして体感する流れを作る。

---

## 思想 — これが最も重要

- **抽象 → 具体 への階段を作る**: 数式・理論を見せる前に、 必ず「身近で見たことがある現象」 を 1〜2 枚置く
- **3 軸スピン**: 1 つのトピックに対して、 (a) 物理・スポーツ、 (b) 経済・金融、 (c) 計算・幾何 の 3 視点から例を持ち寄ると、 学生が「同じ式で世界が動いている」 と気づく
- **静的紹介 → 動的操作 の二段構え**: 
  1. **Story slide** — 静的な SVG イラスト + 解説で「現象の絵」 を見せる
  2. **Interactive slide** — スライダー / ボタンで「自分で動かして」 体感する
- **既存の voice を壊さない**: 元の slide voice、 用語、 構成を尊重。 追加のみ、 削除はユーザー承認
- **教育的順序を守る**: 「身近(物理・スポーツ・食) → 経済・金融 → 抽象(回帰 / 最適化)」 の順で見せる

---

## いつ使うか

トリガー:
- ユーザーが `/examplus` を実行した時
- 「もっと分かりやすい例を入れて」 「インタラクティブにして」 「もう一段リッチに」 「具体例が足りない」 と言われた時
- /brushup の後、 さらに具体例を入れたいとき

`/brushup` との使い分け:

| シーン | コマンド |
|---|---|
| デザイン規約点検・全方向改善 | `/brushup` |
| ビジュアル要素の追加(SVG 等) | `/visual` |
| **日常事例 + インタラクティブ追加** | **`/examplus`** |
| 監査・第三者レビュー | `/audit-review` |

---

## 引数

```
/examplus [TARGET] [--theme "..."] [--axis A,B,C] [--intensity light|medium|rich] [--insert-after slideN]
```

- 位置引数 `TARGET`: ファイルパス(例: `lec04/index.html`)、 スラッグ(例: `lec04`)、 または直近編集対象
- `--theme "..."`: 追加する例の方向性(例: `スポーツ`, `金融`, `食べ物`, `ゲーム`, `音楽`)。 複数指定可(例: `スポーツ,金融`)
- `--axis A,B,C`: 軸の組み合わせ(physics / finance / sport / food / game / music / geometry / culture から選択)
- `--intensity`: 追加の濃さ
  - `light`: 静的な現象紹介スライド 1 枚追加
  - `medium`: 静的紹介 1 枚 + 動的インタラクティブ 1 枚 (推奨)
  - `rich`: 静的紹介 + 動的インタラクティブ + 連動グラフ 2 枚 + Excel ボックス + 補足カード
- `--insert-after slideN`: 既存 slide N の直後に挿入(指定なしなら Claude が自動で挿入位置を提案)

引数なしの場合は **直近の会話から対象 + テーマを自動推定** し、 ユーザーに確認する。

---

## 実行手順

### Step 1: 対象特定 + 文脈把握

1. TARGET から対応する `lecNN/index.html` を Read
2. 既存の PART 構成、 slide 数、 SVG 数、 インタラクティブ slide 数を把握
3. ユーザーが「どのトピック」 に追加したいか確認(例: 「PART 3 の放物線に」 「PART 1 の散布図に」)
4. 既存のスライド範囲(PART 番号、 slide ID)、 nav-link、 OVERVIEW TOC を把握

### Step 2: テーマ決定 + 軸の組み合わせ提案

`--theme` が指定されていなければ、 **3 軸提案**を出す:

```
このトピックに合いそうな事例軸:
  ① 物理・スポーツ: <例: 野球の放物線、 バスケのシュート>
  ② 経済・金融:    <例: 税収のジレンマ、 投資 ROI>
  ③ 食・日常:      <例: 塩加減と美味しさ、 火加減と焼き上がり>

どれを採用しますか? (複数 OK / 単独 OK)
```

ユーザーが選択 → そのテーマに沿った具体例を Step 3 で提示。

### Step 3: 具体例の提示

`§ 6 事例カタログ` から該当軸の事例を 2-3 個選び、 ユーザーに提示:

```
スポーツ軸の候補:
  - 野球のボールの軌道(放物線、 重力の 2 次関数)
  - バスケのシュート角度(成功率の 2 次関数)
  - 陸上 100m タイム(風速との関係、 1 次関数)

金融軸の候補:
  - Laffer 曲線(税率と税収、 1 次×1 次=2 次)
  - 広告投資 ROI(投資額と売上、 限界効用)
  - 投資ポートフォリオ(リスクとリターン)

どれにしますか?
```

### Step 4: 配置・濃さの提案

`--intensity` と `--insert-after` を確認しつつ提案:

```
配置案:
  - slide 12 (PART 3 GATE) の直後に挿入
  - 濃さ: medium(静的 1 + 動的 1 = 計 2 スライド追加)
  - 後続スライドの ID は +2 シフト

最終的な slide 構成:
  ...
  12: PART 3 GATE (現状維持)
  13 新: 野球の放物線(静的、 SVG イラスト)
  14 新: バスケのシュート確率(動的、 角度スライダー)
  15 (= 旧 13): ...
  ...
```

### Step 5: ユーザー承認

`AskUserQuestion` で以下を提示:
- (a) この計画で実装(推奨)
- (b) 軸を変える
- (c) 配置を変える
- (d) 却下

承認後、 Step 6 へ。

### Step 6: 実装

#### 6-1. ID シフト (もし必要なら)
既存の slide ID を後ろにずらす。 `replace_all` で `slide-N → slide-N+k` を **降順**(衝突回避)で実行:
```
slide-20 → slide-22
slide-19 → slide-21
...
slide-13 → slide-15
```

#### 6-2. nav の更新
- nav-link の `href`、 `data-slide`、 `data-slide-range`、 `sub-num` を全て新しい範囲に
- OVERVIEW TOC の `href`、 `data-slide` も更新
- `slide-counter` の `nav-total` を新総数に
- 該当 PART の sub-num 範囲を拡張(例: `12-15` → `12-17`)

#### 6-3. 新 slide の HTML を挿入
`§ 7 静的紹介 / 動的インタラクティブ テンプレート` から該当パターンを選んで挿入。

#### 6-4. JS の IIFE を追加
動的 slide には対応する JS IIFE を `<script>` 内に追加。 既存の Chart.js / SVG マニピュレーション規約に従う。

#### 6-5. 関連箇所の文言調整
- 既存の callout で「次のスライドで…」 等の参照があれば更新
- OVERVIEW のサブテキストに新事例を含める
- SUMMARY の例題列挙に新事例を加える(必要なら)

### Step 7: 構文チェック + ブラウザ確認

```python
import re
with open(path) as f: s = f.read()
print('div', len(re.findall(r'<div\b', s)), '/', len(re.findall(r'</div>', s)))
print('section', len(re.findall(r'<section\b', s)), '/', len(re.findall(r'</section>', s)))
print('slide ids:', sorted([int(x) for x in re.findall(r'id=\"slide-(\d+)\"', s)]))
m = re.search(r'<script>(.*)</script>', s, re.DOTALL); js = m.group(1) if m else ''
print('JS  {} ', js.count('{'), '/', js.count('}'))
print('JS  ()  ', js.count('('), '/', js.count(')'))
```

タグバランス・JS バランス・slide ID 連続性を確認。 `open lec04/index.html` でブラウザ確認をユーザーに依頼。

---

## 6. 事例カタログ(7 軸 × 概念別)

各事例には: トピック / 数学的構造 / 推奨レベル / インタラクション 案。

### A. 物理・スポーツ

| 事例 | 数学構造 | 概念 | レベル | インタラクション |
|---|---|---|---|---|
| 野球のボールの軌道 | y = -½gt² + v₀t | 2 次関数(時間) | 高校物理 | 初速度 v₀ スライダーで軌道変化 |
| バスケのシュート角度 | 命中率 = f(角度) | 2 次関数(角度) | 高校 | 角度スライダー、 動的命中率表示 |
| 陸上のタイム vs 風速 | T = T₀ - α·風速 | 1 次関数 | 高校 | 風速スライダー、 タイム表示 |
| サッカーキックの飛距離 | 距離 = (v² sin 2θ)/g | 三角関数 | 大学 | 角度・速度スライダー |
| 噴水の高さ | y = -gt²/2 + v₀t | 2 次関数 | 高校 | 高さの周期性 |
| ダーツの的中率 | 確率分布 | 正規分布 | 大学 | 距離スライダー、 命中円 |
| 自転車の速度 vs 力 | v = √(2P/m) | √関数 | 高校 | パワー入力 |

### B. 経済・金融

| 事例 | 数学構造 | 概念 | レベル | インタラクション |
|---|---|---|---|---|
| Laffer 曲線(税率 vs 税収) | T = t·(1-t/100)·E₀ | 1 次×1 次=2 次 | 公民 | 税率スライダー、 税収バー |
| 広告投資 ROI | R = a·広告費 - b·広告費² | 2 次関数 | 経営 | 広告費スライダー、 ROI 表示 |
| 商品価格と売上 | R = p·(C - p) | 1 次×1 次=2 次 | 高校 | 価格スライダー、 D + R 同時 |
| 投資ポートフォリオ | リスク vs リターン散布図 | ベクトル空間 | 大学 | 配分スライダー、 散布図 |
| 単利と複利 | A = P(1+rt) vs P(1+r)^t | 1 次 vs 指数 | 高校 | 期間スライダー、 比較 |
| 為替レート変動 | 時系列 | 確率過程 | 大学 | 履歴チャート |
| 商品割引の最適化 | R = p(1-d)·D(d) | 2 次関数 | 経営 | 割引率スライダー |

### C. 食・日常

| 事例 | 数学構造 | 概念 | レベル | インタラクション |
|---|---|---|---|---|
| 塩加減と美味しさ | 上に凸の 2 次関数 | 最適化 | 中学 | 塩量スライダー、 美味しさバー |
| 焼き加減と焦げ目 | 時間と温度のトレードオフ | 2 変数最適化 | 高校 | 温度・時間 2 軸 |
| コーヒーの濃さ | 抽出時間 vs 濃度 | 1 次関数(近似) | 中学 | 時間スライダー |
| 寝る時間 vs 集中力 | 上に凸 | 2 次関数 | 中学 | 睡眠時間スライダー |
| 練習時間 vs 上達 | 対数関数 | 飽和 | 中学 | 練習時間 |

### D. ゲーム・音楽

| 事例 | 数学構造 | 概念 | レベル | インタラクション |
|---|---|---|---|---|
| ゲームの難易度 vs クリア率 | 2 次関数 | 最適難度 | 中学 | 難度スライダー |
| 音量 vs 聞こえ方 | 対数(dB) | 対数関数 | 高校物理 | 音量スライダー |
| BPM vs 心拍数 | 1 次関数(近似) | 線形回帰 | 中学 | BPM スライダー |
| RPG レベル vs 経験値 | 多項式 / 指数 | 累積 | 中学 | レベル指定 |
| 音階の周波数 | 指数関数 | 周波数比 | 高校 | 鍵盤クリック |

### E. 幾何・計算

| 事例 | 数学構造 | 概念 | レベル | インタラクション |
|---|---|---|---|---|
| 周囲固定の長方形面積 | A = x(P/2 - x) | 1 次×1 次=2 次 | 中学 | 縦の長さ スライダー、 動的長方形 SVG |
| 体積最大化(箱) | V = x²(L-2x) | 3 次関数 | 高校 | 切り取りサイズ スライダー |
| 円の最大内接 | A = πr² | 2 次関数 | 中学 | 半径スライダー |
| 階段の段数 vs 高さ | 反比例 | 1/x | 中学 | 段数スライダー |
| 紙を半分に折る | 2^n | 指数関数 | 中学 | 折る回数 |

### F. データ・機械学習

| 事例 | 数学構造 | 概念 | レベル | インタラクション |
|---|---|---|---|---|
| 散布図のパターン | 相関の強さ | 統計学 | 高校 | r スライダー(or プリセット) |
| 擬似相関 | 共通因子 | 因果推論 | 大学 | 共通因子スライダー |
| 線形回帰 | 最小二乗 | 線形代数 | 大学 | a, b スライダー + LSM ボタン |
| 多項式フィット | 過学習 | 機械学習 | 大学 | 次数スライダー |

### G. 文化・歴史

| 事例 | 数学構造 | 概念 | レベル | インタラクション |
|---|---|---|---|---|
| 人口推移 | 指数 / 飽和 | ロジスティック | 高校 | 年代スライダー |
| 言語の単語頻度 | べき乗則 | Zipf 則 | 大学 | ランクスライダー |
| 歴史的価格 | 時系列 | 累積 | 大学 | 年代範囲 |

---

## 7. 静的紹介 / 動的インタラクティブ テンプレート

### Pattern 1: 静的 STORY スライド(現象を絵で見せる)

新 slide id "slide-N" の skeleton:

```html
<!-- ============================ N: <PARTラベル> - <事例名> (現象導入) ============================ -->
<section class="slide" id="slide-N" data-title="PART X ｜ <事例名>">
  <div class="slide-inner">
    <div class="eyebrow">Story ｜ 日常の<軸名>① <事例の見出し></div>
    <h2><キャッチー一行 1><br><キャッチー一行 2、 強調 em></h2>

    <p class="section-sub">
      <導入文: 学生が見たことある体験を 2-3 行></p>

    <!-- 静的 SVG イラスト(chart-wrap で枠) -->
    <div class="chart-wrap" style="height: 380px; padding: 20px;">
      <svg viewBox="0 0 800 380" xmlns="http://www.w3.org/2000/svg" style="width:100%;height:100%;display:block;" aria-hidden="true">
        <!-- 現象を象徴するイラスト + 数式注釈 -->
      </svg>
    </div>

    <div class="callout amber" style="margin-top: 20px;">
      <div class="ico">◎</div>
      <div class="body">
        <数学的洞察 1: 「これは何の関数になっているか」></div>
    </div>

    <div class="callout" style="margin-top: 16px;">
      <div class="ico">◎</div>
      <div class="body">
        <次へのつなぎ: 「この後の slide で別の例を見る/動かす」></div>
    </div>
  </div>
</section>
```

### Pattern 2: 動的 INTERACTIVE スライド(スライダーで現象を変える)

```html
<!-- ============================ N+1: <事例名> (動的) ============================ -->
<section class="slide" id="slide-N+1" data-title="PART X ｜ <事例名>を動かす">
  <div class="slide-inner">
    <div class="eyebrow">Interactive ｜ <操作の見出し></div>
    <h2><意図したメッセージ></h2>
    <p class="section-sub"><手順説明></p>

    <div class="block">
      <div class="controls" style="grid-template-columns: 1fr;">
        <div class="control-row">
          <div class="control-label">
            <span><パラメータ名と単位></span>
            <span class="control-value" id="cEx-x-val">0</span>
          </div>
          <input type="range" id="cEx-x" min="0" max="100" value="50" step="1">
        </div>
      </div>

      <div class="equation light"><数式の動的表示></div>

      <div class="split-answer" style="display: grid; grid-template-columns: 1fr 1fr; gap: 20px; margin-top: 16px;">
        <div class="chart-wrap" style="padding: 16px;">
          <!-- 動的 SVG または canvas -->
          <svg id="cEx-svg" viewBox="0 0 400 280" style="width:100%;height:100%;display:block;"></svg>
        </div>
        <div class="chart-wrap" style="padding: 20px 8px 0;">
          <canvas id="cEx-chart"></canvas>
        </div>
      </div>

      <div class="stat-row" style="margin-top: 16px;">
        <div class="stat accent">
          <div class="stat-label">いまの値</div>
          <div class="stat-value" id="cEx-cur" style="font-size: 30px;">—</div>
        </div>
        <div class="stat amber">
          <div class="stat-label">最大値 ＠ ベスト</div>
          <div class="stat-value" id="cEx-best" style="font-size: 30px;">—</div>
        </div>
        <div class="stat primary">
          <div class="stat-label">パラメータ範囲</div>
          <div class="stat-value">—</div>
        </div>
      </div>

      <div class="callout" style="margin-top: 16px;" id="cEx-tip-box">
        <div class="ico">◎</div>
        <div class="body" id="cEx-tip">
          <スライダー値別の解説(JS で動的)></div>
      </div>
    </div>
  </div>
</section>
```

### Pattern 3: ボタンプリセット型(連続スライダーが重い場合)

```html
<div style="display: grid; grid-template-columns: repeat(N, 1fr); gap: 10px; margin-bottom: 18px;">
  <button class="preset-btn" data-pattern="1" style="...">① <ラベル></button>
  ...
</div>
```

### Pattern 4: 「記録」 で履歴蓄積

擬似相関の slide 8 (cConfound) と同じパターン:
- スライダー + 「記録」 ボタン
- 記録するたびに散布図に紫の点が追加
- 履歴で「動的に右上がりの並びが形成される」 様子を体感

### Pattern 5: 連動する 2 グラフ

x スライダーで 2 つの chart が同時に動く(交通流の v + q、 価格の D + R など)。

---

## 8. JS IIFE テンプレート(実装上の規約)

```js
// ====================== Slide N+1: <事例名> (動的) ======================
(() => {
  const slider = document.getElementById('cEx-x');
  const valEl = document.getElementById('cEx-x-val');
  const curEl = document.getElementById('cEx-cur');
  const tipEl = document.getElementById('cEx-tip');
  const tipBox = document.getElementById('cEx-tip-box');
  const svg = document.getElementById('cEx-svg');

  const f = (x) => /* 数学関数 */;

  // Chart.js 描画(必要なら)
  const ctx = document.getElementById('cEx-chart').getContext('2d');
  const data = [];
  for (let xv = 0; xv <= 100; xv += 1) data.push({ x: xv, y: f(xv) });
  const chart = new Chart(ctx, {
    type: 'line',
    data: { datasets: [/* ... */]},
    options: { responsive: true, maintainAspectRatio: false, parsing: false, /* ... */ },
    plugins: [/* マーカー pluginやカスタム描画 */]
  });

  function update() {
    const X = +slider.value;
    valEl.textContent = X;
    curEl.textContent = f(X).toFixed(2);
    chart.update();

    // SVG イラスト更新
    svg.innerHTML = `<rect ...>...</rect>`;

    // tip メッセージ自動切替
    if (X < threshold) { tipBox.className = 'callout'; tipEl.innerHTML = '...'; }
    else { tipBox.className = 'callout amber'; tipEl.innerHTML = '...'; }
  }
  slider.addEventListener('input', update);
  update();
})();
```

**ID 命名規約**: 各 slide の DOM 要素は **2-3 文字プレフィックス + camelCase** で衝突回避(例: `cArea-x`、 `cEx-tip`、 `cConfound-record`)。 既存の `c1, c2, c3...` には絶対に被らせない。

---

## 9. 配置の推奨パターン

### A. 「PART の入口」 に挿入(現象 → 抽象 への階段)

PART X GATE の直後に静的 STORY を 1-2 枚:
- GATE → 静的事例① → 静的事例② → (既存の中身)

例: lec04 PART 3(放物線) で実施したパターン
- PART 3 GATE → 野球(静的) → 税収(静的) → 面積(動的) → 価格 STORY → 価格 INTERACTIVE

### B. 「既存スライドの拡張」 として挿入

既存スライドの後ろに動的 INTERACTIVE を追加:
- 既存 STORY → 既存 INTERACTIVE → **新動的事例**(別の角度から)

### C. 「PART 末尾の発展」 として挿入

PART の最後に「もっと先」 の例を 1 枚:
- (PART の中身) → **発展事例**(高校範囲外、 学生興味喚起)

---

## 10. やらないこと(アンチパターン)

- ❌ **既存の slide 内容を勝手に削除**: 追加のみ、 削除は明示的なユーザー承認後
- ❌ **ID プレフィックスの被り**: 既存の `c1, c2, c3...` を再利用しない
- ❌ **PART 番号や nav の整合性を壊したまま放置**: ID シフト後は nav・TOC・data-slide-range・sub-num を必ず更新
- ❌ **スライダー範囲が発散領域**: η や歩幅は理論的安定範囲を確認(lec04 で codex 監査済の知見)
- ❌ **chart-wrap の height 指定なし**: 必ず CSS or inline で固定高さ(ResizeObserver loop 防止)
- ❌ **Chart.js animation 有効**: スライダードラッグで重くなる。 `Chart.defaults.animation = false` を尊重
- ❌ **大学 1 年で消化できない用語をそのまま投入**: 「正規方程式」 「Hessian」 「交差エントロピー」 等は補足カードに留め、 メインは平易な言葉で
- ❌ **3 軸 4 軸を一気に追加**: 1 回の /examplus は 1〜2 軸まで、 多くて 3 スライド追加程度に抑える

---

## 11. 完了の判定

- HTML タグバランス OK(div / section / svg / script)
- JS バランス OK(`{}()`)
- slide ID 連続(1..N)
- nav-link / toc-item の `href ↔ data-slide` 一致
- nav の `data-slide-range`、 `sub-num` が新範囲に整合
- ブラウザでスライダー操作・ボタンクリックが動作(ユーザーに確認依頼)

---

## 12. 引数 example

```bash
/examplus
# → 直近編集対象を推定 + 軸の組み合わせを提案

/examplus lec04 --theme スポーツ,金融
# → lec04 にスポーツ + 金融の事例を 2 軸で追加(=今回の野球 + 税収パターン)

/examplus lec05 --axis physics,geometry --intensity rich
# → lec05 に物理 + 幾何 を rich レベルで追加

/examplus --insert-after slide-12 --theme 食 --intensity light
# → 直近対象の slide 12 の直後に「食」 軸の静的紹介を 1 枚追加

/examplus lec04 --theme スポーツ
# → 単一軸、 medium で動的事例 1 つ追加
```

---

## 13. 学習アーカイブ(過去に作った事例)

`/examplus` で実装した事例はここに追記して資産化する。 同じテーマで再度作るときに参考にする。

| 日付 | lec | 軸 | 事例 | 静的/動的 | 数学構造 |
|---|---|---|---|---|---|
| 2026-05-09 | lec04 | スポーツ | 野球のボールの軌道 | 静的 SVG | y = -½gt² + v₀t |
| 2026-05-09 | lec04 | 金融 | Laffer 曲線(税率と税収) | 静的 SVG | T = t(1 - t/100)·E₀ |
| 2026-05-09 | lec04 | 幾何 | 周囲固定の長方形面積最大化 | 動的(x スライダー + 動的長方形 SVG + 放物線 chart) | A = x(10-x) |
| 2026-05-09 | lec04 | データ | 散布図のパターン 4 種 | 動的(プリセット 4 ボタン + 散布図) | r = corr |
| 2026-05-09 | lec04 | 因果推論 | 擬似相関(気温→アイス + 水難) | 動的(気温スライダー + 記録ボタン) | 共通因子モデル |
