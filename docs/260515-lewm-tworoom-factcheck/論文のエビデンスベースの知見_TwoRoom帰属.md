# 論文のエビデンスベースの知見: TwoRoom 失敗の帰属に関する論文記述

検証対象命題: 検証対象.md §C2 「論文中で TwoRoom 失敗を説明している記述箇所は何箇所あり、それぞれ何に帰属させているか」、および §C5 (Limitations 中の SIGReg と TwoRoom の関係)。

論文中で TwoRoom における LeWM の planning gap (LeWM 87 vs PLDM 97 vs DINO-WM 100、参照: `論文のエビデンスベースの知見_数値表.md`) を説明する記述は、本文・図キャプション・appendix テーブルキャプション・Conclusion の Limitations の合計 4 箇所に存在する。それぞれの引用と帰属先を以下に列挙する。

---

## 事実

### 記述 A: §Towards Efficient Planning with WMs 本文

- 出典: `source/sections/4-exp.tex:22` (対応する Markdown 文字起こしは `paper.cleaned.md:187`)
- 原文 (LaTeX ソースから):

> Interestingly, LeWM performs worse on the simplest environment, Two-Room. A possible explanation is that the low diversity and low intrinsic dimensionality of this dataset make it difficult for the encoder to match the isotropic Gaussian prior enforced by SIGReg in a high-dimensional latent space, which may lead to a less structured latent representation. This highlights a potential limitation of the SIGReg regularization in very low-complexity environments.

**訳**: 興味深いことに、LeWM は最も単純な環境である Two-Room において他より低い性能を示す。ありうる説明のひとつは、本データセットの低い多様性と低い intrinsic dimensionality によって、encoder が SIGReg により高次元 latent 空間で課される等方ガウス prior に一致させることが困難となり、その結果として構造化の弱い latent representation を生じさせうる、というものである。これは、極めて低複雑度な環境における SIGReg 正則化の潜在的な限界を示している。

- 帰属先: 「encoder が SIGReg の isotropic Gaussian prior を high-dim 潜在空間で match できず、less structured な representation を生む可能性」(= 表現 / 正則化由来)。
- 語尾は "A possible explanation", "may lead to", "potential limitation" であり、論文側も断定はしていない。

### 記述 B: fig:ctrl-all のキャプション

- 出典: `source/sections/4-exp.tex:56-58` (Markdown 文字起こしは `paper.cleaned.md:197-199`)
- 原文:

> In the simpler Two-Room environment, PLDM and DINO-WM outperform LeWM, which may be explained by the SIGReg regularization encouraging a Gaussian distribution in a high-dimensional latent space, while the intrinsic dimensionality of the environment is much lower.

**訳**: より単純な Two-Room 環境では PLDM と DINO-WM が LeWM を上回り、これは、SIGReg 正則化が高次元 latent 空間でガウス分布を促す一方で、環境の intrinsic dimensionality がそれよりずっと低いことによって説明されうる。

- 帰属先: 「SIGReg による Gaussian 化 vs. 環境の intrinsic dimensionality の低さの不整合」(= 記述 A と同種の正則化由来の説明)。
- 語尾は "may be explained by"。

### 記述 C: appendix tab:probe-tworoom のキャプション

- 出典: `source/appendix.tex:364` (Markdown 文字起こしは `paper.cleaned.md:600`)
- 原文:

> **Physical Latent Probing results on TwoRoom.** Although LeWM underperforms PLDM in downstream planning on this environment, it matches or outperforms PLDM across all probing metrics, and both methods substantially outperform DINO-WM on the linear probe. This suggests that the learned latent space captures the underlying physical state equally well and that the planning gap is not due to a less informative representation but rather to other factors such as the dynamics model or the planning procedure itself.

**訳**: **TwoRoom 上の Physical Latent Probing 結果。** 本環境において LeWM は下流の planning で PLDM を下回るが、全 probing 指標で PLDM と一致するか PLDM を上回り、また両手法とも線形プローブで DINO-WM を実質的に大きく上回る。これは、学習された latent 空間が背後の物理状態を同等によく捉えていること、および planning gap は representation の情報量の不足によるものではなく、むしろ dynamics model や planning procedure 自体といった他の要因によるものであることを示している。

- 帰属先: 「dynamics model または planning procedure itself などの『他の要因』」。明示的に「**not** due to a less informative representation」と書かれている。
- 数値的根拠 (tab:probe-tworoom 本体): LeWM probing は Linear MSE 0.008±0.018 / r 0.996、MLP MSE 0.000 / r 1.000 で、PLDM の Linear MSE 0.008±0.041 / r 0.996、MLP MSE 0.000 / r 1.000 と一致する (詳細は `論文のエビデンスベースの知見_数値表.md` §TwoRoom probing)。
- 語尾は "This suggests that..."、"the planning gap is not due to..." は断定的に書かれている。

