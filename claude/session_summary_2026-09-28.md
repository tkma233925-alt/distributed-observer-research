# セッションまとめ（2026-09-28）

対象論文: 英文版 `sensornetwork.tex`（参照したのは 9/25 受領版 `sensornetwork (2).tex`）

このセッションは **第5節（数値例）以降のゼミ前レビュー** と **第4節末の定理・証明の推敲**。コードの変更はなし。

---

## 第1部　第5節以降のレビュー（ゼミ前）

表の数値（σ減少率 79%/72%、ε倍率 4.8/3.6、エッジ数と平均次数、改善率の比 1.3 / 1.4〜1.8）はすべて再計算して**整合を確認済み**。問題は主張の強さと設定の書き漏れ。

### 1-1. 論理の弱点

| # | 内容 | 対応案 |
|---|---|---|
| **(a)** | 仮説2の比較に交絡。注目群の平均次数比が例1 3.00/5.76=**0.52**、例2 5.18/14.21=**0.36** で、例2は次数の不利自体が強い。生成法・密度・$p$・注目群の選び方もすべて異なる | 「verified/supported」→「consistent with Hypothesis 2」に弱める |
| (b) | 改善率は100%が上限なので、全体の改善率が高い例1では比が小さく出やすい | 指標の限界として把握しておく |
| (c) | 例1「attributed solely to degree」— $p$ のランダム割当とセンサ種別の混在があるので solely は過剰（直前は mainly で表現も揺れている） | mainly に統一 |
| (d) | 1025–1027行目「σ は下界」「減少量は $S_2/(4\sigma)$」「faster convergence」 | σ は**最小値**、$S_2/(4\sigma)$ は**保証される**減少量、$\beta_i$ で $V$ 自体が変わるので faster は言い過ぎ |
| (e) | 2例だけで「密度に対して robust」 | 「a similar tendency is observed」程度に |

### 1-2. 設定の書き漏れ

| # | 内容 |
|---|---|
| **(f)** | カルマンゲイン設計に使った $\bfW_x,\bfW_{y,i}$ が未記載。シミュレーションにノイズを入れているかも不明。ノイズなしなら「MSE at $k=250$」はほぼ0どうしの比、ノイズありなら「converge to the origin」（953行目）が誤り（原点近傍） |
| (g) | 「12試行と注目群でプール」と「最終時刻 $k=250$」が併記され、時間平均の有無が不明。改善率 $(\mathrm{MSE}_{\rm unw}-\mathrm{MSE}_{\rm w})/\mathrm{MSE}_{\rm unw}$ も未定義 |
| (h) | 試行ごとに何がランダムか（ネットワーク固定、初期推定値と通信実現値？）を一文で |
| (i) | 重みなしの σ は $\beta_i\equiv1$ 固定で σ のみ最小化した値であることを明記 |

### 1-3. 表現を直すとむしろ主張が強くなる点

**(j)** 「高次数側のゲインは小さくなる」（826, 938行目）は「重みなしより小さい」と誤読される。実際はグループ平均で重みなしより**大きい**：

| | 重みなし $\epsilon$ | 重みあり・有利側平均 $\epsilon\beta_i^{-1}$ | 不利側平均 |
|---|---|---|---|
| 例1 | 0.013 | 0.063×0.40 ≈ 0.025 | 0.063×1.46 ≈ 0.092 |
| 例2 | 0.015 | 0.053×0.39 ≈ 0.021 | 0.053×1.97 ≈ 0.105 |

→ 994行目「中心側を犠牲にしていない」の根拠になる（現状の根拠は全体改善率>20%のみ）。
ただし $\beta_i^{-1}$ の下限 $c=0.2$ 付近のノードは $0.063\times0.2\approx0.013$ で重みなしとほぼ同じか下回る。**ノートブックで $\beta_i^{-1}$ の最小値を確認**。

### 1-4. 軽微

- 827行目「Sect.\,3」手書き → `\ref{sec:ProposedObserver}`
- 例2 のしきい値 $r_1,r_2$ と距離 $r_i$ の衝突（9/25 指摘C）
- 876行目「placed according to the probability density function of」→「sampled from」
- 892行目「high density」→「node density per unit area」＋有限標本の注記
- 時間応答図の対象が例1（注目群のみ）と例2（全ノード）で不統一（本文で説明済みだが質問されやすい）

**ゼミでの優先度**: (a) 交絡 と (f) ノイズ設定。(j) は補強材料として使える。

---

## 第2部　第4節末の定理と証明

### 2-1. ユーザー原案の問題点

1. **定理の仮定に $\epsilon=1/(2\sigma)$ がない（必須）** — $\epsilon$ 任意だと $\epsilon^2S_3$ が勝ち、前半の主張は成り立たない
2. **証明の「=」は「≤」** — 使っているのは $S_3\le-\sigma S_2$ のみ
3. **「negative value」→ nonpositive** — $\bfxi$ が合意方向（$\bfGamma$ の張る空間）にあると $S_2=0$（原案で修正済み）
4. **負定値の根拠が欠落** — 負定値なのは通信なしの項 $-\bfxi^\top\bfQ\bfxi$（$\bfQ\succ\bfO$）のおかげ。通信項はそれを悪化させないだけ
5. 誤字 arrording、「the error dynamics become」→「the origin of the error dynamics is」

