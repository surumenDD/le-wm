# 論文のエビデンスベースの知見: LeWM の planning 手法

検証対象命題: 検証対象.md §C3 (planning cost の定義)、§C4 (CEM / MPC / planning horizon)、§C5 (SIGReg と TwoRoom の関係のうち planning cost 側に関わる部分)。

---

## 事実

### planning cost の定義 (C3)

- 出典: `paper.cleaned.md:154-160` (`source/.../3-method.tex` の `\subsection{Latent Planning}` セクションに対応)
- 原文:

> Planning is performed by optimizing the action sequence to minimize a terminal latent goal-matching objective:
>
> $\mathcal{C}(\hat{\mathbf{z}}_H) = \| \hat{\mathbf{z}}_H - \mathbf{z}_g \|_2^2, \quad \mathbf{z}_g = \mathrm{enc}_\theta(\mathbf{o}_g),$
>
> where $\hat{\mathbf{z}}_H$ is the predicted latent state at the end of the rollout and $\mathbf{z}_g$ is the latent embedding of the goal observation $\mathbf{o}_g$.

**訳**: Planning は、終端における latent ゴールマッチング目的関数を最小化するように行動列を最適化することで行う。
すなわち、cost $\mathcal{C}(\hat{\mathbf{z}}_H)$ は予測 latent 状態 $\hat{\mathbf{z}}_H$ とゴール embedding $\mathbf{z}_g$ の差の squared L2 ノルムで定義され、ゴール embedding $\mathbf{z}_g$ はゴール観測 $\mathbf{o}_g$ を encoder $\mathrm{enc}_\theta$ に通すことで得る。
ここで $\hat{\mathbf{z}}_H$ は rollout 末端における予測 latent 状態であり、$\mathbf{z}_g$ はゴール観測 $\mathbf{o}_g$ の latent embedding である。

- 関連事実:
    - cost は latent 空間における **squared L2 距離**であり、roll-out 最終状態 $\hat{\mathbf{z}}_H$ と goal embedding $\mathbf{z}_g = \mathrm{enc}_\theta(\mathbf{o}_g)$ の差のノルム二乗。
    - cost は terminal-only (時刻 $H$ のみで評価され、中間ステップで cost は累積しない)。
- 同セクションで定義される latent rollout:

> $\hat{\mathbf{z}}_{t+1} = \mathrm{pred}_\phi(\hat{\mathbf{z}}_t, \mathbf{a}_t), \quad \hat{\mathbf{z}}_1 = \mathrm{enc}_\theta(\mathbf{o}_1)$

**訳 (読み下し)**: 時刻 $t+1$ における予測 latent 状態 $\hat{\mathbf{z}}_{t+1}$ は、時刻 $t$ の latent 状態 $\hat{\mathbf{z}}_t$ と行動 $\mathbf{a}_t$ を predictor $\mathrm{pred}_\phi$ に通すことで得る。初期 latent 状態 $\hat{\mathbf{z}}_1$ は、初期観測 $\mathbf{o}_1$ を encoder $\mathrm{enc}_\theta$ に通すことで得る。

- 出典: `paper.cleaned.md:148-152`

### planning の最適化問題

- 出典: `paper.cleaned.md:163-166`
- 原文:

> $\mathbf{a}^*_{1:H} = \arg\min_{\mathbf{a}_{1:H}} \mathcal{C}(\hat{\mathbf{z}}_H)$

**訳 (読み下し)**: 最適な行動列 $\mathbf{a}^*_{1:H}$ は、行動列 $\mathbf{a}_{1:H}$ について cost $\mathcal{C}(\hat{\mathbf{z}}_H)$ を最小化するものとして定まる。

- planning は世界モデルパラメータを固定したまま行動列のみを最適化する有限ホライズン最適制御問題として定義される (`paper.cleaned.md:161`)。

### planning solver (CEM) (C4)

- 出典: 本文 `paper.cleaned.md:167-169` および appendix `paper.cleaned.md:286-303`、Implementation details `paper.cleaned.md:563-564`
- 本文での記述:

> which we solve using the Cross-Entropy Method (CEM), a sampling method that iteratively selects the best plan and updates the parameters of the sampling distribution with the statistics of the best plans.

**訳**: これを Cross-Entropy Method (CEM) を用いて解く。CEM は、最良の plan を反復的に選択し、最良 plan 群の統計量によってサンプリング分布のパラメータを更新するサンプリング手法である。

- CEM のハイパーパラメータ (本文側、`paper.cleaned.md:301-303`):
    - サンプル数: 300 candidate action sequences per iteration
    - 反復回数: 30 optimization steps
    - elites: top 30 候補
- Implementation details 側 (`paper.cleaned.md:563-564`) の具体値:

> At each planning step, CEM samples 300 candidate action sequences and optimizes them for a maximum of 30 iterations in *PushT* and 10 iterations in the other environments. At each iteration, the top 30 trajectories are retained to update the sampling distribution, and the initial sampling variance is set to 1.