### 記述 D: Conclusion - Limitations & Future Work

- 出典: `paper.cleaned.md:248` (LaTeX ソースの該当箇所は `main.tex` または `5-conclusion.tex` 系。`paper.cleaned.md` のみで引用)
- 原文:

> In particular, limited data diversity can affect the effectiveness of the SIGReg regularization in very simple environments with low intrinsic dimensionality, where matching the isotropic Gaussian prior in a high-dimensional latent space becomes challenging.

**訳**: 特に、低い intrinsic dimensionality を持つ極めて単純な環境では、データ多様性の限界が SIGReg 正則化の有効性に影響を及ぼしうる。そうした環境では高次元 latent 空間で等方ガウス prior に一致させることが困難となる。

- 帰属先: 「low intrinsic dimensionality 環境で SIGReg matching が困難」(= 記述 A・B と同種の正則化由来の説明)。
- 語尾は "can affect", "becomes challenging"。

---

## 事実 (記述間の関係)

- 記述 A・B・D は、TwoRoom 失敗の要因を「SIGReg 由来の高次元 Gaussian 化と低 intrinsic dim 環境との不整合」に帰属させる方向で同種である (語尾はいずれも非断定)。
- 記述 C は、TwoRoom 失敗の要因を「representation が less informative であること」では**ない**と明示し、「dynamics model または planning procedure itself などの他要因」に帰属させる。
- すなわち、論文中には「TwoRoom 失敗 ← SIGReg / 表現側」と「TwoRoom 失敗 ← dynamics / planning 側 (= 表現側ではない)」の 2 種の帰属が、それぞれ異なる箇所で並存する。
- 記述 A の "less structured" は representation 側を指す語彙であり、記述 C の "less informative representation" の否定とは同一の representation 軸上で逆向きの記述である (前者は representation を緩く問題視、後者は representation を問題ではないと否定)。
- いずれの記述も論文中で相互参照されていない (`source/sections/4-exp.tex`, `source/appendix.tex` を grep した範囲で、tab:probe-tworoom 周辺で記述 A・B・D を参照する文は確認できなかった)。

---

## 事実 (周辺の関連記述)

### 記述 E: Reacher / OGBench-Cube に対する比較表現

- 出典: `paper.cleaned.md:197-199` の fig:ctrl-all キャプション (記述 B と同じ図) より:

> LeWM consistently outperforms PLDM and DINO-WM on Push-T and Reacher. On OGBench-Cube, DINO-WM slightly outperforms LeWM, possibly due to the higher visual complexity and the 3D nature of the environment, which makes encoder training more challenging.

**訳**: Push-T と Reacher において LeWM は PLDM および DINO-WM を一貫して上回る。OGBench-Cube では DINO-WM が LeWM をわずかに上回るが、これはおそらく環境の高い視覚的複雑性と 3D 性によるものであり、これらは encoder 訓練をより困難にする。

- ここでは OGBench-Cube での LeWM の劣位 (LeWM 74 vs DINO-WM 86、`論文のエビデンスベースの知見_数値表.md` §OGBench-Cube) を「visual complexity と 3D nature による encoder training の難しさ」に帰属させる。すなわち OGBench-Cube に関しては encoder 側 (= 表現側) の理由として記述されている。

### 記述 F: Limitations 中の planning 自体への言及

- 出典: `paper.cleaned.md:248`
- 同じ Limitations 段落の冒頭で:

> First, planning with current latent world models remains restricted to short horizons. Hierarchical world modeling represents a promising direction to address long-horizon reasoning and planning.

**訳**: 第一に、現在の latent world model による planning は短ホライズンに限定されたままである。Hierarchical world modeling は、長ホライズンの推論および planning に対処するための有望な方向性を示す。

- これは TwoRoom 固有の話ではないが、論文自体が "planning" の短ホライズン制約を Limitation として独立に列挙していることが確認できる。

---

## 現在検討中の点

- 論文側が「2 つの異なる帰属を併記している」ことを著者自身が明示的に説明する記述は、現在確認した範囲では見つかっていない (記述 A〜D は互いに相互参照していない)。
- 記述 C で言及される「dynamics model または planning procedure itself」のうちどちらが、または両方が、planning gap の要因として論文側で支持されているかは、本文中では特定されていない (記述 C 自体が "such as ... or ..." の例示に留まる)。
- 「L2 距離が geodesic に整合しない」「非ユークリッド topology」といった具体的な幾何学的説明は、現在確認した範囲では論文本文に記述されていない。Limitations の "matching the isotropic Gaussian prior ... becomes challenging" は representation 分布側の話であり、距離関数 (cost) 側の幾何学についての明示的な記述は確認できなかった。
