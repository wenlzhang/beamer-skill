# Overlays and animations — action specifications, transitions

## Action specifications (the `<>` syntax)

Beamer's core idea: a single `frame{}` environment becomes *several* PDF pages (sub-slides) when you use overlay specifications. Each `<N>` / `<N->` / `<N-M>` annotation tells Beamer which sub-slides should show the content.

```latex
\begin{frame}{Reveal bullets one at a time}
    \begin{itemize}
        \item<1->  First (visible from sub-slide 1)
        \item<2->  Second
        \item<3->  Third
    \end{itemize}
\end{frame}
```

That single frame produces three PDF pages: slide 1 shows just "First"; slide 2 shows "First" + "Second"; slide 3 shows all three.

## The four reveal commands

| Command | Behaviour | Reserves space? |
|---------|-----------|-----------------|
| `\onslide<N->{...}`  | Visible from slide N onwards. Before N, layout reserves the space; content is invisible. | Yes |
| `\uncover<N->{...}`  | Same as `\onslide` but with semi-transparent rendering before N (with `\setbeamercovered{transparent}`) | Yes |
| `\only<N>{...}`      | Content exists *only* on the matching slides; otherwise gone entirely (no space reserved). | No |
| `\visible<N->{...}`  | Like `\uncover` but always invisible before N (never transparent) | Yes |

Use `\onslide` / `\uncover` / `\visible` when you want the layout to stay stable. Use `\only` when you want different content in the same space (e.g. swap one figure for another).

## Alternative content per slide

```latex
\alt<2>{Showing the alternative}{Showing the default}
```

`\alt<N>{A}{B}` shows A on slide N, B otherwise.

For three or more states:

```latex
\temporal<2-3>{Before slide 2}{During slides 2--3}{After slide 3}
```

## Common patterns

### Build up a list and emphasise items as added

```latex
\begin{itemize}
    \item<+->[\onslide<+>{$\rightarrow$}] First point
    \item<+->[\onslide<+>{$\rightarrow$}] Second point
    \item<+->[\onslide<+>{$\rightarrow$}] Third point
\end{itemize}
```

The `<+->` syntax is a "running counter" — each `+` increments automatically, so you don't have to renumber when reordering. The `\onslide<+>{...}` arrow appears only on the slide where the bullet first appears, then disappears.

### Two figures swapped in place

```latex
\only<1>{\includegraphics[width=0.7\textwidth]{before.pdf}}%
\only<2>{\includegraphics[width=0.7\textwidth]{after.pdf}}
```

Each `\only` is mutually exclusive — slide 1 shows `before`, slide 2 shows `after`, same on-screen position.

### Highlight a single bullet on its slide

```latex
\begin{itemize}
    \item<1-> {\onslide<1>{\color{red}}First item}
    \item<2-> {\onslide<2>{\color{red}}Second item}
    \item<3-> {\onslide<3>{\color{red}}Third item}
\end{itemize}
```

Each item is red on the slide it first appears, then becomes default colour. Useful for "current focus" cues.

### Stage an equation

```latex
\begin{equation*}
    f(x) = \onslide<1->{a_0}
           \onslide<2->{ + a_1 x}
           \onslide<3->{ + a_2 x^2}
           \onslide<4->{ + \cdots}
\end{equation*}
```

## Frame-level overlays

You can attach overlays to the entire `frame` environment:

```latex
\begin{frame}<2->{This frame only appears in handout/section starting slide 2}
    ...
\end{frame}
```

Use with `\againframe<N>{label}` to bring back a frame later in the deck:

```latex
\begin{frame}[label=intuition]
    Original frame content.
\end{frame}

% ... later ...

\againframe<2->{intuition}    % re-show that frame from sub-slide 2 onwards
```

## Transitions (between frames)

Place these inside the frame whose entry should use the transition:

```latex
\transblindshorizontal[duration=0.5]
\transboxin
\transdissolve[duration=0.7]
\transfade
\transglitter
\transsplitverticalin
\transwipe[direction=90]
```

Common options: `duration=<seconds>`, `direction=<degrees>`.

Only `pdfpc`, Adobe Acrobat (presentation mode), and `dspdfviewer` honour these. Web-based / mobile PDF viewers ignore them.

## Animated graphics

```latex
\usepackage{animate}

\animategraphics[loop, autoplay, width=0.7\textwidth]{12}{frame-}{0}{59}
```

Plays frames `frame-0.png` ... `frame-59.png` at 12 fps. Only works in Adobe Acrobat and `pdfpc`.

## Handout mode collapses overlays

```latex
\documentclass[handout]{beamer}
```

In handout mode, all overlay sub-slides collapse to a single page per frame. The fully-revealed state is shown. Useful for printing or sharing the deck after a talk.

To selectively *include* something only in handout mode:

```latex
\mode<handout>{This text only appears in the handout PDF.}
```

To exclude something from handout mode:

```latex
\mode<beamer>{This text only appears in the slide-by-slide PDF.}
```

## Common pitfalls

- **Forgetting overlays make multiple pages.** A frame with `\item<1->`, `<2->`, `<3->` becomes *three* PDF pages. If you don't want that, drop the overlays.
- **`<+->` rebuilds counter per frame.** Across frames it resets to 1; within a frame it increments. Use explicit numbers (`<1->`, `<2->`) for cross-frame coordination.
- **Transitions don't work in all viewers.** Test in your presentation environment before relying on them — many academic conferences project from a generic PDF viewer that ignores them.
