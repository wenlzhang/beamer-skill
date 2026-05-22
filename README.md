# Beamer Skill

A Claude Skill for creating, extending, and polishing LaTeX Beamer presentations *with* an agentic LLM (Claude Code, OpenAI Codex CLI, Cursor, Aider, or any other runtime that follows structured prompts).

Drop this skill in front of Claude Code and ask for "a Beamer deck about X with sections Y and Z" — or hand it a draft `.tex` file and ask to add columns, figures, plots, citations, or animations. The skill encodes the patterns, package incantations, and Beamer-internals gotchas so the LLM does not have to rediscover them every time.

## Credits and licensing context

This skill drives the upstream **Beamer LaTeX class** authored by Till Tantau and currently maintained by Joseph Wright. The Beamer project itself is hosted at:

<https://github.com/josephwright/beamer>

The Beamer class is distributed under the **LaTeX Project Public License (LPPL) 1.3c+** and the **GNU General Public License (GPL) v2+** for code, with documentation under **GFDL 1.3+** or LPPL 1.3c+. This skill ships independently under the MIT licence (`LICENSE`) and contains no Beamer source code — it only describes how to use Beamer's public API. The Beamer project is in no way affiliated with or endorsed by this skill; please direct Beamer-class bugs and feature requests upstream.

See `NOTICE` for the complete attribution and the licence-compatibility analysis.

## How this skill came to be

This skill is **bottom-up**, not top-down. It was distilled from a steady stream of small Beamer authoring tasks — building decks, adding figures, restyling, debugging compile errors — that an LLM was already helping with. Rather than rediscover the same patterns and workarounds every conversation, the recurring know-how is now in one `SKILL.md` plus topic-focused reference files.

That means the skill is opinionated rather than exhaustive. It reflects what reliably works in practice, not every conceivable Beamer feature. Pull requests that broaden the coverage are welcome.

## What this skill is

A Claude Skill is a Markdown file (`SKILL.md`) with YAML frontmatter that an agentic LLM matches against user prompts. When the user asks to create or modify a Beamer deck, the LLM reads `SKILL.md`, consults the `reference/` files as needed, and produces the LaTeX source.

The skill is **portable**: clone the repo into `~/.claude/skills/beamer/` and Claude Code picks it up automatically. For other agentic runtimes (Codex CLI, Cursor, Aider, …) the `SKILL.md` is plain Markdown — pass it in as an instructions / system-prompt file.

## Installation

### Claude Code (primary target)

```bash
git clone https://github.com/wenlzhang/beamer-skill ~/.claude/skills/beamer
```

Or, if you maintain a Claude Code plugin manifest, add this repo as a plugin dependency.

### Codex CLI and other agentic LLMs

`SKILL.md` is plain Markdown — there is nothing Claude-specific about its contents. Hand it to any agentic-LLM runtime that accepts a structured prompt:

```bash
# Codex CLI example -- pass SKILL.md as an instructions file
codex --instructions ~/path/to/beamer-skill/SKILL.md \
      "Make a 20-minute conference deck about graph neural networks for traffic prediction."
```

The frontmatter (`name`, `description`) is matched by Claude Code automatically; in other runtimes you may need to surface the description as part of your own system prompt. The body of `SKILL.md` plus the files under `reference/` work uniformly across runtimes.

Quality depends on the LLM's facility with LaTeX / Beamer — most frontier models handle this comfortably as of 2026.

## Usage

Once installed, simply ask Claude Code (or your chosen agentic LLM) to build / extend / restyle a Beamer deck. The skill activates automatically when the request mentions Beamer, LaTeX presentations, slides, or specific patterns it covers (columns, blocks, overlays, …).

## Example prompts

Copy any of these and adapt the topic, sections, and theme to your situation.

### 1. New deck from scratch

> *"Create a 15-minute Beamer deck on 'Reinforcement Learning for Robotic Manipulation' with sections Introduction, Background, Method, Experiments, Conclusions. Use the `metropolis` theme, 16:9 aspect ratio. Default to `siunitx` and `booktabs`. Save it to `~/Documents/rl-talk/`."*

### 2. Add specific patterns to an existing deck

> *"Open `~/Documents/rl-talk/slides.tex`. On the 'Method' slide, replace the bullet list with a two-column layout — left column the equation block (use the equation from `~/Documents/rl-talk/method.tex`), right column an `\includegraphics` of `figures/architecture.pdf`. Add overlays so the equation arrives on slide 1 and the figure on slide 2."*

### 3. Restyle (theme / colour swap)

> *"Restyle `~/Documents/rl-talk/slides.tex` to use a dark colour scheme: navy-blue primary (`#0E2A47`), cream background (`#F5F2EC`), white text for cover and section dividers. Keep the existing theme structure but swap the palette."*

### 4. Add citations and bibliography

> *"My slides at `~/Documents/rl-talk/slides.tex` cite three papers. Set up `biblatex` with `biber` backend, author-year style. Add a `\printbibliography` frame at the end. The bibliography file is at `~/Documents/rl-talk/refs.bib`. Tell me the compile sequence to use."*

### 5. Add a Gantt chart for project timeline

> *"Add a new frame to `~/Documents/rl-talk/slides.tex` before the Conclusions section. The frame should show a 12-month Gantt chart of the planned future work: 'Literature review' months 1-3, 'Implementation' months 3-9, 'Experiments' months 7-11, 'Writing' months 10-12, with a 'Submission' milestone at month 12. Use `pgfgantt`."*

