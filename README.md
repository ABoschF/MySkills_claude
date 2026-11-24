# MySkills_claude

A collection of personal Claude Code skills. Each skill lives in its own folder with a `SKILL.md` file.

## Skills

### `document-code`
Generates complete documentation for a program in a directory: a `docs/` folder with a User Guide, Precautions and assumptions, and Examples (English, NumPy style, GitHub-ready), interpreting equations and units from the code. It also records open doubts in `docs/preguntas_claude.md` (in Spanish).

**How to use:** point it at a code directory and say "documenta este código" or "haz la documentación de TrajStat". To refresh existing docs, say "actualiza los docs del programa".

### `logical-consistency-audit`
Critically audits a scientific text (manuscript, section or analysis draft) and lists logical, numerical, unit and terminology inconsistencies, methods/results mismatches, and conclusions the data don't support. The report is written in Spanish to `audit/`. It does not fix style or verify citations.

**How to use:** give it the manuscript or section (`.docx`, `.tex`, `.md`, `.pdf` or pasted text) and say "busca inconsistencias en mi artículo" or "¿se sostienen mis conclusiones?".

### `ortho-gram-review`
Minimal grammar and spelling pass on manuscript text (e.g. LaTeX bullet lists and paragraphs in `main.tex`). It applies safe fixes directly and lists ambiguous changes as numbered proposals for you to confirm. It can also save review progress to `audit/ortho-gram-status.md`.

**How to use:** say "correct grammar and orthography in main.tex" or "review the bullet points", then answer its questions about the proposals. Say "save grammar review status" to store progress.

## Template

### `skill-plantilla.md`
Template for creating new skills. It gives the `SKILL.md` structure (front matter with `name` and `description`, then sections for when to use it, expected inputs, steps, output, rules and common errors, and test cases).

**How to use:** copy it to `<new-skill-name>/SKILL.md`, replace the placeholders, and make the `description` concrete, since it decides when Claude loads the skill.

## Installing the skills

Claude Code loads skills from a `skills` folder, where each skill is a directory containing a `SKILL.md`.

### For a specific project
Copy (or symlink) the skill folder into the project's `.claude/skills/` directory:

```bash
mkdir -p /path/to/project/.claude/skills
cp -r document-code /path/to/project/.claude/skills/
```

The skill is then available only when Claude Code is run in that project. Commit `.claude/skills/` to share it with collaborators.

### For all projects
Copy (or symlink) the skill folder into your personal skills directory:

```bash
mkdir -p ~/.claude/skills
cp -r document-code ~/.claude/skills/
```

The skill is then available in every project. To keep it in sync with this repository, use a symlink instead of copying:

```bash
ln -s "$(pwd)/document-code" ~/.claude/skills/document-code
```

Restart Claude Code (or start a new session) after adding a skill. Invoke it with `/<skill-name>`, or just describe the task and Claude will load it based on its `description`.
