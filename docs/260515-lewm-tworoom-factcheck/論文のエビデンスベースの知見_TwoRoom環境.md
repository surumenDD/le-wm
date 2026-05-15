# 論文のエビデンスベースの知見: TwoRoom 環境の仕様

検証対象命題: 検証対象.md §C9 (TwoRoom 環境のデータセット仕様 / 評価条件 / predictor history length / intrinsic dim の扱い)。

---

## 事実

### TwoRoom 環境の定義

- 出典: `paper.cleaned.md:572` (= `source/appendix.tex:327`)
- 原文:

> **TwoRoom** is a simple continuous 2D navigation task introduced by [sobal2025stresstesting]. The environment consists of two rooms separated by a wall with a single door connecting them. The agent (represented as a red dot) must navigate from a random starting position in one room to a randomly sampled target location in the other room, which requires passing through the door. We collect 10,000 episodes with an average trajectory length of 92 steps. The data are generated using a simple noisy heuristic policy that first directs the agent toward the door along a straight-line path and then toward the target location once the agent has crossed into the other room. Each world model is trained on this dataset for 10 epochs.

**訳**: **TwoRoom** は [sobal2025stresstesting] によって導入された単純な連続 2D ナビゲーションタスクである。本環境は、ひとつのドアでつながった壁によって隔てられた二部屋から成る。エージェント (赤い点で表される) は、一方の部屋のランダムな開始位置から、もう一方の部屋でランダムにサンプリングされたターゲット位置までナビゲートする必要があり、これはドアを通過することを要求する。我々は平均軌跡長 92 ステップの 10,000 エピソードを収集する。データは単純なノイジーヒューリスティック方策によって生成される。この方策は、まずエージェントをドアに向けて直線経路で誘導し、エージェントが別の部屋に渡った後はターゲット位置に向けて誘導する。各 world model はこのデータセット上で 10 epoch 訓練される。

- 環境の出典: PLDM 系の論文 [sobal2025stresstesting]。LeWM 論文は本環境を新規導入していない。
- 構造: 2D 連続ナビゲーション、二つの部屋を 1 つのドアで連結する壁構造。
- 行動空間: 連続 (`paper.cleaned.md:176` の図キャプションで「All environments have a continuous action space」)。
- データ規模: 10,000 episodes、平均軌跡長 92 steps。
- 軌跡生成方策: noisy heuristic。エージェントを直線経路でドアに向かわせ、別の部屋に渡った後ターゲットに向かわせる。
- 訓練 epoch: 10。

### 評価条件 (再掲、`論文のエビデンスベースの知見_Planning手法.md` §Evaluation budget と goal sampling と同じ)

- 出典: `paper.cleaned.md:589`
- TwoRoom: evaluation budget = 150 steps、goal は 100 timesteps 先からサンプリング。
- 比較として他環境 (`paper.cleaned.md:589`):
    - PushT: 50 steps、25 timesteps 先
    - OGBench-Cube: 50 steps、25 timesteps 先
    - Reacher: 50 steps、25 timesteps 先

### Predictor history length

- 出典: `paper.cleaned.md:558` (= `source/appendix.tex:311`)
- 原文:

> The predictor is implemented as a ViT-S backbone with learned positional embeddings and causal masking over the observation history. The history length is set to 3 for the *PushT* and *OGBench-Cube* environments, and to 1 for *TwoRoom*.

**訳**: predictor は、学習された positional embedding と観測履歴上の causal masking を持つ ViT-S backbone として実装される。history length は *PushT* および *OGBench-Cube* 環境では 3、*TwoRoom* では 1 に設定される。

- すなわち TwoRoom では predictor が受け取る過去観測の長さ $N = 1$、その他環境では $N = 3$。
- Reacher の history length については当該箇所では明示されていない (PushT・Cube・TwoRoom の 3 つのみ言及)。

### Two-Room と他環境の planning 結果の関係 (再掲)

- 詳細は `論文のエビデンスベースの知見_数値表.md` §TwoRoom 〜 §OGBench-Cube を参照。
- LeWM は TwoRoom (87) で他のすべての評価ベースラインに対して劣位、PushT・Reacher で最高、OGBench-Cube では DINO-WM に対して劣位 (LeWM 74 vs DINO-WM 86)。

### Violation-of-expectation 評価 (TwoRoom 含む)

- 出典: `paper.cleaned.md:238-242` (= `source/sections/4-exp.tex:140-155`)、appendix `paper.cleaned.md:614-630`
- TwoRoom でも VoE 評価が行われ、エージェント色変更 (visual) と teleport (physical) の 2 種の摂動が導入される。
- 本文 (`paper.cleaned.md:238`) で「Surprise is significantly higher for teleportation perturbations across all three environments (paired t-test, $p<0.01$)」と記述される。

### Probing 評価 (TwoRoom)

- 詳細は `論文のエビデンスベースの知見_数値表.md` §TwoRoom probing。
- probed 物理量は agent の 2D 位置のみ (`paper.cleaned.md:598`)。

---

## 現在検討中の点

- 論文中で TwoRoom 環境の intrinsic dimensionality が数値として測定・報告されているか否かは、現在確認した範囲では確認できなかった。本文中の表現は「the intrinsic dimensionality of the environment is much lower」(`paper.cleaned.md:199`, fig:ctrl-all キャプション) や「low intrinsic dimensionality」(`paper.cleaned.md:187, 248`) と定性的にのみ記述される。
- TwoRoom の壁の物理 (透過不可、ドアのみ通過可能) について、本文中の幾何学的説明 (位相、ユークリッド vs 非ユークリッド、geodesic distance) は現在確認した範囲では明示されていない。
- TwoRoom データセット (10,000 episodes) の生成元 (PLDM 公式コード由来か独自再生成か) は本文では明示されておらず、引用 [sobal2025stresstesting] に依拠する旨のみ。