**訳**: 各 planning ステップにおいて、CEM は 300 個の候補行動列をサンプリングし、*PushT* では最大 30 反復、その他の環境では 10 反復で最適化する。各反復では上位 30 軌跡をサンプリング分布の更新のために保持し、初期のサンプリング分散は 1 に設定する。

- すなわち、CEM の反復回数は PushT で 30、それ以外 (TwoRoom, OGBench-Cube, Reacher) で 10 と環境別に設定される。
- CEM 初期分布: Gaussian, $\mu = 0$, $\Sigma = I$ (`paper.cleaned.md:289`)。
- CEM 終了時の方策: 最良 action sequence、または最終分布 $\mu_T$ の最初の action (`paper.cleaned.md:324` Alg.cem の return 行)。

### MPC / replanning (C4)

- 出典: `paper.cleaned.md:168-169` 本文と `paper.cleaned.md:564` Implementation details
- 本文:

> To mitigate this effect, we adopt a Model Predictive Control (MPC) strategy: only the first $K$ planned actions are executed before replanning from the updated observation.

**訳**: この影響を緩和するため、Model Predictive Control (MPC) 戦略を採用する。すなわち、計画された行動列のうち先頭の $K$ ステップのみを実行し、その後、更新された観測から再計画する。

- Implementation details:

> The planning horizon is set to 5 steps, which corresponds to 25 environment timesteps due to the use of a frame skip of 5. We employ a receding-horizon Model Predictive Control (MPC) scheme with a horizon of 5, meaning that the entire optimized action sequence is executed before replanning. This configuration follows the setup used in [zhou2025dino-wm].

**訳**: planning horizon は 5 ステップに設定され、frame skip 5 を用いるため 25 environment timesteps に相当する。我々はホライズン 5 の receding-horizon Model Predictive Control (MPC) スキームを採用する。これは、最適化された行動列の全体を実行してから再計画することを意味する。本設定は [zhou2025dino-wm] で用いられた設定に従う。

- planning horizon $H = 5$ ステップ (= 25 environment timesteps、frame-skip 5)。
- 実行幅: 計画された全 5 ステップを実行した後に再計画 ("entire optimized action sequence is executed before replanning")。Implementation details の記述では $K = H = 5$ となっており、本文 §3.2 の一般記述 ("first $K$ planned actions") の $K$ を 5 で具体化していることになる。
- frame-skip = 5 は連続する 5 個の行動を 1 ブロックに束ねる設定 (`paper.cleaned.md:551`)。

### Evaluation budget と goal sampling (C4)

- 出典: `paper.cleaned.md:589`

> In *TwoRoom*, the evaluation budget is set to 150 steps and the goal state is sampled 100 timesteps in the future. In *PushT*, the evaluation budget is 50 steps and the goal is sampled 25 timesteps in the future. In *OGBench-Cube* and *Reacher*, the evaluation budget is 50 steps, and the goal is sampled 25 timesteps in the future.

**訳**: *TwoRoom* では evaluation budget は 150 ステップに設定され、ゴール状態は 100 timesteps 先からサンプリングされる。*PushT* では evaluation budget は 50 ステップ、ゴールは 25 timesteps 先からサンプリングされる。*OGBench-Cube* および *Reacher* では evaluation budget は 50 ステップ、ゴールは 25 timesteps 先からサンプリングされる。

| Env | Eval budget (steps) | Goal sampled (timesteps ahead) |
|---|---|---|
| TwoRoom | 150 | 100 |
| PushT | 50 | 25 |
| OGBench-Cube | 50 | 25 |
| Reacher | 50 | 25 |

### 訓練側 (encoder + predictor + SIGReg) との関係

- 出典: `paper.cleaned.md:111-139` (Training Objective)
- 訓練損失:

> $\mathcal{L}_{\mathrm{LeWM}} \triangleq \mathcal{L}_{\mathrm{pred}} + \lambda\,\mathrm{SIGReg}(\mathbf{Z}).$

**訳 (読み下し)**: LeWM の訓練損失 $\mathcal{L}_{\mathrm{LeWM}}$ は、予測損失 $\mathcal{L}_{\mathrm{pred}}$ と、係数 $\lambda$ を乗じた SIGReg 正則化項 $\mathrm{SIGReg}(\mathbf{Z})$ の和として定義される。

- $\mathcal{L}_{\mathrm{pred}} = \|\hat{\mathbf{z}}_{t+1} - \mathbf{z}_{t+1}\|_2^2$ (teacher-forcing, latent 空間の squared L2)。
- 既定値: $M = 1024$ random projections、$\lambda = 0.1$ (`paper.cleaned.md:139`)。
- 訓練時 cost (L2) と planning 時 cost (L2) は同形の squared L2 距離である点が、定義として直接観察できる。論文側で「planning 時の L2 cost が訓練時の L2 と同形であることを明示する独立の記述」は、現在確認した範囲では本文中には存在しない (両者が同形であるのは数式の対照から得られる事実)。

### SIGReg の定義 (C5)

