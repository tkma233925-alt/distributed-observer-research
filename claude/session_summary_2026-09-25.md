# セッションまとめ（2026-09-25）

対象論文: 英文版 `sensornetwork.tex`（IEICE Trans. 投稿用, "Distributed Observer Design with Multiple Communication Probabilities Using a Weighted Lyapunov Function"）

このセッションは第4節（安定性解析）を中心とした **記述・記号の精査**。コードの変更はなし。
**第1部 英語表現／第2部 数式の論理／第3部 記号・定義の総点検／第4部 LaTeX組版／第5部 残タスク** の順に整理する。

---

## 第1部　英語表現

### 1-1. 「the difference correction」の言い換え（\eqref{eq:S3} 直後）

`difference correction` も `the correction of the difference` も不適切。

- `correction of X` は「X を補正する」と読まれ、意図（差に**由来する**補正）と逆になる
- 何の差かが直前で定義されていない

$\mathbb{E}[\delta^2]-\mathbb{E}[\delta]^2$ は分散そのものなので **variance** を使うのが素直。推奨案:

```latex
the second term is a correction term accounting for the variance of
$\delta_{i,j}$, namely
$\mathbb{E}[\delta_{i,j}^2]-\mathbb{E}[\delta_{i,j}]^2=p_{i,j}(1-p_{i,j})$.
```

`originating from` の後に不等式だけを置く形も据わりが悪い（`arising from the fact that ...` なら可）。

**状態: 未反映**（現行 tex は "for the correction of the difference originating from ..." のまま）

---

## 第2部　数式の論理

### 2-1. $\bfGamma^\top\hat{\bfL}_{0P}=\bfO$ の証明

2通りの証明がある。

**(a) エッジ単位の直接計算（本文で採用すべき方）**
$(i,j)\in\mathcal{E}$ の両端は同じ連結成分に属するので $\bfgamma_s^\top(\bfe_i-\bfe_j)=0$。混合積で \eqref{eq:orth2} が出て、$\hat{\bfL}_{0r}$ はその和なので零。$\mathcal{E}_r\subseteq\mathcal{E}$ だから使える。**半正定値性も部分空間も不要。**

**(b) 像空間の包含による証明**
半正定値な $\bfA,\bfB$ について $\ker(\bfA+\bfB)=\ker\bfA\cap\ker\bfB$ から $\mathrm{Im}\,\bfA\subseteq\mathrm{Im}(\bfA+\bfB)$。よって $\mathrm{Im}\,\hat{\bfL}_{0r}\subseteq\mathrm{Im}\,\hat{\bfL}_0$ で、\eqref{eq:orth1} から従う。正しいが回り道。

**重要な指摘**: 旧稿の「$p_1,p_2>0$ であり ... となる」の $p_r>0$ は**この結論には不要**（係数の符号を使わない）。$p_r>0$ が効くのは逆向きの $\ker\hat{\bfL}_{0P}=\ker\hat{\bfL}_0$、すなわち $\bfGamma$ が $\hat{\bfL}_{0P}$ の零空間**全体**を張ること（次元削減で情報を落とさない保証）の方。

### 2-2. $\epsilon=1/(2\sigma)$ の導出と「$\sigma$ が小さいほど良い」の意味

$S_3\le-\sigma S_2$ を代入すると $\epsilon S_2+\epsilon^2S_3\le S_2(\epsilon-\sigma\epsilon^2)$。$S_2\le0$ なので $\epsilon=1/(2\sigma)$ で最小値 $S_2/(4\sigma)$。一次項が協調の得、二次項がランダム通信の揺れによる損で、その釣り合い点。

**厳密性の穴（ユーザー指摘を受けて整理）**

1. **$\sigma$ は $\beta$ の関数** — $S_3$ に $\beta_i^{-1}$ が入るので、論理は「$\beta_i,\bfP_i$ を固定 → すべての $\bfxi$ で成り立つ定数 $\sigma$ をとる → $\epsilon$ を最適化」の順
2. **最小化しているのは上界** — 元の式 $S_1+\epsilon S_2+\epsilon^2S_3$ の最小点は $-S_2/(2S_3)$（$\bfxi$ 依存）。$S_2/(4\sigma)$ は代入後の上界の最小値
3. **$\beta$ を変えると $V$ 自体が変わる** — $\sigma$ の大小比較は別々の $V$ の比較になる。ただし $S_2$ は $\beta_i$ に依存しない（$\bfD_i$ の $\beta_i^{-1}$ と $\bfP$ の $\beta_i$ が打ち消す）ので、保証される減少量 $|S_2|/(4\sigma)$ は $\sigma$ だけで比較できる

