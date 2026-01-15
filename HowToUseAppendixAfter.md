# AppendixAfter v2.1.5

## これはなに？

AppendixAfter は、docmute パッケージとの併用下で威力を発揮するパッケージです。
サブファイルでのタイプセットでも、メインファイルのタイプセットでも Appendix を常に末尾に据え付けることが出来ます。

## インストール

AppendixAfter.sty を TeX が認識できるパスへ配置し、必要に応じて `mktexlsr` を実行してください。
例: `$TEXMFLOCAL/tex/latex/appendixafter/`

## 基本的な使い方

### パッケージの読み込みとモード指定

タイプセット対象がメインか、サブファイルかを `mode` で指定します。

1. メインファイルの場合

```tex
\usepackage[mode=main]{AppendixAfter}
```

2. サブファイルの場合

```tex
\usepackage[mode=sub]{AppendixAfter}% サブファイルの場合、mode指定は省略できます
```

### オプション一覧

| オプション      | 値             | 説明                                                                                                         |
| --------------- | -------------- | ------------------------------------------------------------------------------------------------------------ |
| `mode`          | `main`         | ルートモード。`\AppendixInput` は第 2 引数（メイン用パス）を採用します。                                     |
|                 | `sub`          | サブモード（デフォルト）。`\AppendixInput` は第 1 引数（サブ用パス）を採用します。                           |
|                 | `off`          | 機能を停止。記録も末尾出力も行いません。                                                                     |
| `file`          | 文字列         | 中間ファイル名（既定: ` \c_sys_jobname_str.apx`）。危険拡張子（`tex, sty, cls, aux, log, toc, bbl`）は不可。 |
| `auto-appendix` | `true`/`false` | 末尾出力の直前に `\clearpage\appendix` を自動実行するか（既定: `true`）。                                    |

安全のため `file` の拡張子に `.tex` 等は指定できません。デフォルトの `.apx` を推奨します。

## docmute との併用構成例

メイン／サブで異なるパスを安全に扱う例です。

```tex main.tex
\documentclass{ltjsreport}
\usepackage[mode=main]{AppendixAfter}
\addbibresource{sections/01_intro/reference.bib}
\begin{document}
\input{title.tex}
\input{sections/00_abst/main.tex}
\clearpage
\input{sections/01_intro/main.tex}

\begin{AfterAppendix}
  \input{acknowledgement.tex}
  \printbibliography[title={参考文献}]
\end{AfterAppendix}

\end{document}
```

```tex
\documentclass{ltjsreport}
\usepackage{AppendixAfter}
\addbibresource{reference.bib}
\begin{document}
\chapter{序論}
イントロ

引用\cite{Lang_2010}

画像：図\ref{fig:01_image}
\begin{figure}[ht]
  \centering
  \includegraphics[keepaspectratio, scale=1.0]
  {assets/01_image.png}
  \caption{キャプション}
  \label{fig:01_image}
\end{figure}

表：表\ref{table:01_table}
\begin{table}[ht]
  \centering
  \begin{tabular}{|c|c|c|} \hline
    A & B & C \\ \hline
    A & B & C \\ \hline
    A & B & C \\ \hline
    A & B & C \\ \hline
    A & B & C \\ \hline
    A & B & C \\ \hline
    A & B & C \\ \hline
    A & B & C \\ \hline
  \end{tabular}
  \caption{長いキャプション長いキャプション長いキャプション長いキャプション長いキャプション長いキャプション長いキャプション長いキャプション長いキャプション長いキャプション}
  \label{table:01_table}
\end{table}
% \AppendixInput[サブファイルから見た相対パス]{メインファイルから見た相対パス}
\AppendixInput[appendix.tex]{./sections/01_intro/appendix.tex}
\begin{OnlySubAppendix}
  \printbibliography[title={参考文献}]
\end{OnlySubAppendix}
\end{document}
```

## コマンド・環境リファレンス

### ファイル読み込み

#### `\AppendixInput[<sub_path>]{<main_path>}`

末尾（Appendix）で `\input{...}` するファイルを記録します。

- `mode=main`: `<main_path>` から読み込みます
- `mode=sub`: `<sub_path>` から読み込みます（省略時は `<main_path>` を流用）

存在しないファイルを指定した場合、警告を出します。

### 末尾へまとめてコードを送りたい

#### `\begin{AfterAppendix} ... \end{AfterAppendix}`

制御綴（`\input` や `\printbibliography` など）を含むコードを、Appendix 後へそのまま送るための環境です。環境内に書いた順序で出力されます。

例:

```tex
\begin{AfterAppendix}
  \input{acknowledgement.tex}
  \printbibliography[title={参考文献}]
\end{AfterAppendix}
```

### テキスト塊の予約（Verbatim ライク）

#### `\begin{Stock} ... \end{Stock}`

環境内の内容をそのまま `.apx` へ書き出します。記述順で末尾に挿入されます。特殊文字を含むテキストやコード片の転送に適しますが、制御綴を含める用途には `AfterAppendix` を使う方が安全です。

#### `\begin{OnlySubAppendix} ... \end{OnlySubAppendix}`

環境内の内容を、`mode=sub` の場合にのみ `.apx` ファイルに追記します。
参考文献をサブファイルでも表示したい場合などに用いてください。

また、~~使い道は思いつきませんが~~ `mode=main` の場合にのみ `.apx` ファイルに追記する、`OnlyMainAppendix` 環境もあります。

### 1 行だけコマンドを送りたい

#### `\AppendixLine{<code>}`

任意の 1 行をそのまま予約します。

```tex
\AppendixLine{\clearpage}
```

### 手動で末尾出力を発行したい

#### `\OutputAppendix`

通常は `\end{document}` で自動出力されますが、任意位置で末尾出力を確定したい場合に使います。1 度出力されると、以降の自動出力はスキップされます。

### 自動 Appendix の発行

`auto-appendix=true`（既定）では、末尾出力直前に `\clearpage\appendix` を自動で発行します。不要な場合は `auto-appendix=false` にしてください。

## 動作の要点

- 記録の順序どおりに、末尾で上から順に実行されます。

## latexmk の設定（推奨）

`.apx` の変更検知と再スキャンのため、`.latexmkrc` に以下を追加することを推奨します。

```perl .latexmkrc
push @generated_exts, 'apx';
```

## 互換性・非推奨

- `\maintex` コマンドは非推奨になりました。パッケージオプションでの指定を使用してください。
- `\inputappendix` コマンドは `\AppendixInput` に名称変更されました。
- `finalsection` 環境は `Stock` / `AfterAppendix` 環境へ名称変更されました。
- 旧フック（`\FinalProcess`, `\BibsAfter` など）は廃止しました。必要に応じて `\AppendixLine` や `AfterAppendix` を使用してください。