- 出典: `paper.cleaned.md:123-131`、`paper.cleaned.md:255-282` (appendix SIGReg 節)
- 定義 (要点):
    - 潜在埋め込み $\mathbf{Z} \in \mathbb{R}^{N\times B \times d}$ を $M$ 個の単位ベクトル $\mathbf{u}^{(m)} \in \mathbb{S}^{d-1}$ に射影し、各 1 次元射影 $\mathbf{h}^{(m)} = \mathbf{Z} \mathbf{u}^{(m)}$ に対して Epps-Pulley 一次元正規性検定統計量 $T$ を計算し、それらを平均する:

> $\mathrm{SIGReg}(\mathbf{Z}) \triangleq \frac{1}{M} \sum_{m=1}^M T(\mathbf{h}^{(m)}).$

**訳 (読み下し)**: SIGReg は、$M$ 本のランダム単位方向それぞれへの 1 次元射影 $\mathbf{h}^{(m)}$ に対する Epps–Pulley 統計量 $T(\mathbf{h}^{(m)})$ を平均した値として定義される。

- 数学的性質 (appendix `paper.cleaned.md:276-282`):

> By Cramér–Wold, matching all 1D marginals implies matching the joint distribution, i.e., in the asymptotic limit over $M$ we have the following weak convergence result
> $\mathrm{SIGReg}(\mathbf{Z})\rightarrow 0 \iff \mathbb{P}_{\mathbf{Z}}\rightarrow N(0,\mathbf{I}).$

**訳**: Cramér–Wold 定理により、全ての 1 次元周辺分布が一致することは同時分布が一致することを含意する。すなわち、$M$ に関する漸近極限において以下の弱収束結果が得られる: $\mathrm{SIGReg}(\mathbf{Z}) \to 0$ であることと、$\mathbf{Z}$ の分布 $\mathbb{P}_{\mathbf{Z}}$ が標準正規分布 $N(0, \mathbf{I})$ に収束することは同値である。

- すなわち、SIGReg は潜在分布を $N(0, I)$ (等方ガウス) に近づける正則化として位置づけられる。

### planning cost と goal embedding に関する追加事実

- goal embedding $\mathbf{z}_g$ は encoder にゴール観測 $\mathbf{o}_g$ を通したものとして得られる (`paper.cleaned.md:158-159`)。
- 図キャプション `paper.cleaned.md:79-80` (fig:lewm) では同等の説明:

> Given an initial observation $\mathbf{o}_1$ and a goal $\mathbf{o}_g$, the world model learned in Fig.~2 performs planning in the LeWM latent space. The initial state embedding $\mathbf{z}_1$ and the goal embedding $\mathbf{z}_g$ are obtained from the encoder. The predictor then rolls out future latent states up to a horizon $H$. A latent cost between the final predicted state and the goal embedding guides a solver to optimize the action sequence.

**訳**: 初期観測 $\mathbf{o}_1$ とゴール $\mathbf{o}_g$ が与えられたとき、Fig. 2 で学習された world model は LeWM の latent 空間で planning を行う。初期状態 embedding $\mathbf{z}_1$ とゴール embedding $\mathbf{z}_g$ は encoder から得られる。次に predictor がホライズン $H$ までの将来 latent 状態をロールアウトする。最終予測状態とゴール embedding の間の latent cost が、solver による行動列最適化を誘導する。

- "latent cost" の具体形は本文中で前述の squared L2 (式 $\mathcal{C}$) のみが明示的に定義されている。他の cost 形式 (quasimetric, learned metric 等) は論文中に提案されていない。

### 訓練ハードウェア・実装

- 出典: `paper.cleaned.md:567`

> Both training and planning were performed on a single NVIDIA L40S GPU.

**訳**: 訓練と planning はいずれも単一の NVIDIA L40S GPU 上で実行された。

- 訓練・評価コード: [`stable-worldmodel`](https://github.com/rbalestr-lab/stable-worldmodel) (`paper.cleaned.md:566`)、[`stable-pretraining`](https://github.com/rbalestr-lab/stable-pretraining) (`paper.cleaned.md:552`)。
- 公式コード: <https://github.com/lucas-maes/le-wm> (`paper.cleaned.md:40`)。

---

## 現在検討中の点

- 論文中で「planning cost (squared L2) と SIGReg 由来の Gaussian 化との相互作用」を明示的に議論する記述は、現在確認した範囲では存在しない。`論文のエビデンスベースの知見_TwoRoom帰属.md` の記述 A・B・D は representation 分布側の話に留まり、cost 関数自体の選択を問題視する記述ではない。
- planning 時に goal $\mathbf{z}_g$ が encoder のみから得られ predictor を通らないか、predictor も介するかについて、本文での記述は encoder 経由 ($\mathbf{z}_g = \mathrm{enc}_\theta(\mathbf{o}_g)$) のみが明示されている (`paper.cleaned.md:158`)。
- CEM の元論文 [rubinstein2004cross] における用法に対する、本論文での変更点・拡張点は、現在確認した範囲では明示的に区別されて記述されていない。
