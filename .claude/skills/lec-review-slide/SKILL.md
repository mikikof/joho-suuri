---
name: lec-review-slide
description: 情報数理入門の lecNN/index.html の冒頭(slide-2)に「これまでの振り返り」スライドを追加する。前回までに学んだ主要グラフ/概念を 3 カード横並びで思い出させ、クリック展開でその回での他の例を見せるアコーディオン UI。`new-lecture` から呼ばれる、もしくは既存回への遡及追加でも使う。
---

# lec-review-slide — 冒頭「これまでの振り返り」スライド生成

各回 (N ≥ 2) の冒頭に必須化された **slide-2「これまでの振り返り」** を生成する。 lec05 で確立したパターンを規約化した skill。

---

## いつ使うか

- `new-lecture` skill が Phase 1 (HTML) で各 PART を組んだ後に呼ぶ
- 既存回 (lec02-04) に遡及追加してほしいとユーザーが言ったとき
- ユーザーが「振り返りスライド」「lec-review-slide」「冒頭にレビューを足して」と言ったとき

---

## 動作原則

1. **slide-2 として挿入する**。 既存 slide-N (N ≥ 2) を全部 slide-(N+1) に renumber する
2. **3 カード固定** (直線/放物線/指数 のような 3 つのカテゴリ)。 回によってカテゴリは変える
3. **代表現象は 1 つずつ** (常時表示) + **アコーディオンで他例 2-3 個ずつ**
4. **既存メモリ `feedback_no_ai_tone_in_lectures` に従う**: 大主語+抽象動詞メタファー (「世界を書く」 等) は禁止
5. 完了後、 必ず `lec-voice-audit` skill を通す (タイトル文の自己審査)

---

## 入力収集 (対話で確認)

- **対象の回**: lec0N (現在編集中の HTML)
- **振り返るカテゴリ 3 つ**: 回の前回までで扱った主要なグラフ/概念を 3 つ選ぶ。 例:
  - lec05 → 直線 / 放物線 / 指数
  - 仮想 lec06 (確率) → 一様分布 / 正規分布 / 二項分布
  - 仮想 lec07 (微分) → 接線 / 極値 / 変化率
- **各カテゴリの代表現象** (常時表示): 1 つずつ。 lec02-04 の本文から取る (画像/グラフあれば SVG 化)
- **各カテゴリのアコーディオン項目** (展開時に見せる他例): 2-3 個ずつ

入力が揃ったら **カード設計案 (3 カードのタイトル / 代表現象 / 他例)** を箇条書きで提示し承認をもらう。

---

## 過去回の素材リスト (lec02-04 から)

### 直線 (1 次関数)
- 代表: **気温 → アイスコーヒー売上** (lec04 PART 1)
- 他例:
  - 家電を買うのに必要なバイト時間 (lec02 PART 2 / 1 次方程式)
  - 料金プラン A vs B、 逆転点 (lec02 PART 3 / 不等式)
  - 機械が一発でベスト直線を引く (lec04 PART 4 / 最小二乗法)

### 放物線 (2 次関数)
- 代表: **投げ上げたボールの軌道** (lec04 PART 2)
- 他例:
  - 一番儲かる値段は放物線の頂点 (lec04 PART 2-2)
  - 税率と税収のラッファー曲線 (lec04 PART 2)
  - 同じ柵の長さで面積最大化 (lec04 PART 2)

### 指数曲線
- 代表: **100 万円を年利 5% で 30 年運用** (lec02 PART 1 / 複利)
- 他例:
  - 毎日 1 万円 vs 毎日 2 倍 (lec02 PART 5 / 指数爆発)

### 座標・距離 (lec03 ベース)
- 代表: 教室の座席 (lec03 PART 1-1)
- 他例:
  - リスクリターン散布図 (PART 1-2)
  - 三平方の定理で距離 (PART 2)
  - 円の方程式 (PART 3)

### 領域 (lec02-03 ベース)
- 代表: ATM が届く範囲 (lec03 PART 4 想定)
- 他例:
  - 1 次不等式の領域 (lec02 PART 3)
  - 円の内/外 (lec03 PART 4)

### 推薦・距離 (lec03 PART 5 ベース)
- 代表: 映画レコメンドの散布図
- 他例:
  - Spotify 風カードグリッド
  - SVG パラメトリック顔認証

回によって組み合わせを変える。

---

