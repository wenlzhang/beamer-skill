# Charts and plots — pgfplots, pgfgantt

## `pgfplots` setup

```latex
\usepackage{pgfplots}
\pgfplotsset{compat=newest}    % match the installed version
```

`compat=newest` opts into the latest behaviour. For reproducibility across machines pin a specific version: `\pgfplotsset{compat=1.18}`.

## Basic line plot

```latex
\begin{frame}{Line plot}
    \begin{tikzpicture}
        \begin{axis}[
            width=0.75\textwidth, height=0.6\textheight,
            xlabel={Time [s]}, ylabel={Velocity [m/s]},
            grid=major, legend pos=north west,
        ]
            \addplot[blue, thick] coordinates {
                (0,0) (1,2) (2,5) (3,9) (4,14) (5,18)
            };
            \addlegendentry{Measured}
            \addplot[red, dashed, thick] {3*x};
            \addlegendentry{Linear fit}
        \end{axis}
    \end{tikzpicture}
\end{frame}
```

## Plot from a data file

```latex
\addplot[blue, thick] table[x=t, y=v] {data.dat};
```

Where `data.dat` is whitespace-separated with a header line:

```
t   v
0   0
1   2
2   5
...
```

## Log scale, axis options

```latex
\begin{axis}[
    xmode=log, ymode=log,                 % both log
    xmin=1, xmax=100, ymin=1e-3, ymax=1,
    xlabel={Frequency [Hz]},
    ylabel={Gain},
    width=0.7\textwidth, height=0.5\textheight,
    grid=both, minor tick num=4,
    legend pos=south east,
]
    ...
\end{axis}
```

## Bar chart

```latex
\begin{axis}[
    ybar, ymin=0,
    bar width=12pt,
    width=0.7\textwidth, height=0.5\textheight,
    xtick=data, symbolic x coords={A, B, C, D},
    nodes near coords,
    ylabel={Accuracy [\%]},
]
    \addplot coordinates {(A, 85) (B, 91) (C, 88) (D, 94)};
\end{axis}
```

## Scatter plot

```latex
\begin{axis}[
    xlabel={x}, ylabel={y},
    scatter/use mapped color={draw=black, fill=blue!60},
]
    \addplot[only marks, mark=*] coordinates {
        (1, 2) (2, 3) (3, 5) (4, 7) (5, 11)
    };
\end{axis}
```

## Multiple plots with shared axis

```latex
\begin{axis}[...]
    \addplot[blue] table {ppo.dat};
    \addplot[red]  table {sac.dat};
    \addplot[green!60!black] table {td3.dat};
    \legend{PPO, SAC, TD3}
\end{axis}
```

## `pgfgantt` — Gantt charts

```latex
\usepackage{pgfgantt}

\begin{frame}{Project timeline}
    \resizebox{\textwidth}{!}{%
    \begin{ganttchart}[
        hgrid, vgrid,
        x unit=8mm, y unit chart=8mm,
        bar/.append style={fill=blue!40},
        milestone/.append style={fill=red},
        title height=1, title label font=\small\bfseries,
    ]{1}{12}
        \gantttitle{2026}{12} \\
        \gantttitlelist{1,...,12}{1} \\
        \ganttbar{Literature review}{1}{3} \\
        \ganttbar{Implementation}{3}{9}    \\
        \ganttbar{Experiments}{7}{11}      \\
        \ganttbar{Writing}{10}{12}         \\
        \ganttmilestone{Submit}{12}
    \end{ganttchart}}
\end{frame}
```

Options:

| Option | Effect |
|--------|--------|
| `hgrid, vgrid`           | Horizontal and vertical grid lines |
| `x unit=8mm`             | Width per time unit (adjust to fit) |
| `y unit chart=8mm`       | Height per row |
| `bar/.append style={...}`| Default bar colour / border |
| `progress=N`             | Render bar with N% completion shaded |
| `inline`                 | Place task name on the bar instead of left |

For sub-tasks:

```latex
\ganttgroup{Phase 1}{1}{6} \\
\ganttbar{Subtask A}{1}{3} \\
\ganttbar{Subtask B}{3}{6} \\
```

## Box plot (statistical)

`pgfplots` has a built-in `boxplot` library:

```latex
\usepgfplotslibrary{statistics}

\begin{axis}[boxplot/draw direction=y]
    \addplot+[boxplot prepared={
        lower whisker=1, lower quartile=2,
        median=3, upper quartile=4, upper whisker=5,
    }] coordinates {};
\end{axis}
```

Or pass raw data and let `pgfplots` compute the statistics:

```latex
\addplot+[boxplot] table[y index=0] {data.dat};
```

## Common pitfalls

- **`compat=newest` causes silent breakage** across pgfplots versions. For shared / archived decks pin a version.
- **Large data files slow compilation**. For > 10k points, use `externalize` to pre-build each figure once:

  ```latex
  \usetikzlibrary{external}
  \tikzexternalize[prefix=figures/cache/]
  ```

- **`\resizebox{\textwidth}{!}{...}`** is a hammer that scales the whole picture. Easier to set explicit `width=`/`height=` in the axis options.
- **`xtick=data`** uses every data point as a tick — fine for ≤ 10 points; ugly above that. For dense data use `xtick distance=10`.
