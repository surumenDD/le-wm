---
name: katex-port
description: 論文・LaTeX ソースから Markdown docs に数式を転記・引用するときに、論文側で定義された独自マクロ (\bm, \gX, \enc, \pred 等) を KaTeX 標準コマンドへ意味的に等価な形で置換するための原則。KaTeX プレビュー・GitHub Markdown レンダリングで ParseError や生 TeX 表示が起きる事象を回避するために用いる。
---

# 数式マクロの KaTeX 互換変換

## 適用タイミング

論文・LaTeX ソース・他者の docs から、Markdown ファイル (CLAUDE Code のプレビュー / GitHub web UI / VS Code 拡張プレビューでの閲覧を想定) に数式を転記または引用するとき。

「数式が表示されない」「ParseError: KaTeX parse error」「生の TeX のまま表示される」といった問題が報告された / 観察されたときも対象。

## 原則

### なぜ置換するか

- Markdown レンダラ (KaTeX) は、論文側の `\bm`, `\gC`, `\sR`, `\enc`, `\pred` のような独自マクロを既定で**未定義として ParseError を返す**。これらは論文の `math_commands.tex` などで `\newcommand` / `\def` 定義されている。
- 結果として docs が読めない状態となり、出典としての価値が低下する。
- 一方、論文側のマクロが意味する数学記号 (太字ベクトル / カリグラフィ / ブラックボード太字 / ローマン体 / 太字行列) は、KaTeX 標準コマンド (`\mathbf`, `\mathcal`, `\mathbb`, `\mathrm`, `\boldsymbol` 等) で再現可能なケースが多い。

### 何をするか

- 元のマクロが「定義上どの記号フォントを意味するか」を `math_commands.tex` 等で確認する。
- 「**意味の等価性**」を最優先に、見た目 (字体・太さ・斜体性) と意味 (ベクトル / 集合 / 関数記号など) を保ったまま、KaTeX 標準コマンドに書き換える。
- 「形が似ているが意味が違う」代替 (例: 太字ベクトル `\bm{z}` を立体の `z` で置換する) は採らない。
- KaTeX で表現できない意味 (論文独自の合成記号、独自書体、未対応の組版コマンド等) に出会った場合は、無理に置換せず、当該式を画像化するか、その式は元の TeX のまま残し docs に注記を入れる。

### 検証

- 置換後の docs は、ローカルで KaTeX 公式ライブラリ (`katex.renderToString`) を全式に対して走らせ、`ParseError` が出ないことを確認する。
- 失敗した式は、原因となっているマクロを特定して原則に従って再置換する。

## 参照

- KaTeX 対応コマンド一覧: <https://katex.org/docs/supported.html>
- 論文側のマクロ定義は通常 `source/math_commands.tex` などに集約されている。docs に式を写す前にここを参照すると、未定義マクロによる ParseError をまとめて予防できる。

## 適用範囲外 (注意)

- Notion の Markdown インポータは `$...$` 形式の数式自体を認識しない (KaTeX 非対応ではなく、数式記法を Markdown として受理しない仕様)。本 Skill の置換はこの問題を解決しない。Notion 表示を求める場合は、数式の画像化や API 経由のアップロードなど別の手段が必要。
- 論文の組版・体裁を文字どおり再現する目的 (PDF 出力など) には本 Skill は使わない。本 Skill は Markdown プレビューでの可読性確保が目的。