## HTML テンプレ (lec05 の構造)

```html
<!-- 2: REVIEW (これまでの振り返り) -->
<section class="slide" id="slide-2" data-title="これまでの振り返り">
  <div class="slide-inner">
    <div class="eyebrow">lec02 〜 lec0(N-1) を振り返る</div>
    <h2>{タイトル — voice 注意 (下記)}</h2>
    <p class="section-sub">
      第 2 回〜第 {N-1} 回では、 身の回りの現象を <strong>式とグラフ</strong>で表す手順を学んだ。 {カテゴリ 3 つの名前} — 代表例を 1 つずつ思い出し、 「ほかの例を見る」 をクリックすると、 その回で確かに扱ったほかの題材を確認できる。
    </p>

    <div class="review-grid">
      <!-- カード 1 -->
      <div class="rev-card rev-line"> <!-- rev-line / rev-quad / rev-exp などカテゴリ別の修飾子 -->
        <div class="rev-graph">{ミニ SVG グラフ}</div>
        <div>
          <div class="rev-meta-tag">出典 ／ 第 X 回 PART Y</div>
          <div class="rev-name">{カテゴリ名 (例: 直線（1次関数）)}</div>
        </div>
        <div class="rev-body">
          <strong>{代表現象のキャッチ}</strong>。 {1〜2 行の補足説明}。
        </div>
        <button class="rev-toggle" type="button" data-target="rev-line-extra" aria-expanded="false">
          <span class="rev-toggle-label">ほかの例を見る</span>
          <span class="rev-toggle-arrow">▼</span>
        </button>
        <div class="rev-extra" id="rev-line-extra">
          <div class="rev-item">
            <div class="rev-ic">{24x24 アイコン SVG}</div>
            <div class="rev-it-text">
              <strong>{他例 1 のキャッチ}</strong>。 {1 行説明}。
              <div class="rev-it-src">第 X 回 PART Y ／ {サブテーマ}</div>
            </div>
          </div>
          <!-- 他例 2, 3 ... -->
        </div>
      </div>
      <!-- カード 2, 3 ... -->
    </div>
  </div>
</section>
```

---

## CSS (lec05 から流用、 改変禁止 / 必要に応じて色だけ変える)

```css
/* ====================== REVIEW SLIDE (slide-2 ｜ これまでの振り返り) ====================== */
.review-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 20px; margin-top: 28px; align-items: start; }
.rev-card { background: var(--surface); border: 1px solid var(--line); border-radius: var(--radius-lg); padding: 22px 24px 18px; box-shadow: var(--shadow-sm); display: flex; flex-direction: column; gap: 14px; }
.rev-card.rev-line { border-top: 4px solid #2563EB; }
.rev-card.rev-quad { border-top: 4px solid #F59E0B; }
.rev-card.rev-exp  { border-top: 4px solid #E11D48; }
.rev-graph { width: 100%; aspect-ratio: 5 / 3; background: var(--surface-2); border-radius: var(--radius-md); padding: 12px; display: grid; place-items: center; }
.rev-graph svg { width: 100%; height: 100%; display: block; }
.rev-meta-tag { font-family: var(--font-mono); font-size: 11px; letter-spacing: 0.08em; color: var(--ink-3); text-transform: uppercase; font-weight: 600; }
.rev-card.rev-line .rev-meta-tag { color: #1D4ED8; }
.rev-card.rev-quad .rev-meta-tag { color: #B45309; }
.rev-card.rev-exp  .rev-meta-tag { color: #BE123C; }
.rev-name { font-size: 22px; font-weight: 900; color: var(--ink); margin-top: 4px; line-height: 1.25; letter-spacing: -0.01em; }
.rev-body { font-size: 14px; color: var(--ink-2); line-height: 1.75; }
.rev-body strong { color: var(--ink); font-weight: 700; }
.rev-toggle { display: flex; align-items: center; justify-content: space-between; gap: 8px; padding: 12px 16px; margin-top: auto; background: var(--surface-2); border: 1px solid var(--line); border-radius: var(--radius-md); font-family: var(--font-jp); font-size: 13px; font-weight: 700; color: var(--ink-2); cursor: pointer; transition: background 0.15s ease, border-color 0.15s ease, color 0.15s ease; width: 100%; text-align: left; }
.rev-toggle:hover { background: var(--blue-bg); border-color: var(--blue-bg-2); color: var(--blue-dark); }
.rev-card.rev-quad .rev-toggle:hover { background: var(--amber-bg); border-color: #FDE68A; color: #B45309; }
.rev-card.rev-exp  .rev-toggle:hover { background: var(--rose-bg); border-color: #FECDD3; color: #BE123C; }
.rev-toggle .rev-toggle-arrow { font-size: 11px; transition: transform 0.25s ease; display: inline-block; }
.rev-toggle[aria-expanded="true"] .rev-toggle-arrow { transform: rotate(180deg); }
.rev-extra { max-height: 0; overflow: hidden; transition: max-height 0.32s ease; }
.rev-extra.open { max-height: 900px; }
.rev-item { display: grid; grid-template-columns: 36px 1fr; gap: 12px; align-items: start; padding: 12px 2px; border-top: 1px dashed var(--line); }
.rev-item:first-child { border-top: none; padding-top: 14px; }
.rev-ic { width: 36px; height: 36px; background: var(--surface-2); border-radius: var(--radius-sm); display: grid; place-items: center; }
.rev-ic svg { width: 24px; height: 24px; display: block; }
.rev-it-text { font-size: 13px; line-height: 1.7; color: var(--ink-2); }
.rev-it-text strong { color: var(--ink); font-weight: 700; }
.rev-it-src { font-family: var(--font-mono); font-size: 10px; letter-spacing: 0.06em; color: var(--ink-3); font-weight: 600; margin-top: 4px; }
@media (max-width: 1100px) {
  .review-grid { grid-template-columns: 1fr; }
}
```

