# 用語集 (論文 2601.00844 のオリジナル用語 / 専門用語)

論文 `Value-guided action planning with JEPA world models` (Destrade et al., arXiv:2601.00844v1) で出現する論文オリジナル用語・専門用語・略称のうち、本タスクの docs を理解するうえで参照を必要とするものを記載する。各項目は論文中の出典 (原典での定義位置) を併記する。

なお、JEPA, MPC, CEM など `docs/260515-lewm-tworoom-factcheck/用語集old.md` で既に定義済みの用語については重複定義しない。本ファイルでは本論文に固有なもの、または本論文での用法を新規に定義するものに限定する。

## E

### Expectile regression / expectile loss
- 出典: `paper.cleaned.md:56`, `source/Method.tex:17`
- 定義: $L_\tau^2(x) = |\tau - \mathbf{1}_{x<0}| \, x^2$。$\tau = 0.5$ で対称な二乗誤差、$\tau \to 1$ で正の残差を強く重み付けする非対称損失。
- IQL の核となる回帰損失。Quantile regression の二乗版 / 非対称二乗誤差として捉えられる。

## H

### HILP
- 出典: `paper.cleaned.md:34`、文献 `[hilp]`
- 「IQL で目標条件付き価値関数を学習し、表現空間で『負ユークリッド距離 ≈ 価値関数』とする」手法を提案した先行研究の略称。本論文の表現空間整形のアイデアの直接的な出典。
- 本論文との差: HILP は表現を方策実行 (policy execution) に使うのに対し、本論文は JEPA + MPC の planning コストに使う (`paper.cleaned.md:38`)。

## I

### IQL (Implicit Q-Learning)
- 出典: `paper.cleaned.md:32`、文献 `[kostrikov2021offlinereinforcementlearningimplicit]` 他
- expectile regression を用いてオフラインデータから (目標条件付き) 価値関数を学習する手法。ラベルなしの軌道から学習可能。本論文の状態エンコーダ学習目的 $\mathcal{L}_\text{VF}$ の基盤。

## L

### $\mathcal{L}_\text{contrastive}$
- 出典: `paper.cleaned.md:42`, `source/Method.tex:6`
- 訓練軌道中の連続状態を正例、ランダム状態対を負例とする対照損失。本論文中で baseline。

### $\mathcal{L}_\text{regressive}$
- 出典: `paper.cleaned.md:42`, `source/Method.tex:6`
- 連続状態間の表現距離を 1 に強制する回帰損失。本論文中で baseline。Regressive 行では $\mathcal{L}_\text{VCReg}$ と組み合わせて使われる (`source/Results.tex:29`)。

### $\mathcal{L}_\text{VF}$
- 出典: `paper.cleaned.md:48-57`, `source/Method.tex:12-16` (Eq. 1)
- 本論文が提案する状態エンコーダ学習目的。expectile loss + 1-step TD residual で目標条件付き価値関数 $V^\star$ を回帰する。

## M

### MPPI (Model Predictive Path Integral)
- 出典: `paper.cleaned.md:83`, `source/Results.tex:66`
- サンプリングベースの MPC オプティマイザ。本論文では wall / maze 環境共に planning solver として使用。
- 設定 (`paper.cleaned.md:145`):
    - wall: 2000 サンプル、std 12、温度 $\lambda = 0.005$。
    - maze: 500 サンプル、std 5、温度 $\lambda = 0.0025$。
- 詳細式は本論文に記載なく、`[sobal2025learningrewardfreeofflinedata]` のコードベースに依拠することが示唆される。

## P

### pred\_EMA
- 出典: `paper.cleaned.md:79`, `source/Results.tex:33`
- 状態エンコーダを EMA (exponential moving average) スキームで学習する標準的な JEPA 手法。本論文中の baseline。

### pred\_VCReg
- 出典: `paper.cleaned.md:79`, `source/Results.tex:31`
- 状態エンコーダを VCReg loss と予測損失で同時学習する標準的な JEPA 手法 (PLDM 系)。本論文中の baseline。

## Q

