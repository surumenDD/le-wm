# 260513-tworoom-eval 継続作業用コンテキスト

LeWorldModel (arXiv:2603.19312) の Two-Room 環境における planning 再現実験。HF 事前学習済み checkpoint を入手し、論文値 87% を再現するまでがスコープ。

## 現在の状態

### ブランチ
- 現在: `260513-tworoom-eval`
- ベース: `develop` (origin/develop に push 済)
- main: 未変更（規約: main 直接 push 禁止）

### 作業ツリー
- clean（未コミット変更なし）

### 完了した作業
1. 論文 `papers/2603.19312_leworldmodel/paper.cleaned.md` 全読
2. `stable-worldmodel` を `/home/ryo52/workspace/exp-wp/stable-worldmodel/` に clone（commit `2899349`）して実装読解
3. HuggingFace `quentinll/lewm-tworooms` の `config.json` を WebFetch、`weights.pt` を /tmp に DL して `torch.load` で state_dict を実測
4. 再現目標値 87% を `papers/.../source/figs/ctrl-two-room.pdf` を `mutool draw` で描画し確認（`docs/260513-tworoom-eval/tworoom_fig.png`）
5. 4 つのハイパラ情報源（論文 / le-wm yaml / stable-worldmodel yaml / HF config.json）を整理
6. 訓練 yaml → コンストラクタ引数 → 重み形状 の 3 層チェーンを行番号付きで突き止め
7. eval パラメータの採用方針確定（論文 Appendix 値 150/100/10）
8. モデル形状の採用方針確定（HF checkpoint `num_frames=3` 固定、history=1 は再訓練必要で本スコープ外）
9. 著者照会 DM 草案作成（フル版・短縮版・GitHub Issue 版、署名: Ishida Ryota, GitHub: surumenDD, 九州工業大学情報工学部 4年）
10. develop ブランチ作成、`260513-tworoom-eval` ブランチ作成、7 コミット分割で push（規約「1コミット=1意図」準拠）

### push 済みリモート
- `origin/develop` (基盤: .gitignore, CLAUDE.md, papers/)
- `origin/260513-tworoom-eval` (調査docs 4本)

### 最近のコミット
```
a4228b0 docs(260513-tworoom-eval): author contact draft for paper-vs-HF discrepancy
ac8924e docs(260513-tworoom-eval): hyperparameter source analysis and adopted policy
240a442 docs(260513-tworoom-eval): code reading findings from stable-worldmodel and HF
f232d12 docs(260513-tworoom-eval): initial speculative plan and verified 87% target
6d3116e docs: add reference paper materials for LeWorldModel (arXiv:2603.19312)
8f8c43d docs: add CLAUDE.md with project rules and operating conventions
0745148 chore: add .gitignore for env marker, venv, hydra outputs, and caches
```

---

## 次に追加すべきタスク（優先度順）

### 優先度: 高

1. **著者 DM 送信**
   - 草案: `docs/260513-tworoom-eval/著者DM草案.md`
   - 宛先候補: Lucas Maes (`@lucasmaes_`) または Quentin Le Lidec
   - 送信形式の決定: X DM フル版 / 短縮版 / GitHub Issue 版

2. **確定作業プラン.md の作成**（要承認）
   - CLAUDE.md 規約上、確定プランは **ユーザー承認後** に作成可能
   - 内容ベース: `docs/260513-tworoom-eval/ハイパラの情報源整理.md` §5 の採用方針
   - 主な TODO:
     - `config/eval/tworoom.yaml` 修正（eval_budget=150, goal_offset=100、world.frame_skip/history_size 削除）
     - `config/eval/solver/cem_tworoom.yaml` 新規作成（n_steps=10、PushT 他を壊さないため分離）
     - `eval.py:88` の `AutoCostModel` → `swm.wm.utils.load_pretrained` 置換
     - data 配置（`tworoom.tar.zst` → `$STABLEWM_HOME/datasets/tworoom.h5`）

3. **uv 環境構築（コーディング環境）**
   - 既存環境 `/home/ryo52/workspace/afm_exp/pyproject.toml` を参考にバージョン揃え → wheel 再利用で高速化
   - `stable-worldmodel[train,env]` は新規 DL 必要

### 優先度: 中

4. **コード/yaml 修正の実装**
   - 確定プラン承認後に着手
   - 1 コミット = 1 意図で分割
   - 修正対象: 3 ファイル（tworoom.yaml, cem_tworoom.yaml 新規, eval.py）

