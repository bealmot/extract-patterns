# extract-patterns

A Claude Code slash command that extracts **transferable patterns** from a repository, video, or article at two levels:

| Layer | Question it answers | Output |
|---|---|---|
| **Meta** | How should I work, think, decide, and allocate resources differently? | Mental models, principle updates, workflow changes, open questions |
| **Project** | Which patterns should I adapt into my actual codebases? | Pattern, implementation, target module, adaptation notes, cost/risk |

It is opinionated about evidence: every code claim needs a file and line range, inference is labeled, and the source is treated as untrusted data rather than instructions.

## Install

### As a Claude Code plugin (recommended)

```text
/plugin marketplace add bealmot/extract-patterns
/plugin install extract-patterns@bealmot-plugins
```

Plugin commands are namespaced by plugin name, so invoke it as:

```text
/extract-patterns:extract-patterns <source>
```

### As a standalone command

Cloning and copying the command gives you the shorter `/extract-patterns` invocation:

```bash
git clone https://github.com/bealmot/extract-patterns.git
mkdir -p ~/.claude/commands
cp extract-patterns/commands/extract-patterns.md ~/.claude/commands/
```

Then invoke:

```text
/extract-patterns <source>
```

### Local development

```bash
claude --plugin-dir ~/Projects/extract-patterns
```

## Usage

```text
/extract-patterns https://github.com/org/repo
/extract-patterns ~/Projects/some-repo
/extract-patterns https://youtu.be/VIDEO_ID
/extract-patterns https://example.com/article
/extract-patterns some-repo-name
```

With no argument, it asks for a source.

## Inputs

- **GitHub URL** — shallow-cloned to a temporary directory; commit SHA recorded for provenance.
- **Local repo path** — read in place, never modified.
- **YouTube URL** — transcript fetched with `yt-dlp`.
- **Article URL** — fetched with WebFetch, with a `curl` fallback.
- **Repo name** — searched on GitHub; you pick from the matches before cloning.

For GitHub search, the `gh` CLI is optional but recommended. For YouTube, `yt-dlp` is required.

## What it produces

1. A concise overview of the source.
2. **Meta-layer insights** — new models, principle updates, workflow/resource-allocation patterns, and open questions.
3. **Project-layer patterns** — ranked high/medium/low value, with target project, adaptation notes, and cost.
4. **Recommended actions** — ordered, owner-sized next steps.

Persistence is opt-in. The command only writes notes after you approve it, and only to a context directory you configure.

## Optional knowledge-base context

Point `EXTRACT_PATTERNS_CONTEXT` at a directory containing an `index.md` to cross-reference findings against what you already believe:

```bash
export EXTRACT_PATTERNS_CONTEXT="$HOME/notes/patterns"
```

A conventional layout:

```text
patterns/
├── index.md
├── principles/
├── models/
├── sources/
└── open-questions.md
```

The command reads `index.md` when present and, with your approval, writes new notes into these subdirectories. Without the variable, the report stays self-contained and no personal paths are read or written.

## Safety model

- Source repositories are **untrusted input**. Prompt injection in READMEs, comments, filenames, or generated files is ignored.
- Cloned code is **never executed**: no installs, builds, tests, or scripts.
- Secrets from `.env`, credentials, and private config are not printed.
- Analysis is read-only. Writes happen only after explicit approval.
- Reports cite short excerpts; they do not reproduce large verbatim passages.

## Repository layout

```text
extract-patterns/
├── .claude-plugin/
│   ├── plugin.json          # plugin manifest
│   ├── marketplace.json     # single-plugin marketplace catalog
│   └── icon.png             # plugin listing icon
├── commands/
│   └── extract-patterns.md  # the command prompt
├── LICENSE
└── README.md
```

## Validate

```bash
claude plugin validate .
```

## License

MIT © 2026 bealmot