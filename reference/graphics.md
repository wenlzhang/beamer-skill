# Graphics — figures, subfigures, TikZ figures

## Single figure

```latex
\begin{frame}{Title}
    \begin{figure}
        \centering
        \includegraphics[width=0.7\textwidth]{path/to/figure.pdf}
        \caption{Caption text.}
        \label{fig:example}
    \end{figure}
\end{frame}
```

Width sizing options:

| Spec | Use case |
|------|----------|
| `width=\linewidth`       | Full column width (within a `column`) or full frame width |
| `width=\textwidth`       | Same as linewidth at frame level; differs inside columns |
| `width=0.7\textwidth`    | Scale down with margin around the figure |
| `height=0.6\textheight`  | Scale by height (useful for tall figures) |
| `keepaspectratio`        | Use with `width=... height=...` together to never stretch |

## Subfigures (two figures side-by-side with sub-captions)

```latex
\usepackage{subcaption}

\begin{frame}{Side-by-side figures}
    \begin{figure}
        \begin{subfigure}[t]{0.48\textwidth}
            \centering
            \includegraphics[width=\linewidth]{fig1.pdf}
            \caption{Left figure.}
            \label{fig:left}
        \end{subfigure}\hfill
        \begin{subfigure}[t]{0.48\textwidth}
            \centering
            \includegraphics[width=\linewidth]{fig2.pdf}
            \caption{Right figure.}
            \label{fig:right}
        \end{subfigure}
        \caption{Combined caption referring to both.}
    \end{figure}
\end{frame}
```

`\hfill` between sub-figures fills horizontal space evenly. Use `[t]` alignment so unequal-height sub-figures top-align (their captions sit at different heights, which looks fine).

`subcaption` is the modern package. **Do not** use `subfig` — it has been superseded.

## File-format priorities

- **PDF** (vector) — preferred for charts, diagrams, anything with text or sharp lines.
- **PNG** (raster, transparent) — preferred for screenshots, photographs.
- **JPEG** — only for photographs; never for charts (compression artefacts ruin sharp edges).
- **SVG** — convert to PDF first (`rsvg-convert` or Inkscape CLI); LaTeX does not handle SVG directly.

## Cropping / clipping

```latex
\includegraphics[width=0.7\textwidth, trim=10 20 10 30, clip]{figure.pdf}
```

`trim` is `<left> <bottom> <right> <top>` in `bp` (big points; ≈ pixels at 72 DPI). `clip` is required to actually clip — without it, `trim` just shifts the figure.

## TikZ figures inline

For small diagrams that you want to author directly in LaTeX:

```latex
\usepackage{tikz}
\usetikzlibrary{arrows.meta, positioning, shapes.geometric}

\begin{frame}{TikZ diagram}
    \centering
    \begin{tikzpicture}[node distance=10mm]
        \node[draw, rectangle] (a) {Input};
        \node[draw, rectangle, right=of a] (b) {Process};
        \node[draw, rectangle, right=of b] (c) {Output};
        \draw[->] (a) -- (b);
        \draw[->] (b) -- (c);
    \end{tikzpicture}
\end{frame}
```

For block-diagram styling, define styles up front:

```latex
\tikzset{
    block/.style={draw, rectangle, minimum width=2cm, minimum height=8mm,
                  align=center, fill=blue!10},
    arrow/.style={->, >=Latex, thick}
}
```

Then `\node[block]{...}` and `\draw[arrow] ...` everywhere.

## Background image (full-bleed)

```latex
\begin{frame}[plain]
    \begin{tikzpicture}[remember picture, overlay]
        \node[at=(current page.center)] {%
            \includegraphics[width=\paperwidth, height=\paperheight,
                             keepaspectratio]{background.jpg}};
    \end{tikzpicture}
    \begin{textblock*}{0.6\paperwidth}(10mm, 30mm)
        \color{white}\Huge\bfseries Title overlaid on the image
    \end{textblock*}
\end{frame}
```

`[remember picture, overlay]` is essential — without those flags, `current page` is undefined.

## Animation from a sequence of PNGs

```latex
\usepackage{animate}
\animategraphics[loop, autoplay, width=\linewidth]{12}{frame-}{0}{59}
```

Plays 60 frames (`frame-0.png` ... `frame-59.png`) at 12 fps. Only works in Adobe Acrobat and `pdfpc`; other PDF viewers show the first frame statically.

## Common pitfalls

- **Missing file**: LaTeX errors with `File 'figure.pdf' not found`. Check the path is relative to the `.tex` file and the extension is correct.
- **Wrong size**: `width=\textwidth` inside a column gives column width, not page width. Use `\linewidth` when you mean "whatever the current container is".
- **Stretching**: omit `keepaspectratio` and the image stretches when both `width` and `height` are set.
- **Slow compile**: large rasters slow `pdflatex` down. Pre-shrink to your target render size with `magick input.png -resize 1920x output.png`.
