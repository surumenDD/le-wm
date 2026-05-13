# 著者DM草案 — 260513-tworoom-eval

## メタ情報

- **送信先候補**:
  - Lucas Maes（first author, corresponding author per `le-wm/README.md:127`）— X: `@lucasmaes_`（`paper.cleaned.md:5` / `README.md:5`）
  - Quentin Le Lidec（co-first author, HF配布者 `quentinll/lewm-tworooms`）— website: <https://quentinll.github.io/>
- **送信媒体**: X DM（軽量問い合わせ向き）
- **代替案**: 公開議論にしたい場合は `lucas-maes/le-wm` の GitHub Issue
- **言語**: 英語
- **問い合わせ目的**:
  1. Fig 3 の Two-Room LeWM = 87% を生成したモデルが、論文 Appendix D 記載の `history=1` 版か、HF 公開の `num_frames=3` 版か
  2. `le-wm/README.md` の Google Drive URL が 404 になる事象の報告と、正しい URL の照会
  3. Drive checkpoint への viewer 権限付与依頼（`ryo5211ta@gmail.com`）

## DM 本文（コピペ用、英語）

```
Hi Lucas,

I'm a university student in Japan working on an independent
reproduction of LeWorldModel. Thank you for sharing this work — the
end-to-end stability result with just two loss terms is really
elegant, and I'd love to build on it in my own research.

I have one technical question and two small requests, if you don't
mind.

[Question — history length on Two-Room]
Paper Appendix D ("Predictor Architecture") states:
  "The history length is set to 3 for the PushT and OGBench-Cube
   environments, and to 1 for TwoRoom."
In the released code, this history length corresponds to
`wm.history_size` (training config), which is passed to the
predictor constructor as `num_frames`
(`scripts/train/lewm.py:160` in rbalestr-lab/stable-worldmodel,
or `train.py:95` in lucas-maes/le-wm), and ultimately sets the
shape of `predictor.pos_embedding`. For the released checkpoint
`quentinll/lewm-tworooms`:
  - `config.json` has `predictor.num_frames: 3`
  - `weights.pt` stores `predictor.pos_embedding` with shape
    `(1, 3, 192)`
So the released model on Hugging Face appears to have been trained
with history length = 3, rather than the 1 stated for Two-Room in
the paper.

Could I ask which configuration produced the 87% LeWM bar in
Fig 3 (`fig:ctrl-all`) — the paper's history=1 model, or this
released num_frames=3 model?

[Request 1 — Google Drive URL]
The Drive link in `le-wm/README.md`
(https://drive.google.com/drive/folders/1r31os0d4-rR0mdHc7OlY_e5nh3XT4r4e)
currently returns a 404 for me. If an updated URL is available,
could you share it? (If it's still being prepared, no worries.)

[Request 2 — Viewer access]
If it's possible, could I get viewer access to the checkpoints
folder? My Google account is ryo5211ta@gmail.com.

I'd be glad to share back what I find from the reproduction.
Thank you again for the work — looking forward to your future
research.

Best regards,
Ishida Ryota
Final-year undergraduate, School of Computer Science and Systems Engineering
Kyushu Institute of Technology, Japan
GitHub: surumenDD
```

## 補足オプション

### A. もっと短くしたい場合（要点 1 + URL 1 + access 1 を 1 段ずつ）

```
Hi Lucas — Japanese university student here, doing an independent
reproduction of LeWM Two-Room (the 87% bar in Fig 3). Thanks for
sharing this work. One question and two small asks, if you don't
mind:

(1) Paper App. D says history length = 1 for TwoRoom. In the code,
this maps to `wm.history_size` → `Predictor(num_frames=...)` →
`pos_embedding` shape. The released `quentinll/lewm-tworooms` has
`config.json: predictor.num_frames=3` and `weights.pt` with
`pos_embedding` shape `(1, 3, 192)`. Which configuration produced
the 87% in Fig 3 — the paper's history=1 model, or this
num_frames=3 model?

(2) The Drive link in README
(drive.google.com/drive/folders/1r31os0d4-rR0mdHc7OlY_e5nh3XT4r4e)
returns a 404 for me. If an updated URL is available, could you
share it?

(3) If possible, could I get viewer access to the Drive
checkpoints? My account is ryo5211ta@gmail.com.

I'd be glad to share back the reproduction result. Thanks!

— Ishida Ryota
  Final-year undergrad, Kyushu Institute of Technology, Japan
  GitHub: surumenDD
```

### B. GitHub Issue 版（公開議論にする場合）

- Title: `Two-Room reproduction: history length mismatch between paper Appendix D and released HF checkpoint`
- Body: 同上の質問部分をそのまま貼り、Drive URL 404 とアクセス権の話は別 Issue or 末尾の補足とする。Public な場合、メールアドレスは載せないほうが無難。

## 送信前チェックリスト

- [x] 署名: `Ishida Ryota / Final-year undergraduate, School of Computer Science and Systems Engineering, Kyushu Institute of Technology, Japan / GitHub: surumenDD`
- [ ] X DM か Issue か最終決定
- [ ] DM の場合、Lucas Maes (`@lucasmaes_`) か Quentin Le Lidec か。HF 配布者ベースなら Quentin
- [ ] Drive URL 404 のスクショを添付するか（X DM は画像添付可能）
- [ ] 返信が来た場合の記録先（`実験結果.md` か `作業記録.md` のいずれか）を事前に決める

## 引用したエビデンスの所在（DM 内で言及した出典）

| 引用 | 出典 |
|---|---|
| 論文 Appendix D の "history length=1 for TwoRoom" | `papers/2603.19312_leworldmodel/source/appendix.tex:311` / `paper.cleaned.md:558` |
| HF config.json の `predictor.num_frames: 3` | <https://huggingface.co/quentinll/lewm-tworooms/blob/main/config.json> |
| HF weights.pt の `predictor.pos_embedding` shape | `/tmp/lewm_tworoom_weights.pt` を `torch.load(..., weights_only=True)` で取得した state_dict、key `predictor.pos_embedding` のテンソル形状 `(1, 3, 192)`（本ブランチ作業環境での 2026-05-14 計測） |
| Fig 3 Two-Room LeWM = 87% | `papers/2603.19312_leworldmodel/source/figs/ctrl-two-room.pdf` → `docs/260513-tworoom-eval/tworoom_fig.png` |
| 該当 Drive URL | `le-wm/README.md:10`, `:89` |