---

## JS (アコーディオン制御 — IIFE で末尾に追加)

```js
// ====================== REVIEW SLIDE (slide-2): カードのアコーディオン ======================
(() => {
  const toggles = document.querySelectorAll('#slide-2 .rev-toggle');
  toggles.forEach((btn) => {
    btn.addEventListener('click', () => {
      const id = btn.dataset.target;
      const panel = document.getElementById(id);
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

## 既存回 (lec02-04) への遡及挿入手順

1. ファイル backup: `cp lec0N/index.html lec0N/index.html.bak`
2. **slide 番号 renumber**: 既存 slide-2 〜 slide-(末尾) を slide-3 〜 slide-(末尾+1) に。 大きい番号から逆順で sed:
   ```bash
   F="path/to/lec0N/index.html"
   for n in {末尾} {末尾-1} ... 2; do
     m=$((n+1))
     sed -i '' \
       -e "s|id=\"slide-$n\"|id=\"slide-$m\"|g" \
       -e "s|href=\"#slide-$n\"|href=\"#slide-$m\"|g" \
       -e "s|data-slide=\"$n\"|data-slide=\"$m\"|g" \
       "$F"
   done
   ```
3. `data-slide-range="X-Y"` も renumber (X, Y を +1)
4. サイドバーの `sub-num">X</span>` も renumber (大きい順)
5. JS 内の `#slide-N`, `e.detail.target !== N`, `nav-total>N</span>` も renumber
6. 新 slide-2 HTML を表紙 (slide-1) と元 slide-2 (renumber 後の slide-3) の間に挿入
7. サイドバーに新エントリ追加: `<a class="nav-link" href="#slide-2" data-slide="2"><span class="icon">↺</span>これまでの振り返り<span class="sub-num">2</span></a>`
8. CSS (上記) を `</style>` 直前に追加
9. JS (上記) を `</script>` 直前に追加
10. `grep` で id="slide-N" の連番が空きなく揃っていることを検証

---

## 完了後

完成後、 必ず以下を通す:
- **`lec-voice-audit`**: タイトル h2 と section-sub の voice チェック (大主語+抽象動詞・スローガン調)
- 動作確認: アコーディオンの開閉、 ▼ 矢印の回転、 ボタンテキスト切替

---

## 注意

- **「世界を書いてきた」 のような大主語+抽象動詞メタファーは禁止**。 「身の回りの現象を表してきた」 など具体動詞へ
- **「世界を○○する」「現実を語る」「真理を見る」 等のスローガンも禁止**。 講義として自然な「現象を表す」「グラフで書く」 を使う
- 代表現象の SVG は lec02-04 から実物を抽出する (web search で適当に作らない)
- 出典タグ (`第 X 回 PART Y`) は必ず正しい回・章を書く (うろ覚えで書かない)
