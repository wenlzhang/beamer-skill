# Worked example — building a conference-talk deck

End-to-end example of asking Claude Code (or any agentic LLM with this skill installed) to build a 20-minute conference talk on a fictional topic. Substitute your own subject matter.

## Initial prompt

> *"Build a 20-minute Beamer deck for a conference talk titled 'Graph Neural Networks for Traffic Prediction'. Sections: Introduction, Background, Method, Experiments, Discussion. Use the `metropolis` theme. 16:9 aspect ratio. Default to siunitx and booktabs. Citations via biblatex/biber, author-year style. Output to `~/Documents/gnn-talk/`."*

## Stage 1 — bootstrap the deck

The LLM creates `~/Documents/gnn-talk/gnn-talk.tex`:

```latex
% ==============================================================
%   Graph Neural Networks for Traffic Prediction
%   20-minute conference talk; ~18 content slides + cover + outline
%   Compile: pdflatex gnn-talk.tex && biber gnn-talk && pdflatex gnn-talk.tex && pdflatex gnn-talk.tex
% ==============================================================
\documentclass[aspectratio=169]{beamer}
\usetheme{metropolis}

\usepackage[utf8]{inputenc}
\usepackage[T1]{fontenc}
\usepackage[british]{babel}
\usepackage{amsmath, amssymb}
\usepackage{siunitx}
\usepackage{graphicx}
\usepackage{subcaption}
\usepackage{booktabs}
\usepackage{pgfplots}
\pgfplotsset{compat=newest}
\usepackage[backend=biber, style=authoryear, sorting=ynt]{biblatex}
\addbibresource{refs.bib}
\usepackage{hyperref}

\title{Graph Neural Networks for Traffic Prediction}
\subtitle{A 20-minute introduction}
\author{Author Name}
\institute{Affiliation}
\date{\today}

\begin{document}

\frame{\titlepage}

\begin{frame}{Outline}
    \tableofcontents
\end{frame}

\section{Introduction}

\begin{frame}{Why traffic prediction matters}
    \begin{itemize}
        \item Urban congestion costs cities billions per year
        \item Real-time prediction enables adaptive signal control,
              route advisories, fleet dispatch
        \item Sensors are everywhere; ML is mature; what is missing?
    \end{itemize}
\end{frame}

\begin{frame}{The problem in one slide}
    \begin{columns}[T, onlytextwidth]
        \begin{column}{0.55\textwidth}
            \textbf{Given:}
            \begin{itemize}
                \item Sensor graph $G = (V, E)$ with $|V| = N$ road segments
                \item Speed history $\mathbf{X}_{t-T:t} \in \mathbb{R}^{T \times N}$
            \end{itemize}
            \textbf{Predict:}
            \begin{itemize}
                \item Future speeds $\hat{\mathbf{X}}_{t+1:t+H}$ at horizon $H$
            \end{itemize}
        \end{column}
        \begin{column}{0.42\textwidth}
            \centering
            \includegraphics[width=\linewidth]{figures/road-graph.pdf}
            \captionof{figure}{Sensor graph of central Stockholm.}
        \end{column}
    \end{columns}
\end{frame}

\section{Background}

\begin{frame}{Three approaches}
    \begin{block}{Classical}
        ARIMA, Kalman filters; per-sensor; ignore spatial structure.
    \end{block}
    \begin{block}{Recurrent neural networks}
        LSTM / GRU on per-sensor time series; spatial dependency captured
        only by stacking multiple sensors' inputs.
    \end{block}
    \begin{exampleblock}{Graph neural networks}
        Explicitly use $G$. Spatial convolution + temporal recurrence.
        State of the art since DCRNN (\cite{li2018dcrnn}).
    \end{exampleblock}
\end{frame}

\section{Method}

\begin{frame}{Spatial--temporal graph convolution}
    \begin{equation}
        \mathbf{H}^{(l+1)} = \sigma\Big(
            \underbrace{\tilde{\mathbf{A}} \mathbf{H}^{(l)} \mathbf{W}_s^{(l)}}_{\text{spatial}}
            + \underbrace{\mathbf{T} \mathbf{H}^{(l)} \mathbf{W}_t^{(l)}}_{\text{temporal}}
        \Big),
        \label{eq:stgcn}
    \end{equation}
    where $\tilde{\mathbf{A}}$ is the normalised graph adjacency and
    $\mathbf{T}$ is a temporal convolution kernel.
    \pause
    Three takeaways:
    \begin{itemize}
        \item Spatial and temporal terms are linear in the hidden state $\mathbf{H}^{(l)}$
        \item Adjacency $\tilde{\mathbf{A}}$ encodes the road graph (sparse)
        \item No recurrence needed -- pure feed-forward stack
    \end{itemize}
\end{frame}

\section{Experiments}

\begin{frame}{Setup}
    Datasets: PEMS-BAY, METR-LA. Train/val/test split 70/10/20.
    Horizon $H = \SI{60}{\minute}$ ahead in $\SI{5}{\minute}$ steps.
\end{frame}

\begin{frame}{Results}
    \centering
    \begin{tabular}{lccc}
        \toprule
        Method & MAE & RMSE & MAPE \\
        \midrule
        ARIMA   & 4.21 & 7.85 & 12.4\% \\
        LSTM    & 3.65 & 6.92 & 10.8\% \\
        STGCN   & \textbf{2.94} & \textbf{5.71} & \textbf{8.9\%} \\
        \bottomrule
    \end{tabular}
\end{frame}

\begin{frame}{Training curves}
    \begin{tikzpicture}
        \begin{axis}[
            width=0.75\textwidth, height=0.6\textheight,
            xlabel={Epoch}, ylabel={Validation MAE},
            grid=major, legend pos=north east,
        ]
            \addplot[blue, thick] coordinates {
                (0, 5.5) (10, 4.2) (20, 3.5) (30, 3.1) (40, 2.95) (50, 2.94)
            };
            \addlegendentry{STGCN}
            \addplot[red, dashed, thick] coordinates {
                (0, 5.5) (10, 4.7) (20, 4.1) (30, 3.8) (40, 3.7) (50, 3.65)
            };
            \addlegendentry{LSTM}
        \end{axis}
    \end{tikzpicture}
\end{frame}

\section{Discussion}

\begin{frame}{What worked, what didn't}
    \begin{block}{Worked}
        \begin{itemize}
            \item Adjacency learned from data outperforms hand-coded
            \item Multi-horizon training (predict $5, 10, ..., 60$ min) helps short horizons
        \end{itemize}
    \end{block}
    \begin{alertblock}{Didn't work}
        \begin{itemize}
            \item Naive transformer baseline: more parameters, worse generalisation
            \item Adding weather features: minor improvement, not worth the complexity
        \end{itemize}
    \end{alertblock}
\end{frame}

\begin{frame}{Future work}
    \begin{itemize}
        \item Transfer across cities (Stockholm $\to$ Gothenburg)
        \item Uncertainty quantification: predict intervals, not point values
        \item Integration with downstream signal-control loop
    \end{itemize}
\end{frame}

\begin{frame}[allowframebreaks]{References}
    \printbibliography[heading=none]
\end{frame}

\end{document}
```

