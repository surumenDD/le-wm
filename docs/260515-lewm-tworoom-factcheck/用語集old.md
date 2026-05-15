# 用語集 (論文オリジナル用語 / 専門用語)

論文 `2603.19312v2 LeWorldModel` および外部レビュー文書中に出現する、論文オリジナル用語・専門用語・略称のうち、本タスクの docs を理解するうえで参照を必要とするものを記載する。各項目は論文中の出典 (原典での定義位置) を併記する。

ここに記載した用語のみ、本タスク内の docs で前置きなしに利用してよい。それ以外の独自略称 (例: 外部レビュー文書中の `re-le-wm-Q`、「戦略 D」「Phase 1 W2」など) は、引用以外の用途では使用しない。

## J

### JEPA (Joint Embedding Predictive Architecture)
- 出典: `paper.cleaned.md:52, 72`、初出文献 `[lecun2022path]`
- 観測を低次元潜在空間に符号化し、将来観測の潜在表現を予測する世界モデルの枠組み。代表例として I-JEPA (画像), V-JEPA (動画), PLDM, DINO-WM が挙げられる。LeWM 自身もこの枠組みに属する。

## L

### LeWM (LeWorldModel)
- 出典: `paper.cleaned.md:2, 56` 他多数
- 本論文が提案するメソッド。end-to-end で raw pixel から JEPA を学習する手法。encoder + predictor を MSE 予測損失と SIGReg 正則化の 2 項のみで学習する。

### Latent L2 cost
- 本タスク内表記 (論文には固有名ではなく式 $\mathcal{C}(\hat{\mathbf{z}}_H)=\|\hat{\mathbf{z}}_H - \mathbf{z}_g\|_2^2$ として記述、`paper.cleaned.md:157`)
- planning 時に最終予測潜在状態とゴール潜在埋め込みの squared L2 距離を最小化する cost 関数。

## C

### CEM (Cross-Entropy Method)
- 出典: `paper.cleaned.md:167, 284-303`、文献 `[rubinstein2004cross]`
- サンプリングベースのゼロ次最適化手法。候補プランを Gaussian からサンプリングし、上位 K 個 (elites) の統計でサンプリング分布を更新することを反復する。本論文では LeWM planning の solver として使用。

### Cramér–Wold theorem
- 出典: `paper.cleaned.md:125, 279`、文献 `[cramer1936some]`
- 多次元分布の全ての 1 次元射影の周辺分布が一致するならば、もとの多次元分布が一致する、という古典的結果。SIGReg の正当化に使用される。

## D

### DINO-WM
- 出典: `paper.cleaned.md:72, 329-340`、文献 `[zhou2025dino-wm]`
- DINOv2 で事前学習された frozen encoder を用いる world model。collapse 回避のため encoder を凍結し、predictor のみを次潜在予測 MSE で学習する。本論文の baseline。

### DINOv2
- 出典: `paper.cleaned.md:181` 他、文献 `[oquab2024dinov]`
- 大規模事前学習された Vision Transformer。DINO-WM の encoder として使用される。論文中では「approximately 124M images で事前学習」と記述 (`paper.cleaned.md:213`)。

## E

### Epps–Pulley test statistic
- 出典: `paper.cleaned.md:125, 269-274`、文献 `[epps1983test]`
- 一変量正規性検定。経験特性関数と標準正規の特性関数の重み付き L2 差で定義される。SIGReg の 1 次元正規性評価コンポーネント。

## G

### GCBC (Goal-Conditioned Behavioral Cloning)
- 出典: `paper.cleaned.md:531-547`、文献 `[ghosh2019learning]`
- ゴール観測条件付きの教師あり模倣学習ベースライン。観測とゴールを DINOv2 patch 埋め込みで符号化し、MSE で行動を再現する。

### GCIQL / GCIVL
- 出典: `paper.cleaned.md:440-530`、文献 `[kostrikov2021offline]`, `[park2025ogbench]`
- それぞれ goal-conditioned Implicit Q-Learning と goal-conditioned Implicit Value Learning。本論文のオフライン RL ベースライン。

## M

### MPC (Model Predictive Control)
- 出典: `paper.cleaned.md:77, 84, 168, 564`、文献 `[testud1978model]`
- 予測モデルを使った逐次最適制御。本論文では receding-horizon MPC として、CEM で得た行動列を実行した後に観測を更新して再計画する手法を用いる。

## P

### PLDM
- 出典: `paper.cleaned.md:72, 342-438`、文献 `[sobal2025stresstesting]`
- VICReg ベースの正則化を含む 7 項損失で end-to-end に JEPA を学習する手法。本論文の baseline。

### Probing (linear / MLP probe)
- 出典: `paper.cleaned.md:594-598`、文献 `[bardes2023v]` 系
- 凍結した表現から物理量を予測する線形・非線形回帰器を訓練し、その精度で表現の情報量を評価する手順。本論文では各環境で tab:probe-* として報告される。

## S

### SIGReg (Sketched Isotropic Gaussian Regularizer)
- 出典: `paper.cleaned.md:123, 255-282`、文献 `[balestriero2025lejepa]`
- 潜在埋め込みを $M$ 本のランダム単位方向に射影し、各 1 次元射影に Epps-Pulley 検定統計量を適用して平均する正則化項。$M \to \infty$ の極限で潜在分布の $N(0, I)$ への weak convergence と等価。LeWM の anti-collapse 機構。

## T

### TwoRoom (Two-Room)
- 出典: `paper.cleaned.md:176, 572`、文献 `[sobal2025stresstesting]`
- 壁とドアで連結された 2 部屋からなる連続 2D ナビゲーション環境。本論文では「the simplest environment」として参照される。

## V

### VICReg
- 出典: `paper.cleaned.md:72, 344`、文献 `[bardes2022vicreg]`
- 自己教師あり表現学習の正則化。Variance / Invariance / Covariance の 3 項からなる。PLDM の損失設計の基盤。

### VoE (Violation of Expectation)
- 出典: `paper.cleaned.md:240`、文献 `[margoni2024violation, garrido2025intuitive, bordes2025intphys2]`
- 学習した world model が物理的に妥当でない事象に対して高い surprise を割り当てるかを評価する枠組み。本論文では TwoRoom / PushT / OGBench-Cube で実施。

## その他

### Frame skip
- 出典: `paper.cleaned.md:551`
- 連続する複数行動を 1 ブロックにまとめる処理。本論文では frame-skip = 5。1 計画ステップ = 5 environment timesteps に対応。

### Receding horizon
- 出典: `paper.cleaned.md:564`
- MPC の標準的な実行パターン。最適化したホライズン $H$ の行動列のうち最初の $K$ ステップを実行し、状態を更新して再計画する。LeWM 既定設定では $K = H = 5$。