5. **評価実行**
   - コマンド例: `python eval.py --config-name=tworoom.yaml policy=quentinll/lewm-tworooms eval.eval_budget=150 eval.goal_offset_steps=100`（CLI override 経路）
   - 第一試行: 論文値で
   - 第二試行（必要なら）: リポ既定値 50/25/30 で

### 優先度: 低

6. **実験結果.md の作成**
   - 評価実行後、`metrics`（success_rate, eval_time）を併記
   - 87% ± 数 pp に収まれば成功と記録
   - 著者からの返信があれば併記

---

## 技術的なパターン・注意点

### docs の規約
- **`我々共通用語`（【surumePC】等）は docs/コードに混ぜない**（CLAUDE.md 必読）
  - 例外: `今の環境.md` と `我々共通用語を含む環境差異の知見.md` のみ
  - 例外用語: path / `yymmdd-実験名` の通し名 / `用語集new.md` 記載の用語
- **恣意・推測は docs に書かない**。事実ベース・エビデンスベースのみ
- 未確定の検討事項は「現在検討中の点」、決定事項は「採用方針」として明示的に分離
- `確定作業プラン.md` は **ユーザー承認なしに書けない**（CLAUDE.md 規定）

### コミット規約
- 1 コミット = 1 意図
- 意図が同じなら複数ファイルを 1 コミットにまとめてよい
- 意図が異なるなら同一ファイルでも別コミットに分ける
- 「大量だから分割」ではなく「意図が異なるから分割」

### ブランチ規約
- デフォルトブランチは `develop`
- 作業開始日に `yymmdd-実験名` で develop から派生
- main 直接 push 禁止（PR/別フロー経由のみ）

### 重要な発見（再起後の前提情報）

#### 軸 A: Eval パラメータ
- 論文 Appendix F.1: `eval_budget=150, goal_offset=100, CEM n_steps=10`
- リポ yaml (le-wm, stable-worldmodel 両方): `50, 25, 30`
- HF config: eval パラメータは持たない（モデルアーキ定義のみ）
- ⇒ 採用: 論文値（第一試行）、第二試行は保留

#### 軸 B: モデル num_frames
- 論文 Appendix D: Two-Room は `history length = 1`（PushT/Cube は 3、per-env で特筆）
- HF `config.json`: `predictor.num_frames: 3`
- HF `weights.pt` を直接 `torch.load` した結果: `predictor.pos_embedding` shape **`(1, 3, 192)`**（実測）
- ⇒ HF 公開モデルは uniform `num_frames=3` で訓練済み。論文記述 1 とは乖離
- ⇒ 採用: `num_frames=3` 固定（HF 利用前提だと変更不可、history=1 再現は再訓練必要で本スコープ外）

#### 3 層チェーン（モデル形状の根拠）
```
[Layer 1: 訓練 yaml]                [Layer 2: コンストラクタ引数]               [Layer 3: 重みの形状]
wm.history_size = N  ───→  Predictor(num_frames=N, ...)  ───→  pos_embedding: (1, N, input_dim)
```
- le-wm: `config/train/lewm.yaml:47` → `train.py:95` → `module.py:262`
- stable-worldmodel: `scripts/train/config/lewm.yaml:43` → `scripts/train/lewm.py:160` → `stable_worldmodel/wm/lewm/module.py:269-271`

#### コード経路の発見
- 公式 eval は `swm.wm.utils.load_pretrained(cfg.policy)` を使用（HF repo id を直接受ける）
- 本リポ `eval.py:88` は legacy `AutoCostModel`（`_object.ckpt` のみ対応、HF `weights.pt` 非対応）→ 修正必要
- HF データセット `quentinll/lewm-tworooms` は `tworoom.tar.zst`（3.43 GB） → 解凍すると `tworoom.h5` (12.8 GB、実測値)
- `HDF5Dataset(name='tworoom')` は `.h5` を自動付与（`hdf5.py:55`） → 既存 yaml の `dataset_name: tworoom` で OK

### /tmp に残してある実測ファイル
- `/tmp/lewm_tworoom_weights.pt` (72,290,849 byte) — HF 重み実体（再起動で消える可能性あり、再 DL コマンドは下記）
- `/tmp/tworoom_head.tar.zst` — HF データセットの先頭 2MB（tar内のファイル名確認用）

---

## 発見済みの問題点

