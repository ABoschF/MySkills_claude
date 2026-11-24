---
name: ortho-gram-review
description: Correct only grammar and spelling in text of a scientific manuscript (LaTeX bullet lists, itemize/enumerate, paragraphs), changing the logic and wording as little as possible, and ask before any ambiguous change. Use when I say "correct grammar and orthography", "review the bullet points", "fix typos in main.tex", or ask for a minimal language pass on draft text. Also saves the review status to audit/ortho-gram-status.md when I say things like "save grammar status to this point", "save orthographic review status", "save the grammar review status", "guarda el estado de la revisión gramatical/ortográfica hasta aquí" (English or Spanish).
---

# ortho-gram-review

## When to use it
- I ask for a grammar/spelling pass on draft text in the manuscript (usually `main.tex`, inside `\begin{enumerate}`/`\begin{itemize}` blocks, but also paragraphs).
- "Correct only grammar and orthography", "do the least changes", "ask if ambiguous".
- Do NOT use it to rewrite, shorten, restructure, improve style, or check scientific content. If I want that, I will say so.

## Expected inputs
- The text, either pasted in chat or located in a file (default: `main.tex` of this project).
- If I do not say which part and no text is pasted, ask ONE question: which section/line range (or to paste the text). Do not guess.

## Steps
1. Locate the text: read the relevant range of the file (all `enumerate`/`itemize` blocks if I say "the bullet points"; use `grep -n` on `\begin{enumerate|itemize}` to find them).
2. Apply the **safe** corrections directly in the file:
   - typos and misspellings (including Spanish leftovers such as "grupo", "mayor groove" → "major groove");
   - subject–verb agreement, articles, prepositions, missing words ("that", "of", "the");
   - plural/singular, "than" vs "that", "it's"/"its", "an" vs "a";
   - punctuation: missing spaces after periods/colons, double spaces, repeated words, closing periods in bullets;
   - units/numbers spacing only when clearly wrong ("40p" → "40pN" when the unit is evident);
   - capitalization consistency within the same list ("Inner Binding" → "Inner binding").
   Use a short Python script with exact-match replacements that asserts each match is unique (`python3 -I`), or Edit. Report any replacement that did not match.
3. **Do NOT apply** (list them for me as numbered proposals instead) anything that could change the meaning or the logic:
   - sentence fragments or sentences whose subject/verb is missing and must be guessed;
   - ambiguous numbers or symbols (e.g. "0,10mM"), unclear references ("this increment", "around the them");
   - wrong-looking figure references, scientific claims that look incorrect, empty `\item`s;
   - calques and idioms that would need rewording ("In the same order of ideas");
   - spelling variant (British vs American): ask once which one the target journal uses, then standardize.
4. For each proposal give: the line, the original text, my proposed fix, and the reason for the doubt. Ask me to confirm; apply only what I confirm. If I answer a question, apply exactly that answer.
5. Recheck: `git diff -U0 main.tex` and scan the added lines for leftover typos (check pairs like "1rst", "behaviour"/"behavior"). Fix leftovers.

6. **Save status (on request).** When I say "save grammar status to this point", "save orthographic review status", "save the grammar/ortho review status", "guarda el estado de la revisión gramatical", "guarda el estado ortográfico hasta aquí", "guarda el progreso de la revisión" or anything similar that explicitly mentions the grammar/spelling/orthographic review (English or Spanish). A generic "save status" with no mention of grammar/orthography does NOT trigger this; ask which status is meant, write/update `audit/ortho-gram-status.md` (create the `audit/` directory if it does not exist). Overwrite the file with the current state, in English, with:
   - date and file(s) under review;
   - **Done**: sections/line ranges already reviewed and the types of corrections applied;
   - **Pending proposals**: numbered list (line, original, proposed fix, reason), marking each as `open`, `confirmed` or `rejected`, with my answers recorded;
   - **Decisions**: standing choices (e.g. American spelling, `\AB` notes reviewed as independent sentences, captions skipped);
   - **Not reviewed yet**: remaining sections.
   If the file already exists, read it first and update it, keeping earlier decisions. At the start of a later review, if `audit/ortho-gram-status.md` exists, read it and continue from there.

## Output
- Edits made in place in the source file; no extra files.
- A short final message with: (a) what was corrected, grouped by type, (b) numbered list of proposals/doubts not applied, (c) things noticed but outside scope.
- Say clearly that the document was not recompiled (unless it was).

## Rules and common mistakes
- Never touch LaTeX commands, labels, `\ref`/`\cite` keys, math, or comments (`%`).
- `\AB{...}` notes ARE reviewed, but as independent sentences: the correction must stand on its own, and the text around the note must stay grammatical and logically consistent if the note is removed (so never move words between a note and the surrounding text, and check that deleting the note leaves a correct sentence; if it does not, propose a fix). Same grammar/spelling rules and same "ask if ambiguous" rule as for the rest. Keep the `\AB{...}` wrapper.
- `\rev{...}` text is reviewed like normal text. Leave "Details on…" placeholders and figure captions alone unless I ask; only mention typos found there.
- Do not "improve" style, change terminology (micro/nano Mg, mMg/nMg, Hrise), or merge/split bullets. A bullet that is a draft idea stays as it is, even if fragmentary.
- Do not change numbers, forces, concentrations or citations.
- Do not infer pronouns or authorship; keep the author's voice (first person, "we", "I believe").
- Keep the author's meaning even when the science looks doubtful: flag it, do not fix it.
- When I give a rewritten sentence, use it verbatim apart from the corrections agreed.
- Do not commit unless I ask.
- `main_v1.tex` (and any other `main_vN.tex`) is a text-only archive: never compile it and never leave any output of it (PDF, aux, log, copies) in the project directory. Work on a copy in the scratchpad if testing, then write only the `.tex` back, and only when I ask.
- The review can be applied to a different file than `main.tex` if I name it; do not touch other files.

## Test cases
File prefix: `ortho-gram-review__`

| Case | Input | Expected result | Status |
|------|-------|-----------------|--------|
| 01 | `main_v1.tex` (CFREX manuscript draft; work on a copy) | Typos fixed ("stretchs", "anzatz", "mayor"), agreement fixed ("CG lists"), ~18 ambiguous items proposed, not applied | done |
| 02 | A bullet with a fragment and an ambiguous number (e.g. "0,10mM") | Left unchanged, listed as a numbered proposal | pending |
| 02b | `main_v1.tex` (tested, scratchpad copy; all proposals applied after confirmation) | 76+ lines corrected, ~20 proposals confirmed and applied, no compiled outputs left | done |
| 03 | A block containing `\AB{...}` notes and `%` comments | `\AB` text corrected as independent sentences; surrounding text still correct if the note is removed; `%` comments untouched | pending |

## Development notes
- Version: 0.3
- Created from the workflow used on `main.tex` (Nov 2026): safe fixes applied directly, ambiguous ones proposed and applied after confirmation.
- v0.3: status saving (`audit/ortho-gram-status.md`) and `main_vN.tex` no-output rule.
- v0.2: `\AB` notes are now reviewed as independent sentences.
- Pending: decide whether captions should be included by default; decide default spelling variant (American was used for this manuscript).
