# Math and units — equations, theorems, siunitx

## Required packages

```latex
\usepackage{amsmath}     % aligned environments, \text{}, advanced equation tools
\usepackage{amssymb}     % \mathbb{}, \mathcal{}, \nmid, \nparallel, ...
\usepackage{mathtools}   % patches and improvements to amsmath
\usepackage{siunitx}     % units: \SI{9.81}{\meter\per\second\squared}
```

## Single equation

```latex
\begin{equation}
    \dot{\mathbf{x}} = f(\mathbf{x}, \mathbf{u}),
    \label{eq:state}
\end{equation}
```

Reference with `Eq.~\eqref{eq:state}` to get `Eq. (1)` formatting.

## Multi-line aligned equations

```latex
\begin{align}
    a &= b + c, \\
    d &= e + f.
\end{align}
```

`&` is the alignment column. Each line gets an equation number. Use `align*` for unnumbered.

## Cases

```latex
\begin{equation}
    f(x) =
    \begin{cases}
        x^2 & \text{if } x \geq 0, \\
        -x  & \text{if } x < 0.
    \end{cases}
\end{equation}
```

## Theorems and definitions

```latex
\newtheorem{theorem}{Theorem}
\newtheorem{definition}{Definition}
\newtheorem{lemma}{Lemma}

\begin{frame}{Result}
    \begin{theorem}[Pythagorean]
        For a right triangle, $a^2 + b^2 = c^2$.
    \end{theorem}
    \begin{proof}
        Standard.
    \end{proof}
\end{frame}
```

Beamer pre-defines `theorem`, `corollary`, `lemma`, `proof`, `example`, `definition` if you `\usepackage{theorem}` or use the default Beamer settings. Customise the style with `\setbeamertemplate{theorems}[numbered]` for numbered presentations.

## `siunitx` quick reference

```latex
\SI{9.81}{\meter\per\second\squared}    % 9.81 m/s²
\SI{120}{\kilo\meter\per\hour}           % 120 km/h
\SI{25}{\celsius}                        % 25 °C
\SI{1.5e-3}{\watt}                       % 1.5 × 10⁻³ W
\num{1.23e-4}                            % 1.23 × 10⁻⁴ (just the number)
\numlist{1; 2; 3}                        % 1, 2, and 3
\SIrange{10}{20}{\kilo\gram}             % 10 kg to 20 kg
\ang{30}                                 % 30°
```

Global configuration:

```latex
\sisetup{
    per-mode = symbol,        % km/h not km h^{-1}
    range-phrase = --,        % 10--20 km/h
    range-units = single,     % 10--20 kg, not 10 kg--20 kg
    detect-all,               % adapt to surrounding font
}
```

## Step-by-step equation reveal

Reveal an equation one term at a time:

```latex
\begin{frame}{Derivation}
    \begin{equation*}
        \underbrace{\dot{\mathbf{x}}}_{\text{state derivative}}
        = \underbrace{\mathbf{A}\mathbf{x}}_{\onslide<2->{\text{drift}}}
        + \onslide<3->{\underbrace{\mathbf{B}\mathbf{u}}_{\text{control}}}
    \end{equation*}
\end{frame}
```

Use `\onslide` (preserves space) for incremental reveal of equation parts.

## Boxed / highlighted equation

```latex
\begin{equation}
    \boxed{
        \dot{\mathbf{x}} = f(\mathbf{x}, \mathbf{u})
    }
\end{equation}
```

For coloured highlight:

```latex
\usepackage{xcolor}
\colorbox{yellow!30}{$E = mc^2$}
```

## Common pitfalls

- **Don't use `$$ ... $$`** for display equations — it's plain-TeX, not LaTeX, and confuses the spacing engine. Use `\[ ... \]` or `equation{}` instead.
- **Don't mix `eqnarray` and `align`** — `eqnarray` is obsolete (poor spacing). Always use `align` / `aligned` / `gather`.
- **`\text{}` not `\mathrm{}`** for inline prose in math — `\mathrm` uses math-italics rules and breaks ligatures.
- **`siunitx` doesn't auto-detect math mode** in all places — be explicit with `$\SI{...}{...}$` if you see odd rendering.