And `~/Documents/gnn-talk/refs.bib`:

```bibtex
@inproceedings{li2018dcrnn,
    author    = {Li, Y. and Yu, R. and Shahabi, C. and Liu, Y.},
    title     = {Diffusion Convolutional Recurrent Neural Network},
    booktitle = {ICLR},
    year      = {2018},
}
```

## Stage 2 — compile and inspect

```bash
cd ~/Documents/gnn-talk
pdflatex gnn-talk.tex
biber gnn-talk
pdflatex gnn-talk.tex
pdflatex gnn-talk.tex
```

Look for `Overfull \vbox` warnings — none in this case, the deck fits cleanly.

## Stage 3 — follow-up edits

A few rounds of refinement are typical. Examples:

### "Add an overlay so equation (1) reveals one term at a time"

The LLM modifies the Method frame:

```latex
\begin{equation}
    \mathbf{H}^{(l+1)} = \sigma\Big(
        \onslide<1->{\underbrace{\tilde{\mathbf{A}} \mathbf{H}^{(l)} \mathbf{W}_s^{(l)}}_{\text{spatial}}}
        \onslide<2->{+ \underbrace{\mathbf{T} \mathbf{H}^{(l)} \mathbf{W}_t^{(l)}}_{\text{temporal}}}
    \Big).
\end{equation}
```

The frame now becomes 2 sub-slides; on slide 1 only the spatial term shows; on slide 2 both terms appear together.

### "Add a Gantt chart slide before the references showing 6 months of planned future work"

The LLM adds a new frame:

```latex
\usepackage{pgfgantt}

\begin{frame}{Planned future work}
    \resizebox{\textwidth}{!}{%
    \begin{ganttchart}[
        hgrid, vgrid, x unit=12mm,
        bar/.append style={fill=blue!40},
    ]{1}{6}
        \gantttitle{Months}{6} \\
        \gantttitlelist{1,...,6}{1} \\
        \ganttbar{Cross-city transfer}{1}{4} \\
        \ganttbar{Uncertainty quantification}{2}{5} \\
        \ganttbar{Signal-control integration}{4}{6} \\
        \ganttmilestone{Submit ICRA}{6}
    \end{ganttchart}}
\end{frame}
```

### "Make the deck handout-ready for printing"

Compile a second version with the `handout` class option to a different output name:

```bash
pdflatex -jobname=gnn-talk-handout "\PassOptionsToClass{handout}{beamer} \input{gnn-talk.tex}"
biber gnn-talk-handout
pdflatex -jobname=gnn-talk-handout "\PassOptionsToClass{handout}{beamer} \input{gnn-talk.tex}"
pdflatex -jobname=gnn-talk-handout "\PassOptionsToClass{handout}{beamer} \input{gnn-talk.tex}"
```

All overlays collapse to single pages; the deck becomes 14 pages (down from 16 with overlays expanded).

## Stage 4 — final verification

Before declaring done, sweep through the rendered PDF:

1. Cover slide — title, author, date all visible
2. Outline — populated with five section names
3. Section dividers (auto-generated by `metropolis`) — coloured, clean
4. Equations — render with correct subscripts and brace-grouping
5. Table — booktabs rules (top/mid/bottom), no vertical lines
6. Plot — axes labelled, legend visible, lines distinguishable
7. Gantt — bars visible, milestone marker visible
8. References — bibliography populated

## Lessons from this walkthrough

- **Start with the deck skeleton + outline, fill content second.** Cheaper to iterate.
- **Compile after each major change.** Catching an `Overfull \vbox` early is easier than debugging a half-broken deck.
- **Use `\pause` and overlays sparingly.** Each overlay-laden frame adds 2-5 PDF pages to the rendered deck; reviewers may not have your overlay context.
- **`metropolis` does not auto-add section dividers** — you get the section name only in the navigation bar and in TOC. Add explicit `\section{...}` divider frames if you want named transitions in the talk.
