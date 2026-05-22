# Beamer basics — preamble, packages, document class

## Document class

```latex
\documentclass[aspectratio=169]{beamer}
```

Aspect-ratio options:

| Option | Ratio | Use for |
|--------|-------|---------|
| `aspectratio=169`  | 16:9 | modern projectors and laptops — **default recommendation** |
| `aspectratio=1610` | 16:10 | older MacBook displays |
| `aspectratio=149`  | 14:9 | rare |
| `aspectratio=54`   | 5:4 | rare |
| `aspectratio=43`   | 4:3 | Beamer's default; old projectors |
| `aspectratio=32`   | 3:2 | rare |

Other useful class options:

| Option | Effect |
|--------|--------|
| `handout`       | One slide per page, all overlays collapsed (printable handout) |
| `notes=show`    | Speaker notes interleaved with slides (in-PDF) |
| `notes=only`    | Only the speaker notes (skip slides; for printing notes) |
| `t`             | Top-align frame content by default |
| `c`             | Centre-align frame content by default (Beamer default) |
| `b`             | Bottom-align frame content by default |
| `xcolor=table`  | Lets `xcolor` colour table cells (load before `\usetheme{}`) |
| `compress`      | Compress navigation symbols |

## Common preamble — order matters

```latex
% --- Document class --------------------------------------------------
\documentclass[aspectratio=169]{beamer}

% --- Theme (load first, before content-related packages) -------------
\usetheme{Madrid}
\usecolortheme{default}
\usefonttheme{professionalfonts}

% --- Encoding and language -------------------------------------------
\usepackage[utf8]{inputenc}
\usepackage[T1]{fontenc}
\usepackage[british]{babel}

% --- Maths and units (academic decks) --------------------------------
\usepackage{amsmath, amssymb}
\usepackage{siunitx}

% --- Graphics --------------------------------------------------------
\usepackage{graphicx}
\usepackage{subcaption}      % NOT subfig; subcaption is modern
\usepackage{tikz}
\usepackage{pgfplots}
\pgfplotsset{compat=newest}

% --- Tables ----------------------------------------------------------
\usepackage{booktabs}

% --- Citations (if needed) -------------------------------------------
\usepackage[backend=biber, style=authoryear]{biblatex}
\addbibresource{refs.bib}

% --- Hyperlinks (load LAST) ------------------------------------------
\usepackage{hyperref}
\hypersetup{colorlinks=true, allcolors=blue}
```

## Why the order matters

- **Themes before packages**: theme files can patch templates; loading them after content packages may overwrite your `\setbeamertemplate{...}` customisations.
- **`hyperref` last**: it patches many internal macros (`\section`, `\caption`, `\ref`). Loading anything that also patches those macros *after* `hyperref` breaks PDF anchors silently — clickable links go to the wrong page.
- **`xcolor=table` as a class option**, not `\usepackage{xcolor}[table]`: Beamer already loads `xcolor`; passing `table` to it requires injecting at class-load time.

## Required content commands

```latex
\title{My Talk}
\subtitle{An optional subtitle}
\author{Author One \and Author Two}
\institute{Affiliation}
\date{2026-05-22}

\begin{document}
    \frame{\titlepage}
    \begin{frame}{Outline}\tableofcontents\end{frame}
    % ... \section{...} + content frames ...
\end{document}
```

`\and` between authors creates the comma-separated multi-author display. `\institute{}` accepts a short version via `\institute[short]{long}`.

## Frame options

```latex
\begin{frame}[<options>]{Frame title}
    Content
\end{frame}
```

Useful options:

| Option | Effect |
|--------|--------|
| `[t]` / `[c]` / `[b]` | top / centre / bottom alignment of body |
| `[plain]`             | no headline, no footline, no frame title |
| `[fragile]`           | required for any frame containing `\verb` or `\begin{verbatim}` |
| `[fragile=singleslide]` | safer fragile mode that tolerates literal `\end{frame}` inside verbatim |
| `[allowframebreaks]`  | auto-split frame across multiple pages if content overflows (useful for references) |
| `[label=name]`        | give the frame a label for `\hyperlink{name}{}` |
| `[shrink=N]`          | shrink content to fit (use sparingly; ugly) |

## Top-of-file comment block

For LLM-generated decks, prepend:

```latex
% ==============================================================
%   <Deck Title>
%   Author: <Name>
%   Compile: pdflatex deck.tex && pdflatex deck.tex
%   (Add: biber deck && pdflatex deck.tex if using biblatex)
% ==============================================================
```

This gives the next person who opens the file (often yourself, weeks later) the compile command without having to recall whether the deck uses citations or not.