### 2-2. 修正案

```latex
\begin{thm}
\label{thm:main}
Let $\beta_i^{-1}$ $(i=1,\ldots,N)$ and $\sigma$ be a solution of the
LMI problem \eqref{eq:LMIproblem}, and let the cooperative gains be given by
$\epsilon\bfD_i = (\epsilon/\beta_i)\bfP_i^{-1}\bar{\bfA}_i^{-\top}$
with $\epsilon=1/(2\sigma)$.
Then, for every $\bfxi(k)$, the value of $\mathbb{E}[\Delta V]$ is less than
or equal to that in the case without communication, i.e., $\bfD_i=\bfO$.
Moreover, the origin of the error dynamics \eqref{eq:errordynamics} is
globally asymptotically stable in probability.
\end{thm}

\begin{proof}
The difference in $\mathbb{E}[\Delta V]$ between the case with the proposed
gain and the case with $\bfD_i=\bfO$ is $\epsilon S_2+\epsilon^2 S_3$.
Since $S_3\le-\sigma S_2$ holds by \eqref{eq:LMI} and $\epsilon=1/(2\sigma)$,
we have
\begin{equation*}
\epsilon S_2+\epsilon^2 S_3\le\frac{S_2}{4\sigma}\le 0 ,
\end{equation*}
which proves the former part of the theorem.
Consequently,
\begin{equation*}
\mathbb{E}[\Delta V]\le-\bfxi(k)^{\top}\bfQ\bfxi(k)+\frac{S_2}{4\sigma}
\le-\bfxi(k)^{\top}\bfQ\bfxi(k),
\end{equation*}
and, since $\bfQ\succ\bfO$, $\mathbb{E}[\Delta V]$ is negative definite with
respect to $\bfxi(k)$.
Hence, the origin of \eqref{eq:errordynamics} is globally asymptotically
stable in probability by the result in the Appendix of \cite{Yamashita}.
\end{proof}
```

### 2-3. 確認事項

- $\mathbb{E}[\Delta V]$ は厳密には $\mathbb{E}[\Delta V\mid\bfxi(k)]$（第4節冒頭の「For a fixed $\bfxi(k)$」で読み取れるので既存記法のままでも可）
- \cite{Yamashita} の Appendix の前提として、$\delta_{i,j}(k)$ の**時間方向の独立性**が本文で仮定されているか（\eqref{eq:binary} 付近）
- 定理はノイズなし（$\bfv=\bfw_i=\bfzero$）の主張。数値例でノイズを入れているなら区別して書く
- **想定質問**：$\epsilon=0$ でも $\mathbb{E}[\Delta V]\le-\bfxi^\top\bfQ\bfxi$ なので、安定性自体は局所オブザーバで保証されている。定理の中心は「協調項が安定性を壊さず、減少量を $|S_2|/(4\sigma)$ 上乗せできる」こと

---

## 第3部　初期状態の記述

- 具体値は 5.1節（724–725行目）のみ：$\bfx(0)=[1,1,1]^\top$、初期推定値 $\sim\mathcal{N}(0,3)$
- 第2・3節には $\bfx(0)$, $\hat{\bfx}_i(0)$ の仮定・説明なし（$\bfxi(0)$ は安定性の定義に記号として出るのみ）
- $\mathcal{N}(0,3)$ → $\mathcal{N}(\bfzero,3\bfI_3)$。第3節の定義（共分散行列）に従うと 3 は分散。コードが `np.random.normal(0, 3)`（標準偏差）なら $\mathcal{N}(\bfzero,9\bfI_3)$ と書くべき → **ノートブックで確認**
- 12試行で初期推定値を毎回引き直しているかも未記載（1-2 (h)）

---

## 第4部　残タスク

**必須**
1. 定理の仮定に $\epsilon=1/(2\sigma)$、証明の「≤」と負定値の根拠（2-2 をそのまま反映）
2. (f) ノイズ設定（$\bfW_x,\bfW_{y,i}$ の値、ノイズの有無）と 953行目の記述
3. (g) MSE・改善率の定義

**推奨**
4. (a)(c)(d)(e) の主張の強さの調整
5. (j) 実効ゲインの比較を追記し「中心側を犠牲にしない」の根拠にする
6. (h)(i) 試行条件と重みなし σ の求め方
7. 第3部の $\mathcal{N}(\bfzero,\cdot\,\bfI_3)$ 表記

**要確認（ノートブック）**
8. $\beta_i^{-1}$ の最小値（下限 $c=0.2$ に張り付いているノードの有無）
9. 初期推定値の生成が分散3か標準偏差3か
10. $\delta_{i,j}(k)$ の時間独立性の仮定が本文にあるか

**9/25 からの持ち越し**: B1（エッジの向き）、B2（$\bfGamma_s$ 正規化）、B3、σ の符号の向き、B6（$\bfP_i$ の転置方向）ほか → `session_summary_2026-09-25.md` 第5部
