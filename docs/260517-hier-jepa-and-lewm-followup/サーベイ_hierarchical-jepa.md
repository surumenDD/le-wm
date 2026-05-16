# サーベイ: Hierarchical JEPA / 階層的世界モデル系論文

調査範囲: JEPA (Joint-Embedding Predictive Architecture) 系の階層的拡張、および隣接する hierarchical world model。Web 検索 (arXiv / Semantic Scholar / Google Scholar / Hugging Face Papers / OpenReview / プロジェクトサイト) で収集。

各論文の主張・実験結果は arXiv abstract / project page / HTML での確認に依拠し、再現実験は本タスクの範囲外。**「LeWM (arXiv:2603.19312) の Limitations が示唆する hierarchical 路線」に対する直接の組合せ可能性** を関連性として整理する。

---

## 事実: HiT-JEPA は世界モデルではない (記憶の照合結果)

- 「HIT-JEPA = Hierarchical Image Transformer JEPA」という記憶は誤り。実在する `HiT-JEPA` (arXiv:2507.00028v1) は GPS / 都市軌道データに対する trajectory similarity 用途の階層的 JEPA であり、planning / world model / navigation control は扱わない。
- **タイトル**: HiT-JEPA: A Hierarchical Self-supervised Trajectory Embedding Framework for Similarity Computation
- **著者**: Lihuan Li, Hao Xue, Shuang Ao, Yang Song, Flora Salim (University of New South Wales)
- **公開**: 2025-07 (LeWM より 約 8 ヶ月先行)
- **エグゼクティブサマリー**: 3 層 (point-level → segment-level → trajectory-level) の階層的 JEPA。上位層 attention が下位層への "top-down spotlight" として作用。データセットは Porto / T-Drive / GeoLife / Foursquare-TKY/NYC / AIS(AU)。下流タスクは trajectory similarity / clustering / travel-time estimation。
- **LeWM との関連性**: 直接の関連なし。座標シーケンス上で JEPA を多層に積む点は (x, y) 軌跡を扱う設定との形式的類似はあるが、planning 損失も action conditioning もない。
- **Hierarchical 軸**: 空間スケール (point → segment → route)。

---

## 事実: HWM (Hierarchical Planning with Latent World Models) — LeWM の 1 ヶ月後に出た本命系統

- **タイトル**: Hierarchical Planning with Latent World Models
- **arXiv ID**: 2604.03208v1
- **公開年月**: 2026-04-03 (LeWM 2603.19312 の約 21 日後)
- **著者**: Wancong Zhang, Basile Terver, Artem Zholus, Soham Chitnis, Harsh Sutaria, Mido Assran, Randall Balestriero, Amir Bar, Adrien Bardes, Yann LeCun, Nicolas Ballas (NYU / Meta FAIR / Mila / Brown)
- **発表会議**: arXiv preprint (cs.LG)
- **エグゼクティブサマリー**:
    - 単一の共有 latent 空間上で **時間スケールが異なる 2 つの world model** を学習。
    - 低レベル world model: primitive action ごとの 1 ステップ遷移を予測。
    - 高レベル world model: transformer-based action encoder が出力する macro action (latent action) を条件に長期遷移を予測。
    - 損失は teacher-forcing の L1 距離 $\|\hat{z}_{t_{k+1}} - z_{t_{k+1}}\|_1$ (高レベルは $\gamma_\text{roll}=0$ で multi-step rollout なし)。
    - Planning は CEM / MPPI で連続行動を最適化。高レベル planner が予測した最初の latent を低レベルの subgoal として渡す。
- **報告数値**: Push-T で d=25/50/75 horizon に対し 89/78/61%。Diverse Maze で D∈[9,12] で 95%、D∈[13,16] で 83%。Franka pick-and-place 70% (vs flat VJEPA2-AC 0%)。最大 4× planning-time compute 削減。
- **base world model**: PLDM / DINO-WM / VJEPA2-AC の 3 系統。
- **LeWM との関連性**:
    - 著者重複 (LeCun, Balestriero, Ballas, Bardes など Meta FAIR / NYU 系)。
    - PLDM・DINO-WM・VJEPA2-AC を「latent world model + 連続行動 + L1 損失」というインタフェースで扱うため、LeWM もこの条件を満たし、原理上 LeWM を HWM の base world model として差し替える形での適用が可能。
    - **TwoRoom 環境での実験は HWM 本文に記載なし**。Diverse Maze (PointMaze 系) は含む。
    - HWM 本文での LeWorldModel への明示引用の有無は WebFetch では検出できず (未確認、PDF full-text 精査が必要)。
- **Hierarchical 軸**: **時間スケール** (primitive action vs latent macro action)。空間 / 表現次元は階層化していない (共有 latent)。

---

## 事実: A Path Towards Autonomous Machine Intelligence (LeCun 2022) — H-JEPA 構想の出典

