---
name: beamer
description: Create, modify, and polish LaTeX Beamer presentations using AI agents (Claude Code, Codex CLI, or any agentic LLM). Handles cover pages, multi-column layouts, blocks, figures, tables, equations, citations, Gantt charts, pgfplots, themes (Madrid / Berlin / Singapore / ...), colour customisation, overlays (`\onslide<2->`, `\uncover<2->`), animations, and speaker notes. Use whenever the user asks to build, extend, or restyle Beamer slides — anything from a one-off conference talk to a multi-section thesis-defence deck.
---

# Beamer Skill

A menu of patterns for building LaTeX Beamer presentations *with* an agentic LLM (Claude Code, Codex CLI, Cursor, Aider, …). The skill encodes how to compose each common slide pattern correctly, which packages to load, and the gotchas that bite if you skip a step — so the LLM does not have to rediscover them from first principles every time.

## Credits and licensing context

This skill drives the upstream **Beamer LaTeX class** authored by Till Tantau and currently maintained by Joseph Wright — [github.com/josephwright/beamer](https://github.com/josephwright/beamer). Beamer is distributed under the dual **LaTeX Project Public License (LPPL) 1.3c+** and **GNU General Public License (GPL) v2+** for code, and **GFDL 1.3+** / **LPPL 1.3c+** for documentation. The Beamer project is in no way affiliated with or endorsed by this skill.

This skill itself is MIT-licensed and contains no Beamer source code. It only describes how to *use* Beamer's public API. See `LICENSE` and `NOTICE` in the skill repository for the full terms.

## When to use this skill

Trigger when the user:

- Asks to **create** a Beamer deck from scratch (any topic, any theme).
- Asks to **add** a specific slide pattern to an existing deck (columns, figure, table, equation, plot, gantt, citation, …).
- Asks to **restyle** a deck (change theme, colour palette, fonts, footer, navigation).
- Asks for **overlays / animations** (incremental reveal, alternative content per slide).
- Asks for **speaker notes**, **handouts**, or **two-screen presenter view**.

Do **not** trigger this skill when the user asks for:

- Converting a PowerPoint template to Beamer — use the companion [ppt-to-beamer-skill](https://github.com/wenlzhang/ppt-to-beamer-skill) instead.
- Writing the *content* of a talk (what to say) — that is a writing task, not a layout task.

## What you need from the user

Ask up front if missing:

1. **Goal of the deck** — research talk, lecture, status update, thesis defence, sales pitch. Affects pacing, density, and which patterns to default to.
2. **Audience** — academic / industry / mixed. Affects whether to default-on `siunitx`, `amsmath`, `booktabs`, `pgfplots`.
3. **Length** — minutes of talking time. Rule of thumb: ~1 slide per minute for research talks, ~2 minutes per slide for technical deep-dives.
4. **Theme preference** — minimal (default Beamer / Berlin), corporate (Madrid / Singapore), institutional (matches a brand — use the companion ppt-to-beamer-skill).
5. **Aspect ratio** — 16:9 (modern projectors and laptops, recommended), 4:3 (older projectors). Default to 16:9.
6. **Bibliography** — if the user mentions citations, ask whether they use `biblatex` (modern) or `natbib` (legacy IEEE / ACM journal style).

## The Beamer mental model (one-paragraph refresher)

A Beamer deck is a LaTeX document with `\documentclass{beamer}`. Content lives in `frame` environments; each `\begin{frame}{Title}...\end{frame}` becomes one page in the PDF (or several pages if you use overlays inside it). The visual appearance is driven by a layered template system: a **theme** (e.g. `\usetheme{Madrid}`) bundles four sub-themes — **outer** (headline, footline, sidebar), **inner** (titles, blocks, lists, bullets), **colour** (palette), and **font** (sizes, families). Override each layer independently for fine control.

## The pattern catalogue

Below is the menu of patterns you compose to build any deck. Each entry links to a deeper reference file when needed.

### A. Deck skeleton (always start here)

```latex
\documentclass[aspectratio=169]{beamer}
\usetheme{Madrid}              % or Berlin, Singapore, Frankfurt, default, ...
\usecolortheme{default}        % or beaver, crane, dolphin, ...
\usefonttheme{professionalfonts}

\usepackage[utf8]{inputenc}
\usepackage[T1]{fontenc}
\usepackage[british]{babel}
\usepackage{graphicx}
\usepackage{booktabs}
\usepackage{hyperref}

\title{Talk Title}
\subtitle{Optional subtitle}
\author{Author Name}
\institute{Affiliation}
\date{\today}

\begin{document}
\frame{\titlepage}
\begin{frame}{Outline}\tableofcontents\end{frame}
% ... content frames ...
\end{document}
```

See [reference/beamer-basics.md](reference/beamer-basics.md) for the full skeleton with all common packages, why each is included, and the order they should be loaded (hyperref must come last).

### B. Themes and appearance

Beamer ships ~30 themes. The classics:

| Theme | Feel | Use for |
|-------|------|---------|
| `default`    | Minimal, no chrome | Distraction-free research talks |
| `Madrid`     | Title bar + footer | Industry / corporate |
| `Berlin`     | Mini navigation bar | Long structured talks (2+ sections) |
| `Singapore`  | Subtle headline | Academic seminars |
| `Frankfurt`  | Bold colour blocks | Lecture courses |
| `Warsaw`     | Sidebar + navigation | Tutorials (slot for ToC) |
| `metropolis` | Modern, clean (external) | Conference talks (de facto standard since 2016) |

`metropolis` is not part of upstream Beamer — install via `tlmgr install beamertheme-metropolis` or include from CTAN.

For colour customisation:

```latex
\definecolor{Accent}{HTML}{0066CC}
\setbeamercolor{structure}{fg=Accent}
\setbeamercolor{frametitle}{fg=Accent}
\setbeamercolor{block title}{fg=white,bg=Accent}
```

See [reference/themes.md](reference/themes.md) for the full theme catalogue, colour-theme list, and the four-layer override mechanism.

### C. Columns and multi-pane layouts

```latex
\begin{frame}{Two-column comparison}
    \begin{columns}[T,onlytextwidth]
        \begin{column}{0.48\textwidth}
            Left content
        \end{column}
        \begin{column}{0.48\textwidth}
            Right content
        \end{column}
    \end{columns}
\end{frame}
```

Options: `[T]` top-align, `[c]` centre, `[b]` bottom; `onlytextwidth` keeps content within the page text width. For three columns drop to `0.31\textwidth` per column.

See [reference/layouts.md](reference/layouts.md) for two/three-column patterns, mixed-width layouts, and the figure-plus-caption beside text pattern.

### D. Blocks (callouts)

```latex
\begin{block}{Definition}
    Standard block -- neutral colour from the theme.
\end{block}

\begin{alertblock}{Warning}
    Red-tinted block for caveats and warnings.
\end{alertblock}

\begin{exampleblock}{Example}
    Green-tinted block for worked examples.
\end{exampleblock}
```

For a custom-colour block, use `tcolorbox`:

```latex
\usepackage{tcolorbox}
\begin{tcolorbox}[colback=blue!5, colframe=blue!75!black, title=Note]
    Custom-coloured block with thicker frame.
\end{tcolorbox}
```

### E. Figures

```latex
\begin{frame}{Figure with caption}
    \begin{figure}
        \centering
        \includegraphics[width=0.7\textwidth]{path/to/figure.pdf}
        \caption{Brief description.}
        \label{fig:example}
    \end{figure}
\end{frame}
```

Two figures side-by-side via `subcaption`:

```latex
\usepackage{subcaption}

\begin{frame}{Side-by-side figures}
    \begin{figure}
        \begin{subfigure}[t]{0.48\textwidth}
            \includegraphics[width=\linewidth]{fig1}
            \caption{Left figure}
        \end{subfigure}\hfill
        \begin{subfigure}[t]{0.48\textwidth}
            \includegraphics[width=\linewidth]{fig2}
            \caption{Right figure}
        \end{subfigure}
    \end{figure}
\end{frame}
```

See [reference/graphics.md](reference/graphics.md) for the full figure / subfigure / wrapfigure patterns plus scaling and clipping options.

### F. Tables

```latex
\usepackage{booktabs}          % \toprule, \midrule, \bottomrule

\begin{frame}{Comparison table}
    \begin{table}
        \centering
        \begin{tabular}{lcc}
            \toprule
            Method & Accuracy & Speed \\
            \midrule
            A & 92\% & fast \\
            B & 95\% & medium \\
            C & 88\% & slow \\
            \bottomrule
        \end{tabular}
        \caption{Method comparison.}
    \end{table}
\end{frame}
```

For wider tables that don't fit, scale with `\resizebox{\textwidth}{!}{...tabular...}` or drop to `\scriptsize` / `\footnotesize`.

### G. Equations and units

```latex
\usepackage{amsmath, amssymb}
\usepackage{siunitx}

\begin{frame}{Equations}
    The state-space model is
    \begin{equation}
        \dot{\mathbf{x}} = \mathbf{A}\mathbf{x} + \mathbf{B}\mathbf{u},
        \label{eq:state}
    \end{equation}
    with quantities $v_{\max} = \SI{120}{\kilo\meter\per\hour}$ and
    $g = \SI{9.81}{\meter\per\second\squared}$.
\end{frame}
```

For step-by-step equation reveal, use `\onslide<2->` (see overlays).

See [reference/math-and-units.md](reference/math-and-units.md) for aligned equations, equation arrays, theorem environments, and `siunitx` configuration.

### H. Plots (`pgfplots`)

```latex
\usepackage{pgfplots}
\pgfplotsset{compat=newest}

\begin{frame}{Plot example}
    \begin{tikzpicture}
        \begin{axis}[
            width=0.7\textwidth,
            xlabel={Time [s]}, ylabel={Velocity [m/s]},
            grid=major]
            \addplot[blue, thick] table {data.dat};
            \addplot[red, dashed] {2*x + 1};
        \end{axis}
    \end{tikzpicture}
\end{frame}
```

For inline data, use `\addplot coordinates {(0,0) (1,2) (2,5)};`. For external data, point `\addplot table {filename.dat}` at a whitespace-separated columns file.

See [reference/charts-and-plots.md](reference/charts-and-plots.md) for axis styling, log scales, bar/scatter/box plots, and styling presets.

### I. Gantt charts (`pgfgantt`)

```latex
\usepackage{pgfgantt}

\begin{frame}{Project timeline}
    \begin{ganttchart}[
        hgrid, vgrid, x unit=8mm,
        bar/.append style={fill=blue!40}]{1}{12}
        \gantttitle{2026}{12} \\
        \gantttitlelist{1,...,12}{1} \\
        \ganttbar{Literature}{1}{3} \\
        \ganttbar{Method}{3}{7}    \\
        \ganttbar{Experiments}{6}{10} \\
        \ganttmilestone{Submit}{11}
    \end{ganttchart}
\end{frame}
```

Adjust `x unit` to fit; use `vrule` and milestones for key dates. For sub-tasks, use `\ganttgroup` to wrap children.

### J. Citations

For `biblatex` (modern, recommended):

```latex
\usepackage[backend=biber, style=authoryear, sorting=ynt]{biblatex}
\addbibresource{refs.bib}

% In a frame:
The method was proposed in \cite{author2024}.

% At the end of the deck:
\begin{frame}[allowframebreaks]{References}
    \printbibliography
\end{frame}
```

Compile sequence: `pdflatex` → `biber` → `pdflatex` → `pdflatex`.

For `natbib` (legacy IEEE / ACM):

```latex
\usepackage[numbers]{natbib}
\bibliographystyle{ieeetr}

% At the end:
\bibliography{refs}
```

Compile sequence: `pdflatex` → `bibtex` → `pdflatex` → `pdflatex`.

See [reference/citations.md](reference/citations.md) for inline-citation styles (`\citet`, `\citep`, `\textcite`, `\parencite`), narrow citations for slide constraints, and the "References" frame pattern with `\tiny` sizing for long bibliographies.

### K. Cover pages and title slides

The default cover from `\frame{\titlepage}` is plain. For a richer cover:

```latex
\begin{frame}[plain]
    \begin{tikzpicture}[remember picture, overlay]
        \node[anchor=south west, xshift=8mm, yshift=20mm]
            at (current page.south west) {%
            \begin{minipage}{0.85\paperwidth}
                {\Huge\bfseries Talk Title}\par\vspace{4mm}
                {\large An optional subtitle that spans wider}\par\vspace{10mm}
                {\normalsize Author Name}\par
                {\small Affiliation}\par
                {\small Date}
            \end{minipage}};
    \end{tikzpicture}
\end{frame}
```

For a coloured-background cover, set `\setbeamercolor{background canvas}{bg=...}` inside a group scope. A complete worked example of a full-bleed branded cover lives at <https://github.com/wenlzhang/chalmers-beamer> (the companion `ppt-to-beamer-skill` output).

### L. Overlays (incremental reveals)

Beamer's "action specifications" `<N-M>` reveal content on selected sub-slides within a frame:

```latex
\begin{frame}{Reveal one bullet at a time}
    \begin{itemize}
        \item<1-> First point (visible from slide 1)
        \item<2-> Second point
        \item<3-> Third point
    \end{itemize}
\end{frame}
```

Other common patterns:

| Command | Effect |
|---------|--------|
| `\onslide<2->{content}`  | content appears from slide 2 onwards (others see empty space) |
| `\uncover<2->{content}`  | same but with `\onslide{}` semantics (transparent before) |
| `\only<2-3>{content}`    | content present *only* on slides 2 and 3 (no space reserved) |
| `\visible<2->{content}`  | content visible from slide 2 onwards (space always reserved) |
| `\alt<2>{A}{B}`          | show A on slide 2, B otherwise |
| `\temporal<2-3>{before}{during}{after}` | three-state transition |

For frame-level overlay tests, use `\begin{frame}[t,plain]<2->`.

See [reference/overlays-animations.md](reference/overlays-animations.md) for staged equations, alternative figure overlays, button highlighting, and the difference between `\uncover` (reserves space) and `\only` (does not).

### M. Animations and transitions

Beamer supports both *intra-frame* animations (handled by overlays above) and *inter-frame* slide transitions:

```latex
\transblindshorizontal     % between frames; needs PDF viewer support
\transboxin
\transdissolve[duration=0.5]
\transfade
\transglitter
\transsplitverticalin
\transwipe[direction=90]
```

Place inside the frame whose entry should use the transition. Most PDF viewers (Adobe Acrobat in presentation mode, `pdfpc`, `dspdfviewer`) honour these; web-based viewers usually don't.

For animated graphics (e.g. animated GIFs of plots), use `\animategraphics{12}{frame-}{0}{59}` from the `animate` package — 12 fps, frames `frame-0.png`..`frame-59.png`. This embeds a JavaScript-driven animation; only works in `pdfpc` and Adobe Acrobat.

### N. Speaker notes and two-screen view

```latex
\begin{frame}{Slide content}
    Visible to the audience.
    \note{
        Hidden notes only the presenter sees.
        Mention timing, anecdote, source.
    }
\end{frame}
```

Enable presenter view by adding to the preamble:

```latex
\setbeameroption{show notes on second screen=right}
```

Render with `pdfpc deck.pdf` for the actual two-screen experience, or `\setbeameroption{show notes}` for inline notes during draft.

### O. Handouts (one slide per page, no overlays)

```latex
\documentclass[aspectratio=169, handout]{beamer}
\usepackage{pgfpages}
\pgfpagesuselayout{4 on 1}[a4paper, landscape, border shrink=5mm]
```

`handout` collapses all overlays to a single slide per frame. `pgfpages` packs N frames per A4 page for printing — `2 on 1`, `4 on 1`, `6 on 1` are common.

## Compilation

For decks **without** citations: `pdflatex deck.tex` twice (TOC needs the second pass).

For decks **with** `biblatex` / `biber`:

```bash
pdflatex deck.tex
biber deck
pdflatex deck.tex
pdflatex deck.tex
```

For decks with `natbib` / `bibtex`:

```bash
pdflatex deck.tex
bibtex deck
pdflatex deck.tex
pdflatex deck.tex
```

For decks with `pgfplots` external data, pre-generate the data files; Beamer / pgfplots do not run computations.

## Verifying the output

After compiling, do a quick visual sweep:

1. Cover slide: title + author + date all visible and not overlapping.
2. Outline slide: `\tableofcontents` populated (re-run if empty — TOC needs two passes).
3. Section dividers (if any): styled consistently.
4. Body frames: no content runs off the bottom (look for `Overfull \vbox` warnings).
5. Figures: present, scaled, not cropped.
6. Citations: numbers / author-year present, bibliography frame populated.
7. Overlays: walk through the deck in a PDF viewer and confirm the staged reveals work.

If overfull warnings appear, try `\footnotesize` or `\scriptsize` on the affected frame, or split the content across two frames.

## Reference files

- [reference/beamer-basics.md](reference/beamer-basics.md) — full preamble template, package load order, document-class options
- [reference/layouts.md](reference/layouts.md) — column patterns, mixed widths, figure-beside-text
- [reference/graphics.md](reference/graphics.md) — figures, subfigures, wrap-around text, TikZ figures
- [reference/math-and-units.md](reference/math-and-units.md) — aligned equations, theorem environments, `siunitx` config
- [reference/charts-and-plots.md](reference/charts-and-plots.md) — pgfplots, pgfgantt, bar / scatter / box-plot patterns
- [reference/overlays-animations.md](reference/overlays-animations.md) — action specifications, staged equations, transitions
- [reference/themes.md](reference/themes.md) — full theme catalogue, four-layer override mechanism, colour theming
- [reference/citations.md](reference/citations.md) — biblatex vs natbib, References frame patterns

## Anti-patterns to avoid

- **Don't paste plain LaTeX paragraphs into slides.** Bullet points, short phrases, or columns scale; flowing prose does not.
- **Don't use `\section` without `\AtBeginSection`** if you want section dividers. The TOC entry appears but no divider slide is auto-generated (Beamer behaviour).
- **Don't mix `subfig` and `subcaption`** in the same document — pick one. `subcaption` is the modern choice.
- **Don't load `hyperref` early.** It must come *last* in the preamble; loading it before other packages breaks PDF anchors silently.
- **Don't forget `\protect`** when using fragile commands (`\cite`, `\verb`) inside frame titles. Use `\begin{frame}[fragile]` for any frame with verbatim content.
- **Don't aim for one perfect slide.** Beamer rewards iteration: compile early, see the result, adjust. The compile cycle is ~1 second on a modern laptop.
- **Don't claim a deck is "done" without checking it on the projector / screen size you will present from.** Aspect-ratio mismatches between authoring and projection are the #1 source of "fonts are tiny" surprises.

## Output style

When generating Beamer code on the user's behalf:

- Use UK English in prose unless the user's existing content is American.
- Default to `aspectratio=169` (modern projectors).
- Default to `\usepackage{booktabs}` for tables; never use vertical lines.
- Default to `siunitx` for units in scientific / engineering decks.
- Default to `biblatex` + `biber` for citations unless the user mentions IEEE / ACM journal constraints.
- Add a top-of-file comment block summarising the deck's purpose and how to compile.
- Do not use emojis in `.tex` source unless the user explicitly asks.
