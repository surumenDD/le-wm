# サーベイ: LeWorldModel (arXiv:2603.19312) の後続論文

調査範囲: arXiv:2603.19312 (LeWorldModel, Maes/Le Lidec/Scieur/LeCun/Balestriero, 2026-03-13) を引用する論文、および LeWM の TwoRoom 87% を直接の改善対象として扱う論文。

調査経路: Semantic Scholar citations API、Hugging Face Papers、Google Scholar、arXiv 検索。**alphaXiv** (https://www.alphaxiv.org/abs/2603.19312) は HTTP 502 で fetch 失敗 (未確認)、**OpenReview** での LeWorldModel 関連投稿は ICLR 2026 投稿期終了 / NeurIPS 2026 未投稿期のため検索ヒットなし。

Semantic Scholar 上で確認できた LeWM citing 論文は 2026-05 時点で **17 件**。

---

## 事実: LeWM の TwoRoom planning を直接改善する後続論文 (3 件、いずれも 2026-05 公開)

LeWM の **TwoRoom 87%** および **Wall** 環境を直接ベースラインとして比較し、planning success rate を改善した論文を以下に列挙する。

### 後続 1: RC-aux (Predictive but Not Plannable)

- **タイトル**: Predictive but Not Plannable: RC-aux for Latent World Models
- **arXiv ID**: 2605.07278
- **公開年月**: 2026-05-08
- **著者**: Wenyuan Li, Guang Li, Keisuke Maeda, Takahiro Ogawa, Miki Haseyama
- **発表会議**: 未確認
- **エグゼクティブサマリー**:
    - 問題提起: LeWM の latent space が "predictive but not plannable" である (= 予測には十分だが計画には latent が整っていない)。
    - 解決: Reachability-Correction auxiliary objective (RC-aux) を導入。
        - (1) multi-horizon open-loop prediction による **temporal 軸** の planning-aligned supervision。
        - (2) budget-conditioned reachability with temporal hard negatives による **spatial 軸** の planning-aligned supervision。
    - world-model backbone は変更せず、補助損失のみ追加。
- **LeWM との関係**: 直接の改善提案。LeWM をベースラインとして continuation-training と matched-from-scratch の両設定で比較。
- **TwoRoom / wall**: **両方あり**。
    - TwoRoom: LeWM 88.8±3.0% → **RC-aux 98.0±1.4%** (+9.2pp)。
    - Wall: 50.4±6.5% → **83.6±3.6%** (+33.2pp)。
    - Reacher: +4.4pp、OGBench-Cube: +3.2pp、Push-T: −0.4pp (飽和)。
- **既存 docs との関連**: `docs/260515-lewm-tworoom-factcheck/論文のエビデンスベースの知見_TwoRoom帰属.md` 記述 C 「planning gap は representation の情報量の不足によるものではなく、dynamics model or planning procedure 側」という帰属仮説に対する直接の応答研究。RC-aux は「planning 用途では representation 側にも補助構造が必要」と再解釈する立場。

### 後続 2: Sub-JEPA (Subspace Gaussian Regularization)

- **タイトル**: Sub-JEPA: Subspace Gaussian Regularization for Stable End-to-End World Models
- **arXiv ID**: 2605.09241
- **公開年月**: 2026-05-10
- **著者**: Kai Zhao, Dongliang Nie, Yuchen Lin, Zhehan Luo, Yixiao Gu, Deng-Ping Fan, Dan Zeng
- **発表会議**: 未確認
- **エグゼクティブサマリー**:
    - 問題提起: LeWM の SIGReg (高次元 ambient 空間での等方ガウス事前分布) が "overly restrictive" である。
    - 解決: ランダムに選んだ複数の subspace 上でガウス制約を課す。collapse 防止性は保ちつつ表現の柔軟性を上げる。
- **LeWM との関係**: 直接の改善提案 (SIGReg 置換)。
- **TwoRoom / wall**: **TwoRoom あり** (wall は記載なし、4 環境で評価)。
    - TwoRoom: LeWM 84.33±4.23 → **Sub-JEPA 95.00±2.76**。
    - Reacher: 82.67 → 84.00。
    - PushT: 84.67 → 89.00。
    - OGB-Cube: 67.33 → **76.33**。
- **コード**: https://github.com/intcomp/Sub-JEPA
- **既存 docs との関連**: `docs/260515-lewm-tworoom-factcheck/論文のエビデンスベースの知見_TwoRoom帰属.md` 記述 A・B・D 「TwoRoom 失敗 ← SIGReg と低 intrinsic dim の不整合」帰属仮説に直接対応する応答研究。SIGReg を subspace 化することで TwoRoom 84.33→95.00% を達成しており、記述 A/B/D の方向の介入が実際に planning gap を縮める根拠を提供する。
- **数値の注意**: Sub-JEPA の LeWM TwoRoom 数値 (84.33) は LeWM 論文の図 (87) および RC-aux の数値 (88.8) と異なる。3 seed 評価の不一致または評価プロトコルの差の可能性。

### 後続 3: GC-IDM (Latent Geometry Beyond Search)

- **タイトル**: Latent Geometry Beyond Search: Amortizing Planning in World Models
- **arXiv ID**: 2605.08732
- **公開年月**: 2026-05-09
- **著者**: Hoang Nguyen, Xiaohao Xu, Xiaonan Huang
- **発表会議**: 未確認
- **エグゼクティブサマリー**:
    - 問題提起: CEM / MPPI / iCEM / 勾配ベースなどの online search は遅い。
    - 解決: pretrained LeWM の latent geometry を活かし、planning を Goal-Conditioned Inverse Dynamics Model (GC-IDM) に amortize する。
    - GC-IDM は latent state, goal latent, remaining horizon から直接 next action を出力。
- **LeWM との関係**: planning 手続きの改善。LeWM を encoder/dynamics として使う前提で planner のみ置換。
- **TwoRoom / wall**: **TwoRoom あり** (wall は含まれず)。
    - TwoRoom: CEM 82.0% (n=50 samples) → **GC-IDM 100.0%**。
    - TwoRoom: CEM 84.0% (n=200 samples) → **GC-IDM 100.0%**。
    - per-decision コスト **100–130× 削減**。
- **既存 docs との関連**: `docs/260515-lewm-tworoom-factcheck/論文のエビデンスベースの知見_Planning手法.md` で整理した LeWM の CEM 設定 (300 candidate × 10 iterations for TwoRoom) を直接の対象とし、planner 部分のみを置換することで TwoRoom 100% を達成した報告。記述 C の「planning procedure 側」帰属仮説に対する直接の検証結果に該当する。

---

## 事実: LeWM 著者本人による派生・姉妹論文 (3 件)

### 姉妹 1: stable-worldmodel-v1

- **タイトル**: stable-worldmodel-v1: Reproducible World Modeling Research and Evaluation
- **arXiv ID**: 2602.08968 (v2: 2026-02-17)
- **公開年月**: 2026-02-09
- **著者**: Lucas Maes, Quentin Le Lidec, Dan Haramati, Nassim Massaudi, Damien Scieur, Yann LeCun, Randall Balestriero
- **エグゼクティブサマリー**:
    - LeWM 実験基盤となるオープンソース library。
    - 統合内容: data 収集、標準化環境 (TwoRoom を含む、17 個の factor of variation)、planning algorithms (CEM/iCEM/MPPI/SGD/Adam/PGD)、ベースライン (LeWM, C-JEPA, DINO-WM, PLDM, IVL, IQL, GCBC)。
    - DINO-WM の zero-shot robustness 解析を実例として提示。
- **LeWM との関係**: LeWM の評価基盤 (姉妹論文)。LeWM 論文中の "stable-worldmodel" GitHub repo (rbalestr-lab/stable-worldmodel) の公開時期および機能拡張の母体に該当。
- **リポジトリ**: https://github.com/galilai-group/stable-worldmodel
- **TwoRoom 含む**: あり (factor of variation 制御で robustness 評価可能)。

### 姉妹 2: Causal-JEPA

- **タイトル**: Causal-JEPA: Learning World Models through Object-Level Latent Interventions
- **arXiv ID**: 2602.11389
- **公開年月**: 2026-02-11
- **著者**: Heejeong Nam, Quentin Le Lidec, Lucas Maes, Yann LeCun, Randall Balestriero
- **エグゼクティブサマリー**:
    - patch ベース JEPA を object-centric に拡張。
    - object 単位 masking で latent intervention を誘導し、counterfactual 風効果を発生。
    - VQA で counterfactual reasoning が約 20pp 改善、agent control では patch-based の 1% の latent feature で同等性能。
- **LeWM との関係**: 著者重複の姉妹研究 (object-centric 系統)。

### 姉妹 3: LeJEPA (SIGReg の理論的根拠)

- **タイトル**: LeJEPA: Provable and Scalable Self-Supervised Learning Without the Heuristics
- **arXiv ID**: 2511.08544
- **公開年月**: 2025-11-11 (v1) → 2025-11-14 (v3)
- **著者**: Randall Balestriero, Yann LeCun
- **エグゼクティブサマリー**:
    - SIGReg (Sketched Isotropic Gaussian Regularizer) を導入した SSL 論文。
    - 等方ガウスが JEPA embedding の下流予測リスクを最小化する最適分布であることを示す。
    - stop-gradient / teacher-student / EMA / scheduler 不要。約 50 行で実装可。
    - ViT-H/14 で ImageNet-1K linear probe 79%。
- **LeWM との関係**: LeWM の SIGReg の理論的根拠を与える前駆論文 (LeWM の損失設計の心臓部)。

---

## 事実: LeWM を引用し TwoRoom / wall は使わない後続論文 (隣接領域、11 件)

| # | タイトル | arXiv | 公開 | 系統 |
|---|---|---|---|---|
| B-1 | Is the Future Compatible? Diagnosing Dynamic Consistency in World Action Models (Ruan et al.) | 2605.07514 | 2026-05 | rollout 信頼性指標 + value-free consensus。RoboCasa / RoboTwin 2.0 |
| B-2 | World Model for Robot Learning: A Comprehensive Survey (Hou et al., incl. Abbeel/Malik/Yilun Du/Yang) | 2605.00080 | 2026-05 | ロボット世界モデルの survey |
| B-3 | Latent State Design for World Models under Sufficiency Constraints (Kim) | 2605.01694 | 2026-05 | taxonomy paper |
| B-4 | Detecting is Easy, Adapting is Hard: Local Expert Growth for Visual MBRL (Zhao) | 2604.27411 | 2026-04 | JEPA-Indexed Local Expert Growth |
| B-5 | 3D-Anchored Lookahead Planning via World-Model-Based MCTS (Sidik & Mizrahi) | 2604.11302 | 2026-04 | MCTS + 3D-consistent WM |
| B-6 | ProteinJEPA: Latent Prediction Complements Protein LMs (Ofer et al.) | 2605.07554 | 2026-05 | タンパク質エンコーダへの JEPA 応用 |
| B-7 | AeroJEPA: 3D Aerodynamic Field Modeling (Giral et al.) | 2605.05586 | 2026-05 | 空力場 surrogate に JEPA |
| B-8 | 3D MRI Pretraining via Slice Navigation (Wang & Chen) | 2605.06487 | 2026-05 | 3D 医用画像へ action-conditioned latent dynamics |
| B-9 | Render, Don't Decode: NOVA (Nzoyem & Comi) | 2605.06298 | 2026-05 | weight-space world model |
| B-10 | Learning to Theorize the World (Baek et al.) | 2605.03413 | 2026-05 | Neural Theorizer (NEO) |
| B-11 | DySIB / Sonata / Infrastructure-Centric WM / IntentScore / Metriplector / Video-as-WM survey | 2604.* / 2605.* | 2026-04~05 | 各種 WM 応用 |

いずれも LeWM を主に "stable end-to-end JEPA training" の参照として引用しており、TwoRoom / wall 環境は使用していない。

---

## 事実: LeWM と同時期の隣接論文 (LeWM を引用していないが同問題系)

### 隣接 1: What Drives Success in Physical Planning with JEPA-WMs (Terver et al., ICLR 2026)

- **タイトル**: What Drives Success in Physical Planning with Joint-Embedding Predictive World Models?
- **arXiv ID**: 2512.24497 (v2: 2026-01-08)
- **公開年月**: 2025-12-30
- **著者**: Basile Terver, Tsung-Yen Yang, Jean Ponce, Adrien Bardes, Yann LeCun (Meta FAIR)
- **発表会議**: ICLR 2026 (OpenReview forum=TuYC5Fpp7M)
- **エグゼクティブサマリー**:
    - JEPA-WM の technical design choice をアブレーション。
    - model architecture / training objective / planning algorithm を体系比較。
    - navigation / manipulation で DINO-WM と V-JEPA-2-AC を上回るモデルを提案。
- **LeWM との関係**: 時系列で LeWM より前 (2025-12) のため LeWM 引用なし。JEPA-WM の planning 解析という意味で隣接論文。
- **コード**: https://github.com/facebookresearch/jepa-wms
- **著者の重複**: Basile Terver は HWM の著者にも含まれる。

### 隣接 2: Temporal Straightening for Latent Planning (Wang/Bounou/Balestriero 2026-03)

- **タイトル**: Temporal Straightening for Latent Planning
- **arXiv ID**: 2603.12231
- **公開年月**: 2026-03-12 (LeWM 公開の 1 日前)
- **著者**: Ying Wang, Oumayma Bounou, Gaoyue Zhou, Randall Balestriero, Tim G. J. Rudner, Yann LeCun, Mengye Ren
- **エグゼクティブサマリー**:
    - 人間視覚に着想を得た curvature regularizer で latent trajectory を直線化。
    - Euclidean 距離が測地距離をよく近似するよう encoder / predictor を訓練。
- **LeWM との関係**:
    - Bounou (Value-guided JEPA 著者) + Balestriero (LeWM 著者) の共著。両グループの橋渡し位置にある研究。
    - Value-guided JEPA の "latent 距離 ≈ value" を trajectory 全体に拡張した方向性。
    - LeWM 引用は未確認 (公開日が 1 日違いのため引用関係は時系列的に発生しないが、stable-worldmodel ベンチマークでの評価の有無は未確認)。

### 隣接 3: Closing the Train-Test Gap in World Models for Gradient-Based Planning (Parthasarathy/Bounou/LeCun 2025-12)

- **タイトル**: Closing the Train-Test Gap in World Models for Gradient-Based Planning
- **arXiv ID**: 2512.09929
- **公開年月**: 2025-12-10
- **著者**: Arjun Parthasarathy, Nimit Kalra, Rohun Agrawal, Yann LeCun, Oumayma Bounou, Pavel Izmailov, Micah Goldblum
- **エグゼクティブサマリー**:
    - world model は next state 予測で訓練されるが planning 時には action 列推定に流用される、というギャップを **train-time data synthesis** で埋める。
    - CEM 並み or 超の成績を 10% の計算時間で達成。
- **LeWM との関係**: gradient-based planning + JEPA 系 world model の改善。Value-guided JEPA の直近の姉妹研究。

### 隣接 4: World Action Verifier (Liu et al. 2026-04)

- **タイトル**: World Action Verifier: Self-Improving World Models via Forward-Inverse Asymmetry
- **arXiv ID**: 2604.01985
- **公開年月**: 2026-04-02
- **著者**: Yuejiang Liu, Fan Feng, Lingjing Kong, Weifeng Lu, Jinzhou Tang, Kun Zhang, Kevin Murphy, Chelsea Finn, Yilun Du
- **エグゼクティブサマリー**: world model の self-correction として、action-conditioned 状態予測を state plausibility と action reachability に分解検証。MiniGrid / RoboMimic / ManiSkill 9 タスクで 2× sample efficiency、+18% downstream policy 性能。
- **LeWM との関係**: LeWM 引用なし。reachability 概念で RC-aux と類似。

### 隣接 5: PiJEPA (Chahe & Zhou 2026-03)

- **タイトル**: Policy-Guided World Model Planning for Language-Conditioned Visual Navigation
- **arXiv ID**: 2603.25981
- **公開年月**: 2026-03-26
- **著者**: Amirhosein Chahe, Lifeng Zhou
- **エグゼクティブサマリー**: Octo policy + 凍結 DINOv2 / V-JEPA-2 で生成した action distribution で MPPI を warm-start するハイブリッド navigation planner。
- **LeWM との関係**: LeWM 引用なし。同時期同系統。CAST navigation dataset。

---

## 事実: TwoRoom 87% 失敗の改善方向 (後続論文の分類)

LeWM の TwoRoom 弱さは後続論文で広く認識されており、原因仮説と対処法が大きく 3 系統に分かれる。

1. **SIGReg の高次元等方ガウス前提が低次元タスクに不適**: Sub-JEPA (2605.09241) は subspace 制約で TwoRoom 84.33 → 95.00% を達成。`260515` 記述 A/B/D 帰属仮説の方向。
2. **latent space は plannable でない (representation-planning gap)**: RC-aux (2605.07278) は reachability 補助損失で TwoRoom 88.8 → 98.0%、Wall 50.4 → 83.6% を達成。`260515` 記述 C 帰属仮説 (planning procedure 側) を一部肯定しつつ、representation 側の補助構造で対処。
3. **CEM 自体が非効率**: GC-IDM (2605.08732) は CEM を inverse dynamics で置換し TwoRoom 82 → 100%、100–130× 高速化。`260515` 記述 C 帰属仮説 (planning procedure 側) の直接の方向。

3 系統いずれも LeWM 公式実装をそのまま使う形での改善であり、再現実験の後段として直接接続可能な構造を持つ。

---

## 現在検討中の点

- **Sub-JEPA / RC-aux / GC-IDM が報告する LeWM TwoRoom ベースライン数値の不一致**:
    - LeWM 論文 fig:ctrl-two-room: 87%
    - RC-aux 2605.07278: 88.8±3.0%
    - Sub-JEPA 2605.09241: 84.33±4.23%
    - GC-IDM 2605.08732: 82.0% (n=50) / 84.0% (n=200)
    - これらの差の要因候補として、3 seed 評価の不一致、CEM サンプル数 / iteration 数の違い、stable-worldmodel ライブラリ側の更新による評価プロトコル変更などがあるが、各論文の付録での評価設定確認は本タスクの範囲外。
- **alphaXiv (https://www.alphaxiv.org/abs/2603.19312) のコメント / 関連論文**: HTTP 502 で fetch 失敗。後日再アクセスが必要。
- **HWM (2604.03208) からの LeWM 引用の有無**: §サーベイ_hierarchical-jepa.md §HWM と同じ未確認事項。
- **stable-worldmodel-v1 (2602.08968)** の TwoRoom factor-of-variation 評価が LeWM 論文中の単一 TwoRoom 設定と一致するか: stable-worldmodel が "17 個の factor of variation" を提供する一方で、LeWM 論文中の TwoRoom 評価は固定 1 設定。両者の対応関係は未確認。
- **Temporal Straightening (2603.12231)** が LeWM TwoRoom 環境で評価されているかは未確認。abstract レベルでは特定環境名なし。