- **タイトル**: A Path Towards Autonomous Machine Intelligence (Version 0.9.2)
- **著者**: Yann LeCun (Meta / NYU Courant)
- **公開**: OpenReview, 2022-06-27 (arXiv 番号なし、position paper)
- **エグゼクティブサマリー**:
    - Configurator / Perception / World Model / Critic / Short-Term Memory / Actor から成る自律エージェントの設計図。
    - 中核は JEPA、その階層拡張として **H-JEPA (Hierarchical JEPA)** を提案。
    - 多レベル・多時間スケールでの予測アーキテクチャの設計図のみ提示し、具体的な訓練アルゴリズム・実験は含まない。
- **LeWM との関連性**: LeWM の Limitations が "hierarchical world modeling" を long-horizon planning 改善の方向として挙げる際の概念的出典。LeCun 自身が LeWM と本構想の双方に著者または上位アドバイザとして関与。LeWM は H-JEPA 構想の「単一スケール版」を end-to-end で動かしている。
- **Hierarchical 軸**: 概念上は時間スケール + 抽象度 (具体実装は未提示)。

---

## 事実: Director — Deep Hierarchical Planning from Pixels (Hafner 2022)

- **タイトル**: Deep Hierarchical Planning from Pixels
- **arXiv ID**: 2206.04114
- **公開年月**: 2022-06
- **著者**: Danijar Hafner, Kuang-Huei Lee, Ian Fischer, Pieter Abbeel (Google Brain / UC Berkeley)
- **発表会議**: NeurIPS 2022
- **エグゼクティブサマリー**:
    - Dreamer (RSSM) の latent 空間上で manager / worker の 2 階層 policy を学習。
    - Manager が VQ 化された latent subgoal を提案し、worker は subgoal 到達を内発報酬で学ぶ。
    - 評価環境: 3D maze (egocentric vision)、Atari、DMLab、視覚制御 (sparse reward)。
- **LeWM との関連性**:
    - JEPA ではなく **生成型 RSSM** ベース (reconstruction loss を使う)。
    - manager が subgoal を提案、worker が短期 horizon を解くという設計は、HWM の高レベル / 低レベル planner と直接対応。
    - LeWM の TwoRoom と類似の egocentric 視覚 maze 設定がある。
- **Hierarchical 軸**: 時間スケール + 行動抽象度 (worker が固定 K=8 ステップ、manager が K ステップごとに subgoal を更新)。

---

## 事実: Hieros (Mattes & Herbrich 2024)

- **タイトル**: Hieros: Hierarchical Imagination on Structured State Space Sequence World Models
- **arXiv ID**: 2310.05167 (v2: 2024)
- **公開年月**: 2023-10 / 2024
- **著者**: Paul Mattes, Rainer Herbrich (所属未確認)
- **発表会議**: ICML 2024
- **エグゼクティブサマリー**:
    - 各階層が独自の S5 (Simplified Structured State Space Sequence) world model と actor-critic を持ち、上位層が下位層に subgoal を提案する model-based RL。
    - S5 により訓練は並列、imagine は逐次。
    - Atari 100k で当時の SOTA。subgoal は image 空間にデコード可能 (説明可能性)。
- **LeWM との関連性**: JEPA ではなく S5 (生成的)。階層的 imagine の枠組みとして HWM と並べて参照される。LeWM とは別系統。
- **Hierarchical 軸**: 時間スケール + subgoal 抽象度。

---

## 事実: Puppeteer — Hierarchical World Models for Humanoid Control (Hansen 2024)

- **タイトル**: Hierarchical World Models as Visual Whole-Body Humanoid Controllers
- **arXiv ID**: 2405.18418 (v3: 2025-05)
- **公開年月**: 2024-05
- **著者**: Nicklas Hansen, Jyothir S V, Vlad Sobal, Yann LeCun, Xiaolong Wang, Hao Su (UC San Diego / NYU / Meta FAIR)
- **発表会議**: ICLR 2025
- **エグゼクティブサマリー**:
    - 56-DoF humanoid 制御で、上位 (puppeteer) が視覚を見て低次元 reference motion を出し、下位 (tracker) が joint-level で追従。
    - 両者とも TD-MPC ベースの world model を学習 (報酬あり)。
    - 8 タスクで強性能、人間評価でも好まれるモーション生成。
- **LeWM との関連性**: 著者に LeWM 系の Sobal / LeCun を含む。世界モデルは TD-MPC 系 (reconstruction なしの latent dynamics + reward + value)。報酬ありの hierarchical RL であり、LeWM の reward-free planning とは別流派。
- **Hierarchical 軸**: 行動次元 (high-level が low-dim reference motion、low-level が full DoF) + 時間スケール。

---

## 事実: V-JEPA 2 (Meta FAIR 2025) — flat だが HWM の base として登場

- **タイトル**: V-JEPA 2: Self-Supervised Video Models Enable Understanding, Prediction and Planning
- **arXiv ID**: 2506.09985
- **公開年月**: 2025-06
- **著者**: Mido Assran, Adrien Bardes (1st 連名), 計 28 名 (Meta FAIR)。Yann LeCun, Michael Rabbat, Nicolas Ballas 含む。
- **エグゼクティブサマリー**: 100 万時間超の動画で pretrain した V-JEPA 2 (encoder) と、Droid の <62h で post-train した action-conditioned 版 V-JEPA 2-AC (300M-param block-causal transformer)。Franka で zero-shot pick-and-place を実行。
- **LeWM との関連性**: 大規模 video pretraining 版の JEPA。HWM が VJEPA2-AC を base としており、これが LeWM と同列のレイヤ。
- **Hierarchical 軸**: なし (flat)。階層化は HWM 側で適用される。

