## これは何？

hijiki 氏の構成した論文執筆環境の為に Appendix(と bibliography 用の設定を生やしたもの)

## 使い方

まずはこのファイルを texlive が見つけられるところに置いた方が良いです
(usr/local/texlive/texmf-local/tex/latex/appendixafter/AppendixAfter.sty)
置いたら必ず mktexlsr するように。

### inputappendix

```tex
\inputappendix[#1]{#2}
```

#1 にはそのファイルからの相対パス、#2 にはメインファイルからの相対パスを入れてください。
そのファイルを、ドキュメントの末尾で input します

### maintex

メインファイルの場合、プリアンブルで`\maintex`してください

### finalsection

```tex
\begin{finalsection}
hoge
\end{finalsection}
```

謝辞などの、Appendix より後で始めたいものはこの環境で囲った中に書き込んでください。Appendix が存在しない場合は単純に最後に呼び出すことが出来ます。

#### AppendixAfter, BibsAfter, FinalProcess

このパッケージは AtEndDocument に`\FinalProcess`をすることで機能を提供しています。

##### FinalProcess

初期状態では`\FinalProcess`は`\AppendixAfter`,`\BibsAfter`,`\InputAllFinalSections`の順に読み込んでいます。

##### InputAllFinalSections

`\InputAllFinalSections`の中身は`\jobname.fsl`を読み込むようになっています。
*finalsection*環境を使うたびに`\jobname\theAppendixAfter@finalsection.fsc`を作成し、それを input する、という内容が`\jobname.fsl`には記載されています。

##### AppendixAfter

初期状態では`\AppendixAfter`の中身は`\clearpage`->`appendix`->`\InputIfFileExists{\jobname.afn}{}{}`となっています(IO があるため、余りいじることはお勧めしません)

##### BibsAfter

初期状態では`\BibsAfter`の中身は空となっています。そのため、このマクロを `\renewcommand` することで、Appendix の後に参考文献などを入れることが出来ます。

## 新規 IO について

`.afn`(Appendix File Name), `.fsl`(Final Section List), `.fsc`(Final Section Content) ファイルを利用します。
latexmk を使用する場合は

```perl
push @generated_exts, 'afn', 'fsl', 'fsc';
add_cus_dep('afn', 'tex', 0, 'dummy_rescan');
add_cus_dep('fsl', 'tex', 0, 'dummy_rescan');
add_cus_dep('fsc', 'tex', 0, 'dummy_rescan');
sub dummy_rescan {
    return 0;
}
```

と追記した方が良いかと思います。