### Quasimetric / Quasi-distance
- 出典: `paper.cleaned.md:59`, `source/Method.tex:20`、文献 `[wang2023optimalgoalreachingreinforcementlearning, wang2022improved]`
- 一般に対称性 $d(x, y) = d(y, x)$ を満たさない「距離」関数。目標条件付き価値関数の非対称性をモデル化するために導入される。本論文の `VF_quasi` 系手法で $V_\theta$ の定義における $\|\cdot\|_2$ を置換するために使われる。
- 具体的な数式形は本論文に転記されず、`[wang2022improved]` で導入された generic form を参照のみ。

## S

### Sep (separate training)
- 出典: `paper.cleaned.md:61`, `source/Method.tex:22`
- 本論文オリジナルの訓練手順名。状態エンコーダのみを先に $\mathcal{L}_\text{VF}$ で学習し、その後で行動エンコーダ・予測器を $\mathcal{L}_\text{pred}$ で学習する 2 段階手順。
- Table 1 (`tab:exemple`) の Sep カラムが ✓ の行に対応 (Contrastive, Regressive, VF, VF\_quasi, VF\_VCReg)。

## V

### $V^\star$ (goal-conditioned value function)
- 出典: `paper.cleaned.md:46`, `source/Method.tex:9`
- 到達コスト $C: (s, a, g) \mapsto \mathbf{1}_{s \neq g}$ に対応する最適な目標条件付き価値関数。$s = g$ となるまで毎ステップ -1 のペナルティを与える "reaching cost" 設定。

### $V_\theta$ (parameterized value function)
- 出典: `paper.cleaned.md:46`, `source/Method.tex:9`
- $V_\theta(s, g) = -\Vert \mathcal{E}_\theta(s) - \mathcal{E}_\theta(g) \Vert_2$ (Euclidean 版)、または $\|\cdot\|_2$ を quasimetric に置換した形 (`VF_quasi`)。$V^\star$ の近似器として学習される。

### VCReg (Variance-Covariance regularization)
- 出典: `paper.cleaned.md:26` 他、文献 `[bardes2022vicreg]` (本論文中 `\mathcal{L}_\text{VCReg}` として参照)
- JEPA の anti-collapse 機構として PLDM 系で用いられる。本論文では `pred_VCReg` / `Regressive` / `VF_VCReg` 系統で部分的に併用される。

### VF / VF\_quasi (本論文中の手法呼称)
- 出典: `paper.cleaned.md:79` (Table 1), `source/Results.tex:35, 49`
- VF: $V_\theta$ をユークリッド距離で構成し、Sep で学習。
- VF\_quasi: $V_\theta$ をクエスケスト距離 (quasimetric) で構成し、Sep で学習。
- `VF_pred` / `VF_quasi_pred`: 同じ目的だが Sep ✗ (= joint training)。
- `VF_VCReg` / `VF_VCReg_pred`: $\mathcal{L}_\text{VF}$ に VCReg を加える系統。

## W

### Wall environment (WS / WB)
- 出典: `paper.cleaned.md:69`, `source/main.tex:103-127`
- ドア付きの壁で 2 つの部屋に分割された連続 2D 環境。エージェントは開始位置から壁の反対側の目標へ移動する。
- WS: 小さな行動ノルム (平均 1 pixel、std 0.4)、WB: 大きな行動ノルム (平均 2 pixel、std 0.8)。
- 注意: `docs/260515-lewm-tworoom-factcheck` で扱った `TwoRoom` 環境とは別環境 (出典文献 `[sobal2025stresstesting]` vs `[sobal2025learningrewardfreeofflinedata]` の違い、ドア位置のランダム化の有無)。両環境の異同自体は事実照合の対象 (`推測作業プラン.md` 側で扱う)。

## その他

### Maze environment (本論文 §4.1)
- 出典: `paper.cleaned.md:71`, `source/main.tex:129-135`
- Mujoco PointMaze ベース、$4 \times 4$ グリッドの $50$–$60\%$ を迷路マスとする。慣性をシミュレートするため速度を状態に含める。