### 6. Add a pgfplots figure

> *"On the 'Experiments' frame of `~/Documents/rl-talk/slides.tex`, add a `pgfplots` figure showing training reward vs. episode for three policies (PPO, SAC, TD3). Data is in `~/Documents/rl-talk/data/{ppo,sac,td3}.dat` (two columns: episode, reward). Use the policy name as the legend, log-scale the x-axis."*

### 7. Speaker notes for two-screen view

> *"Add speaker notes to every content frame in `~/Documents/rl-talk/slides.tex`. Notes should mention timing (roughly 1 minute per slide) and one anecdote or transition phrase per slide. Enable `\setbeameroption{show notes on second screen=right}` and tell me how to render with `pdfpc` for the actual two-screen presenter view."*

### 8. Handout mode (printable)

> *"Compile `~/Documents/rl-talk/slides.tex` in handout mode (`handout` document-class option, four frames per A4 page in landscape). Output as `~/Documents/rl-talk/handout.pdf`."*

### 9. End-to-end with GitHub repo creation

> *"Create a Beamer deck for the talk we discussed, save it to `~/Documents/new-talk/`, and once it compiles cleanly, create a public GitHub repository at `myhandle/new-talk` and push the result there. I want `main` for releases and `dev` for ongoing work."*

### Anti-prompts (what the skill is not for)

- *"Write the content of a 20-minute talk on quantum computing."* — that's a writing / domain task, not a layout task; ask without the Beamer framing.
- *"Convert this PowerPoint file to Beamer."* — use the companion [ppt-to-beamer-skill](https://github.com/wenlzhang/ppt-to-beamer-skill) which is specifically designed for that.
- *"Match my university's brand identity exactly."* — same; use [ppt-to-beamer-skill](https://github.com/wenlzhang/ppt-to-beamer-skill) and feed it your institution's `.potx` and logos.

## What you get back

A working `.tex` file (and any companion `.bib`, data files, or figure assets the prompt referenced) that compiles cleanly to a PDF deck. The skill always:

- Adds a top-of-file comment block stating the deck's purpose and the compile command.
- Loads packages in the order Beamer requires (with `hyperref` last).
- Picks sensible defaults (16:9, `booktabs`, `siunitx` for academic work).
- Compiles the result and reports any `Overfull \vbox` warnings.

## Skill contents

```
beamer-skill/
├── .gitignore
├── LICENSE                              MIT (skill code only)
├── NOTICE                               Beamer credits + brand statement
├── README.md                            This file
├── SKILL.md                             The skill (LLM reads this)
├── reference/
│   ├── beamer-basics.md                 Preamble, packages, document class
│   ├── layouts.md                       Columns, mixed-width, figure-beside-text
│   ├── graphics.md                      Figures, subfigures, TikZ figures
│   ├── math-and-units.md                Aligned equations, siunitx, theorems
│   ├── charts-and-plots.md              pgfplots + pgfgantt patterns
│   ├── overlays-animations.md           Action specifications, transitions
│   ├── themes.md                        Theme catalogue + four-layer override
│   └── citations.md                     biblatex vs natbib, References frames
├── examples/
│   └── walkthrough.md                   End-to-end conference-talk example
└── snippets/                            Ready-to-copy LaTeX fragments
    ├── deck-skeleton.tex
    ├── two-column-block-figure.tex
    ├── pgfplots-line-plot.tex
    ├── pgfgantt-12-month.tex
    └── biblatex-frame.tex
```

## Limitations and honest caveats

- **Beamer is the engine, not this skill.** Bugs in the *output* PDF that trace to Beamer itself (theme rendering, navigation symbols, `pgfpages` quirks) should be reported upstream at <https://github.com/josephwright/beamer/issues>, not here.
- **The skill assumes a working TeX Live installation.** Any package mentioned (`biblatex`, `pgfgantt`, `metropolis`, …) must be installed via `tlmgr` or your distribution package manager. The skill does not install dependencies for you.
- **Frontier-model variance.** The quality of generated `.tex` depends on the LLM. Claude Sonnet 4.6+ and Opus 4.6+ produce code that compiles on the first try in our testing; older or smaller models may need 1–2 follow-up corrections.
- **No content generation.** This is a layout / styling tool. If you ask "write me 20 slides about quantum computing", the LLM will write text on its own knowledge — the skill does not vet content for accuracy.

## Contributing

Pull requests welcome, especially:

- New reference files documenting Beamer features the skill doesn't yet cover (e.g. `algorithm2e` for pseudocode, `chemfig` for chemical structures, `circuitikz` for electronics).
- Worked examples under `examples/` for specific talk types (5-minute lightning talk, hour-long lecture, defence-style 45-minute presentation).
- Theme presets under `snippets/` for community-favourite themes (Metropolis, AnnArbor, Pittsburgh, …).

## Licence

This repository is licensed under the **MIT licence** — see `LICENSE`. Outputs the skill produces are LaTeX source that you author; the skill imposes no restrictions on derivative work.

The skill *uses* (without bundling or modifying) the Beamer class, which is licensed under LPPL 1.3c+ / GPL v2+. See `NOTICE` for the complete attribution chain and a brief compatibility note.