**反映状況**
- 脚注に「$S_2$ is independent of the selection of the parameters $\beta_i$」を追加済み（**誤字 paremeters あり**）
- **未修正**: 本文「if we choose larger $\sigma$, $S_2/(4\sigma)$ becomes smaller」は符号の向きが逆。$S_2/(4\sigma)\le0$ なので $\sigma$ を大きくすると値は**大きく**なる。「a smaller $\sigma$ yields a larger guaranteed reduction $|S_2|/(4\sigma)$」の形に直す
- 「Therefore, $\mathbb{E}[\Delta V]$ can be made smaller than that for no communication cases」も、$S_2<0$ の条件と「than in the case without communication」への修正を提案済み

### 2-3. $\epsilon S_2+\epsilon^2S_3\le\ldots$ の不等号

$S_2,S_3$ は二次形式（スカラー）なので **$\le$ で正しい**。行列不等式 $\preceq$ が出るのは、$S_3\le-\sigma S_2$ を**すべての $\bfxi$ で**保証する LMI \eqref{eq:LMI} の段階。

---

## 第3部　記号・定義の総点検

### A. 定義前の使用／未定義

| # | 内容 | 修正 |
|---|---|---|
| A1 | $n$ が \eqref{eq:L0r} の $\bfI_n$ で初出、定義は第3節 | \eqref{eq:plant} 直後に $\bfx(k)\in\mathbb{R}^n$ |
| A2 | $\hat{\bfL}_P$ が \eqref{eq:mixedprod}, \eqref{eq:LMI} で定義前使用。$\bfL_{0P}$ は**未定義**。添字も不統一 | \eqref{eq:S2} 直後で $\bfL_{0P}\triangleq p_1\bfL_{01}+p_2\bfL_{02}$, $\hat{\bfL}_{0P}\triangleq\bfL_{0P}\otimes\bfI_n$ と定義し全体を $\hat{\bfL}_{0P}$ に統一 |
| A3 | $\xi_{i,1}(k)$, $\mathbb{P}(\cdot)$, $r_i$ が未定義 | それぞれ一言定義 |

### B. 定義そのものの問題

| # | 内容 | 状態 |
|---|---|---|
| **B1** | 「$(i,j)$ = from $i$ to $j$」だが、\eqref{eq:observer}・\eqref{eq:sublaplacian}・$\tilde{\bfLambda}_i$ の説明はすべて **$j\to i$**（受信側が $i$） | 要修正 |
| **B2** | $\bfGamma_s=\bfgamma_s\otimes\bfI_n/\sqrt{\lVert\bfgamma_s\rVert}$ は正規化されない（列ノルム $|\mathcal{V}_s|^{1/4}$） | 修正案提示済 |
| **B3** | $\bfGamma^\top(\bfe_i-\bfe_j)(\bfe_i-\bfe_j)^\top=\bfO$ の次元不一致＋「annihilates」の向きが逆 | 修正案提示済 |
| B4 | 「$\bfGamma_i$ is defined by (orth1),(orth2)」— 定義ではなく性質、添字 $i$ がノードと衝突 | 要修正 |
| B5 | $\mathcal{E}_r$ の対称性（$(i,j)\in\mathcal{E}_r\Leftrightarrow(j,i)\in\mathcal{E}_r$）が明記されていない | 一文追加推奨 |
| B6 | $\bfP_i$ がリッカチ解とリアプノフ解の2通りに定義。フィルタ型リッカチ解が保証するのは $\bar{\bfA}_i\bfP_i\bar{\bfA}_i^\top\prec\bfP_i$ で、$\bar{\bfA}_i^\top\bfP_i\bar{\bfA}_i\prec\bfP_i$ ではない | **要確認**（9/16 セッションの転置方向確認スクリプトが未実行のまま） |
| B7 | 「$S_2\preceq0$, $S_3\succeq0$」「$-\sigma S_2\succeq S_3$」はスカラーなので $\le,\ge$ | 要修正 |

