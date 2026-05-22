# Themes — catalogue and customisation

## The four-layer theme system

Every Beamer theme is composed of four orthogonal layers:

| Layer | Controls | Set with |
|-------|----------|----------|
| **Theme (outer + inner combined)** | Overall look + frame structure | `\usetheme{Name}` |
| **Outer theme** | Headline, footline, sidebar, navigation | `\useoutertheme{Name}` |
| **Inner theme** | Title page, blocks, bullets, theorems | `\useinnertheme{Name}` |
| **Colour theme** | Palette of every named element | `\usecolortheme{Name}` |
| **Font theme** | Font sizes and families | `\usefonttheme{Name}` |

A `\usetheme{Madrid}` is shorthand for "load the matching outer + inner + colour + font sub-themes". You can then override any one layer.

## Theme catalogue (upstream Beamer)

| Theme | Visual style | Best for |
|-------|--------------|----------|
| `default`     | Bare, no chrome | Distraction-free research |
| `Antibes`     | Tree of sections at the top | Long tutorials |
| `Bergen`      | Sidebar + section tree | Tutorials |
| `Berkeley`    | Sidebar + title bar | Defences |
| `Berlin`      | Mini navigation bar + section tree | Structured technical talks |
| `Boadilla`    | Title bar + section/subsection bar | Conference talks |
| `boxes`       | Outlined boxes | Tutorials |
| `CambridgeUS` | Title bar + footer with sections | Defences |
| `Copenhagen`  | Bold colour bars | Industry pitches |
| `Darmstadt`   | Section/subsection navigation | Long tutorials |
| `Dresden`     | Wider navigation | Long tutorials |
| `Frankfurt`   | Bold colour blocks | Lecture courses |
| `Goettingen`  | Sidebar | Defences |
| `Hannover`    | Sidebar | Defences |
| `Ilmenau`     | Navigation tree at top | Long structured talks |
| `JuanLesPins` | Compact title bar | Conference talks |
| `Luebeck`     | Section tabs at top | Tutorials |
| `Madrid`      | Title bar + footer | **Industry / corporate default** |
| `Malmoe`      | Outlined boxes | Tutorials |
| `Marburg`     | Sidebar | Defences |
| `Montpellier` | Compact section bar | Academic talks |
| `PaloAlto`    | Sidebar | Tutorials |
| `Pittsburgh`  | Minimal title | Workshop talks |
| `Rochester`   | Bold title bar | Industry |
| `Singapore`   | Subtle headline + section bar | **Academic seminars default** |
| `Szeged`      | Title + section bar | Tutorials |
| `Warsaw`      | Sidebar + navigation | Lecture courses |

### External themes worth knowing

| Theme | Source | Status |
|-------|--------|--------|
| `metropolis` | https://github.com/matze/mtheme | De-facto modern standard since 2016. Install via `tlmgr install beamertheme-metropolis`. |
| `Singapore` (CTAN-modified) | various forks | Cleaner variants exist on CTAN |
| Institution-specific | Various GitHub repos | Search "your-university beamer" |

## Colour themes

| Colour theme | Palette |
|--------------|---------|
| `default`    | Blue-grey |
| `albatross`  | Dark with light text (presentation in dark room) |
| `beaver`     | Red |
| `beetle`     | Brown |
| `crane`      | Yellow |
| `dolphin`    | Light blue |
| `dove`       | Black and white (printing) |
| `fly`        | Grey with white text (dark) |
| `lily`       | Light pastel |
| `monarch`    | Orange / brown |
| `orchid`     | Pink / purple |
| `rose`       | Pink |
| `seagull`    | Grey (subtle) |
| `seahorse`   | Light blue |
| `sidebartab` | Colour for sidebar themes |
| `spruce`     | Green |
| `whale`      | Strong blue |
| `wolverine`  | Yellow on dark blue |

Combine with the main theme:

```latex
\usetheme{Madrid}
\usecolortheme{seahorse}
```

## Font themes

```latex
\usefonttheme{default}            % Beamer default
\usefonttheme{serif}              % Use serif font throughout
\usefonttheme{professionalfonts}  % Tell Beamer not to substitute math fonts
\usefonttheme{structurebold}      % Bold structural elements (frame title, blocks)
\usefonttheme{structureitalicserif}
\usefonttheme{structuresmallcapsserif}
```

`professionalfonts` is the right choice when you load `lmodern`, `mathptmx`, `mathpazo`, `newpxmath`, etc. — it stops Beamer from over-styling math symbols.

## Custom colours

After loading a theme, override specific colour roles:

```latex
\definecolor{Accent}{HTML}{0066CC}

\setbeamercolor{structure}{fg=Accent}            % drives most accent colours
\setbeamercolor{frametitle}{fg=Accent}
\setbeamercolor{title}{fg=Accent}
\setbeamercolor{block title}{fg=white, bg=Accent}
\setbeamercolor{block body}{fg=black, bg=Accent!10}
\setbeamercolor{alerted text}{fg=red!70!black}
```

The `structure` colour is the "primary" knob — many other roles inherit from it. Override structure first, then patch any specific role that doesn't follow.

## Custom fonts

```latex
\setbeamerfont{frametitle}{size=\Large, series=\bfseries, family=\sffamily}
\setbeamerfont{block title}{size=\small, series=\bfseries}
\setbeamerfont{footline}{size=\fontsize{7pt}{8pt}\selectfont}
```

## Remove navigation symbols

The little arrows in the bottom-right corner are usually visual noise:

```latex
\setbeamertemplate{navigation symbols}{}
```

Put this in the preamble after your `\usetheme{...}`.

## Custom templates (advanced)

```latex
% Custom footline: just slide number, right-aligned
\setbeamertemplate{footline}{%
    \hbox to \paperwidth{\hfill\insertframenumber\hspace{8mm}}%
    \vspace{2mm}%
}

% Custom frame title: bold, no rule
\setbeamertemplate{frametitle}{%
    \vspace{2mm}%
    \begin{beamercolorbox}[wd=\paperwidth, leftskip=8mm, rightskip=8mm]{frametitle}
        \usebeamerfont{frametitle}\insertframetitle
    \end{beamercolorbox}%
}
```

## Common pitfalls

- **`\usetheme{}` overrides your colour customisations** if loaded *after*. Set theme first, then `\setbeamercolor{}`.
- **`metropolis` not found**: install with `tlmgr install beamertheme-metropolis` (or your distribution's package).
- **Sidebar themes (`Berkeley`, `Marburg`, `PaloAlto`) eat 25% of the slide width** for the sidebar. Don't pick them for talks with figure-heavy content.
- **`structure` does most of the work**: when fixing one ugly colour, check whether changing `structure` alone propagates. It often does.