---

## 事実: V-JEPA 2.1 (Meta FAIR 2026) — encoder 深さ方向の階層化

- **タイトル**: V-JEPA 2.1: Unlocking Dense Features in Video Self-Supervised Learning
- **arXiv ID**: 2603.14482
- **公開年月**: 2026-03 (LeWM の約 2 週間前)
- **著者**: Lorenzo Mur-Labadia, Matthew Muckley, Amir Bar, Mido Assran, Koustuv Sinha, Mike Rabbat, Yann LeCun, Nicolas Ballas, Adrien Bardes (Meta FAIR)
- **エグゼクティブサマリー**:
    - V-JEPA 2 を dense 表現に向けて改良。
    - **自己教師損失を encoder の複数中間層に階層的に適用する "deep self-supervision"**、masking 目的との併用、multi-modal tokenizer、スケール拡大により dense / global 両方の downstream で改善。
    - Ego4D / EPIC-KITCHENS / TartanDrive 等で SOTA。実機 grasping で V-JEPA 2 比 +20pt。
- **LeWM との関連性**: 時間方向ではなく **encoder 深さ方向の階層化**。LeWM の Limitations が示唆する「long-horizon planning のための時間階層」とは方向が異なる。LeWM の encoder 表現強化として組合せ可能だが planning horizon 問題には直接効かない。
- **Hierarchical 軸**: ネットワーク深さ (encoder layer index)。

---

## 事実: 隣接論文 (名称が紛らわしいが本質的に hierarchical world model ではない)

| 略称 | arXiv | 公開 | 著者代表 | 階層化の有無 |
|---|---|---|---|---|
| Causal-JEPA | 2602.11389 | 2026-02 | Nam / Le Lidec / Maes / LeCun / Balestriero | object-centric (空間 mask 単位)、階層なし |
| H-WM (TAMP) | 2602.11291 | 2026-02 | Huang et al. | symbolic + visual の階層、JEPA とは別系統 |
| BiJEPA | 2603.00049 | 2026-03 | - | symmetric 表現学習、階層なし |
| Variational JEPA | 2601.14354 | 2026-01 | - | 確率拡張、階層なし |
| Emergent temporal abstractions | 2512.20605 | 2025-12 | - | autoregressive model で temporal action が emerge することを示す論文。JEPA ベースではない |
| jonwiggins/H-JEPA | GitHub | - | community | 対応する arXiv 論文は存在しない (実装のみ) |

---

## 事実: LeWM の Limitations が指す「hierarchical world modeling」に該当する論文の優先順位 (発表時期・組合せ可能性で整理)

1. **HWM (2604.03208)** — LeWM の 1 ヶ月後、同系統 (LeCun / Ballas / Balestriero / Bardes 著)、PLDM / DINO-WM / VJEPA2-AC を base に temporal hierarchy + macro-action latent を導入。LeWM を base world model として差し替える形での適用が最小改修。
2. **Director (2206.04114)** — manager/worker の subgoal 階層を VQ-latent で実装。RSSM ベースなので JEPA への移植は非自明だが、subgoal 提案のアルゴリズム面で参照対象。
3. **Puppeteer (2405.18418)** — high-level が low-dim reference を発行する構造。TwoRoom (2D nav) には行動次元が 2 のため適用先としての差が大きい。
4. **Hieros (2310.05167)** — S5 ベースの hierarchical imagination。JEPA ではないが階層 imagine の枠組みとして比較対象。
5. **V-JEPA 2.1 (2603.14482)** — encoder 深さの階層化なので「long-horizon の時間階層」とは方向が異なる。encoder 強化としては独立に組合せ可能。

---

## 現在検討中の点

- **HWM 本文中の LeWorldModel への明示引用の有無**: WebFetch では検出できず。PDF full-text 精査または arXiv の cited-by 機能で確認する必要がある。
- **HWM の TwoRoom 環境での評価の有無**: 本文に TwoRoom 言及なし。Diverse Maze (PointMaze 系) は含むが、これと TwoRoom の関係は要確認。
- **HWM の high-level world model における TD-target 学習の挙動**: $\gamma_\text{roll}=0$ で multi-step rollout なし。LeWM TwoRoom (evaluation budget 150 steps, goal 100 timesteps 先) の long-horizon 設定で HWM の high-level horizon がどの程度長く取れるかは本文中の数値からは直接判断できない。
- **HWM の base として LeWM を差し替えた場合の性能**: 本論文中で実験されていない。実装上は stable-worldmodel ライブラリで両者を同列に扱う構造があるため移植は技術的にできる位置にあるが、性能の予測は本タスクの範囲外。
- **Hierarchical JEPA を直接 LeWM TwoRoom に当てる先行実装**: 確認できた範囲では HWM 以外に該当論文なし (jonwiggins/H-JEPA は対応論文がない community 実装)。
