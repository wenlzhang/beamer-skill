# Layouts — columns, multi-pane, mixed widths

## Two-column layout (most common)

```latex
\begin{frame}{Two-column comparison}
    \begin{columns}[T, onlytextwidth]
        \begin{column}{0.48\textwidth}
            Left content.
        \end{column}
        \begin{column}{0.48\textwidth}
            Right content.
        \end{column}
    \end{columns}
\end{frame}
```

| Option | Effect |
|--------|--------|
| `[T]`  | Top-align columns (most common — content of unequal heights starts from the same top edge) |
| `[c]`  | Centre-align (Beamer default; uneven heights look unbalanced) |
| `[b]`  | Bottom-align |
| `onlytextwidth` | Constrain total width to `\textwidth` (no overhang into margins) |

Use `0.48\textwidth` per column with a small visual gap. For exactly half each with no gap, `0.5\textwidth` works but the columns touch.

## Three-column layout

```latex
\begin{columns}[T, onlytextwidth]
    \begin{column}{0.31\textwidth}
        Column 1
    \end{column}
    \begin{column}{0.31\textwidth}
        Column 2
    \end{column}
    \begin{column}{0.31\textwidth}
        Column 3
    \end{column}
\end{columns}
```

## Asymmetric (text + figure)

```latex
\begin{columns}[T, onlytextwidth]
    \begin{column}{0.55\textwidth}
        \begin{itemize}
            \item Point one
            \item Point two
            \item Point three
        \end{itemize}
    \end{column}
    \begin{column}{0.42\textwidth}
        \centering
        \includegraphics[width=\linewidth]{figure.pdf}
        \caption{Caption}
    \end{column}
\end{columns}
```

A 55/42 split with 3% slack is a robust default. Tweak to taste.

## Equation on left, explanation on right

```latex
\begin{columns}[c, onlytextwidth]
    \begin{column}{0.48\textwidth}
        \begin{equation*}
            \dot{\mathbf{x}} = \mathbf{A}\mathbf{x} + \mathbf{B}\mathbf{u}
        \end{equation*}
    \end{column}
    \begin{column}{0.48\textwidth}
        State-space model with state $\mathbf{x}$ and control $\mathbf{u}$.
    \end{column}
\end{columns}
```

Note `[c]` (centre) here — equation height is small, vertically centring it next to the prose looks neater than top-align.

## Nested column inside a block

```latex
\begin{frame}{Block with columns inside}
    \begin{block}{Comparison}
        \begin{columns}[T, onlytextwidth]
            \begin{column}{0.48\textwidth}
                Method A
            \end{column}
            \begin{column}{0.48\textwidth}
                Method B
            \end{column}
        \end{columns}
    \end{block}
\end{frame}
```

Beamer handles this gracefully. The block's title spans the whole width, then the columns split inside the body.

## Avoiding overflow

When content overflows the bottom of the frame (`Overfull \vbox` warning), in priority order:

1. **Reduce content** — bullets shorter, fewer items, simpler figure.
2. **Drop font size**: add `\footnotesize` or `\scriptsize` at the start of the frame body.
3. **Split into two frames**: use `\begin{frame}<<title>>` ... `\end{frame}` `\begin{frame}<<title (cont.)>>` ... `\end{frame}`.
4. **Last resort**: `[allowframebreaks]` frame option for content where natural breaks make sense (reference lists, long bullet lists).

Do **not** scale with `[shrink=N]` for normal slides — it makes text uneven and ugly.

## Figure beside text with caption

```latex
\begin{columns}[T, onlytextwidth]
    \begin{column}{0.5\textwidth}
        \begin{itemize}
            \item Bullet 1
            \item Bullet 2
        \end{itemize}
    \end{column}
    \begin{column}{0.48\textwidth}
        \begin{figure}
            \centering
            \includegraphics[width=\linewidth]{figure.pdf}
            \caption{Figure caption.}
        \end{figure}
    \end{column}
\end{columns}
```

The `figure` environment inside a column gives you the caption + figure number; without it, the image is bare.
