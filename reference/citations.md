# Citations — biblatex vs natbib, References frames

## Which package to use

| Package | Status | Use when |
|---------|--------|----------|
| `biblatex` + `biber` | Modern (recommended) | Any new deck; full Unicode and complex styles |
| `natbib` + `bibtex`  | Legacy | IEEE / ACM journals where the venue requires a specific bibtex `.bst` |
| Beamer built-in `\cite{}` | Minimal | Tiny decks where you do not need a bibliography frame |

## `biblatex` setup (modern, recommended)

```latex
\usepackage[backend=biber, style=authoryear, sorting=ynt]{biblatex}
\addbibresource{refs.bib}
```

Common style options:

| `style=`        | Output looks like | Use for |
|-----------------|-------------------|---------|
| `authoryear`    | (Smith 2024)              | Humanities, soft sciences |
| `numeric`       | [1]                       | IEEE, computer-science conferences |
| `numeric-comp`  | [1, 2, 3] or [1--3]       | Same, compact |
| `alphabetic`    | [Smi24]                   | Maths, theoretical CS |
| `apa`           | APA 7th                   | Psychology, social science |
| `ieee`          | IEEE journals             | IEEE Xplore-published work |
| `nature`        | Nature journals           | Nature group submissions |

Citation commands:

```latex
\textcite{key}        % "Smith (2024) showed..."  -- in narrative
\parencite{key}       % "(Smith 2024)"             -- in parens
\cite{key}            % style-default (often same as parencite)
\citeauthor{key}      % "Smith"
\citeyear{key}        % "2024"
\fullcite{key}        % full reference inline
\footcite{key}        % cite in a footnote
\nocite{key}          % include in bibliography without citing in text
\nocite{*}            % include EVERY entry in the bib file
```

## References frame

```latex
\begin{frame}[allowframebreaks]{References}
    \printbibliography[heading=none]
\end{frame}
```

`[allowframebreaks]` is essential for long bibliographies — Beamer auto-splits across multiple PDF pages when entries overflow. `heading=none` suppresses the redundant "References" heading inside the bib.

For a sized-down bibliography that fits on one slide:

```latex
\begin{frame}{References}
    \scriptsize
    \printbibliography[heading=none]
\end{frame}
```

## Compile sequence (`biblatex` + `biber`)

```bash
pdflatex deck.tex      # gather citation keys
biber deck             # build the bibliography (note: no .tex extension)
pdflatex deck.tex      # incorporate the bibliography
pdflatex deck.tex      # resolve cross-references
```

Four commands total. With `latexmk -pdf deck.tex` this is automatic.

## `natbib` setup (legacy)

```latex
\usepackage[numbers, sort&compress]{natbib}
\bibliographystyle{ieeetr}     % or plain, abbrv, apalike, ...
```

Citation commands:

```latex
\citet{key}      % "Smith (2024)"           -- in narrative
\citep{key}      % "(Smith 2024)"           -- in parens
\citeauthor{key}
\citeyear{key}
\citealt{key}    % "Smith 2024"             -- no parens
```

References frame:

```latex
\begin{frame}[allowframebreaks]{References}
    \bibliography{refs}       % no extension; bibtex reads refs.bib
    \bibliographystyle{ieeetr}
\end{frame}
```

## Compile sequence (`natbib` + `bibtex`)

```bash
pdflatex deck.tex
bibtex deck
pdflatex deck.tex
pdflatex deck.tex
```

## Bib file format (`refs.bib`)

```bibtex
@article{smith2024quantum,
    author    = {Smith, J. and Doe, A.},
    title     = {Quantum widget dynamics},
    journal   = {Physical Review Letters},
    year      = {2024},
    volume    = {132},
    number    = {4},
    pages     = {123--130},
    doi       = {10.1103/PhysRevLett.132.123},
}

@inproceedings{doe2023learning,
    author    = {Doe, A.},
    title     = {Learning to learn},
    booktitle = {NeurIPS},
    year      = {2023},
}

@book{tantau2023beamer,
    author    = {Tantau, T. and Wright, J.},
    title     = {The Beamer Class User Guide},
    publisher = {self-published},
    year      = {2023},
}
```

Citation keys are arbitrary but conventionally `<author><year><shorttitle>` — short enough to type, distinctive enough to disambiguate.

## Slide-friendly citation pattern

Full citations break visual flow on slides. Two common compromises:

### Inline short citation

```latex
The method (Smith et al., 2024) achieves ...
\par\vspace{1mm}
{\scriptsize Smith J., Doe A. (2024). \textit{Quantum widget dynamics}. PRL 132:123.}
```

### Footer-style citation

```latex
\begin{frame}{Result}
    Main claim.
    \blfootnote{\scriptsize Smith J. et al. (2024). PRL 132:123.}
\end{frame}
```

Where `\blfootnote` is a borderless footnote (define once in preamble):

```latex
\newcommand\blfootnote[1]{%
    \begingroup
    \renewcommand\thefootnote{}\footnote{#1}%
    \addtocounter{footnote}{-1}%
    \endgroup
}
```

## Common pitfalls

- **`biber` vs `bibtex` mix-up**: `biblatex` package requires `biber` as the backend; `natbib` requires `bibtex`. Wrong backend = silent empty bibliography.
- **Missing entries**: bibliography is empty after compile. Re-run the full sequence (you need exactly the right order).
- **Long author lists**: `biblatex` truncates at 3+ authors to "Smith et al." with `maxbibnames=3`. Adjust per style requirements.
- **Cite key typos**: `\cite{foo}` when `foo` is not in the bib produces `[?]` in the output. Watch the compile log for `Citation 'foo' undefined`.