**B2 修正案**
```latex
$\bfGamma_s \triangleq (\bfgamma_s/\lVert\bfgamma_s\rVert)\otimes \bfI_n$,
whose columns are orthonormal,
```

**B3 修正案（原文の構成を活かす最小修正）**
```latex
Since $\bfGamma^{\top}\{(\bfe_i-\bfe_j)(\bfe_i-\bfe_j)^{\top}\otimes\bfI_n\}=\bfO$
holds for any $(i,j)\in\mathcal{E}$, $\bfGamma^{\top}$ also annihilates
the graph Laplacian of any subgraph of $G$.
Therefore, $\bfGamma^{\top}\hat{\bfL}_{01}=\bfO$ and
$\bfGamma^{\top}\hat{\bfL}_{02}=\bfO$ are also satisfied.
```
（\eqref{eq:orth2} を $\bfH=\bfI_n$ で引用する短縮版も提示済み。原文の論理自体は正しく、必須の修正は次元と annihilates の向きの2点のみ）

### C. 記号の多重使用

| 記号 | 用法 | 提案 |
|---|---|---|
| $\sigma$ | スペクトル半径 / LMI 変数 | スペクトル半径を $\rho(\cdot)$ に |
| $\mathcal{N}$ | 正規分布 / 近傍集合 $\mathcal{N}_i$ | どちらかを変更 |
| $r$ | 領域添字 / しきい値 $r_1,r_2$ / 距離 $r_i$ | しきい値を $d_1,d_2$ に |
| $\oplus$ | 集合の非交和 | $\sqcup$ か「$\cup$, $\cap=\emptyset$」 |

### D. 軽微な誤記

- \eqref{eq:S2} 直後の `\left[\hat{\bfL}(k)] \right]` に余分な `]`
- 脚注 paremeters → parameters
- 例1 の「Sect.\,3」手書き → `\ref{sec:ProposedObserver}`
- ラベル `eq:Lambdacirc` → 記号を $\tilde{\bfLambda}$ にしたので改名推奨
- 序論 "apr igh probability" → "a high probability"

---

## 第4部　LaTeX 組版

### 4-1. `align` 内 `split` のエラー（$\mathring{\bfLambda}_i$ / $\tilde{\bfLambda}_i$ の式）

- `split` は1行に `&` を1つしか持てない → `Extra alignment tab`
- `align` 側の `&` の位置が行ごとに違うと左端が揃わない → `&\begin{split}` とする

**状態: 反映済み**（現行 tex は各行 `&` 1つ）

### 4-2. 長い文中数式で行間・単語間が伸びる（\eqref{eq:mixedprod} 直後）

原因は (1) 添字付き $\sum$ で行の高さが増える、(2) 改行できない長い文中数式で単語間が引き伸ばされる。
対策は **別行立て**（\eqref{eq:mixedprod} と `align` で並べる）。和の添字は $j:(i,j)\in\mathcal{E}$ → $j\in\mathcal{N}_i$ で短縮。応急処置は `\sum\nolimits` と `\allowbreak`。

---

## 第5部　残タスク

**必須（誤り）**
1. B1 エッジの向きの定義を「from $j$ to $i$」に
2. B2 $\bfGamma_s$ の正規化
3. B3 次元と annihilates の向き
4. 2-2 の「larger $\sigma$ → smaller」の符号の向き
5. B7 スカラー不等式の記号

**推奨**
6. A2 $\hat{\bfL}_{0P}$ の定義位置と記号統一（4-2 の組版修正と同時に）
7. A1, A3, B4, B5 の定義追加
8. 1-1 variance correction への言い換え
9. C 記号の多重使用の解消
10. D 誤字

**要確認**
11. B6 $\bfP_i$ の転置方向 — 実際にシミュレーションで使った $\bfP_i$ で $\bar{\bfA}_i^\top\bfP_i\bar{\bfA}_i-\bfP_i\prec0$ か数値確認
12. 2-1 の $p_r>0$ の記述位置 — 次元削減の正当化（零空間全体）の箇所で述べるよう移す