- `lucas-maes/le-wm` の `eval.py:88` が legacy `AutoCostModel` で HF 形式に未対応（要修正）
- `config/eval/tworoom.yaml:10-11` の `world.history_size: 1` と `world.frame_skip: 1` は `swm.World` が受けない kwarg（gym.make に流れて env が拒否する可能性、削除推奨）
- `config/eval/solver/cem.yaml` を直接書き換えると PushT 他の評価が壊れる → Two-Room 用に `solver/cem_tworoom.yaml` を分離するのが安全
- 公式 `stable-worldmodel/scripts/plan/config/tworoom.yaml` の callables は `pos_agent`/`goal_pos_agent`（stale）。実際 env が emit するのは `proprio`（`envs/two_room/env.py:346`）。本リポ既存 yaml の `proprio` 表記が正
- README の Google Drive checkpoint URL が 404（著者照会の Request 1）

---

## コマンド

```bash
# 重みファイル再 DL（/tmp が消えた場合）
curl -sL -o /tmp/lewm_tworoom_weights.pt \
  "https://huggingface.co/quentinll/lewm-tworooms/resolve/main/weights.pt"

# pos_embedding 形状を確認
uv run --with torch python3 -c "
import torch
sd = torch.load('/tmp/lewm_tworoom_weights.pt', map_location='cpu', weights_only=True)
print('predictor.pos_embedding shape:', tuple(sd['predictor.pos_embedding'].shape))
"

# 3 層チェーンを VSCode で開く（le-wm 側）
code -g /home/ryo52/workspace/exp-wp/le-wm/config/train/lewm.yaml:47
code -g /home/ryo52/workspace/exp-wp/le-wm/train.py:95
code -g /home/ryo52/workspace/exp-wp/le-wm/module.py:262

# 3 層チェーンを VSCode で開く（stable-worldmodel 側）
code -g /home/ryo52/workspace/exp-wp/stable-worldmodel/scripts/train/config/lewm.yaml:43
code -g /home/ryo52/workspace/exp-wp/stable-worldmodel/scripts/train/lewm.py:160
code -g /home/ryo52/workspace/exp-wp/stable-worldmodel/stable_worldmodel/wm/lewm/module.py:269

# 評価実行（修正後の想定コマンド）
python eval.py --config-name=tworoom.yaml \
  policy=quentinll/lewm-tworooms \
  eval.eval_budget=150 \
  eval.goal_offset_steps=100 \
  solver.n_steps=10
```

---

## 参照ドキュメント

リポ内（重要度順）:
- `CLAUDE.md` — プロジェクト規約（必読）
- `docs/260513-tworoom-eval/ハイパラの情報源整理.md` — 4 情報源と 2 軸の矛盾、採用方針（最重要・最新）
- `docs/260513-tworoom-eval/読解結果.md` — stable-worldmodel と HF の実装読解（行番号エビデンス付き）
- `docs/260513-tworoom-eval/推測作業プラン.md` — 初期計画（§3/§6 は『ハイパラの情報源整理.md』で上書き済）
- `docs/260513-tworoom-eval/著者DM草案.md` — 著者照会の英文 DM
- `docs/260513-tworoom-eval/tworoom_fig.png` — 論文 Fig 3 の Two-Room パネル再描画（87% 確定の根拠）

外部:
- `/home/ryo52/workspace/exp-wp/stable-worldmodel/` — 依存ライブラリ clone（コード読解用）
- `/home/ryo52/workspace/exp-wp/afm_exp/pyproject.toml` — uv バージョン揃え参考用
- HF model: <https://huggingface.co/quentinll/lewm-tworooms>
- HF dataset: <https://huggingface.co/datasets/quentinll/lewm-tworooms>

---

## 環境メモ

- 本プロンプト冒頭で `今の環境.md` を参照して環境を判別する規約（`今の環境.md` は .gitignore 済、ローカル管理）
- コーディング環境: GPU 小（5070ti×2 / 5070(8GB)）
- 訓練/評価環境: GPU 大（3090/4090/A100(40GB)）
- 本実験は eval のみで GPU 要求が小さいため、コーディング環境でも実行可能の見込み（要 VRAM 実測）

---

## 継続作業の開始方法

新しいセッションで以下を実行:
```
/continue 260513-tworoom-eval-handoff
```

または手動で:
```
260513-tworoom-eval（LeWM Two-Room 再現実験）の継続です。
詳細は .tmp/260513-tworoom-eval-handoff.md を参照してください。
```
