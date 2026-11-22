[![make.fun](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/rizalarkana77/site-generator-speed-01-run-20261122-entry/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/rizalarkana77/site-generator-speed-01-run-20261122-entry/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122)

# Fast makefun-site drop-in — a faster static site generator

`site-generator-speed-01-run-20261122` — "Build a static site faster, 22 November 2026".

A dependency-free ES module that answers exactly as make.fun's own
makefun-site 1.0.0 does on every case — same front matter, template dialect,
Markdown subset, slug rule and build report, byte for byte — but about
**3.5× faster on CPU time** than the reference on the practice set (~0.19 s
vs ~0.66 s for 3 cases, 200–600 sources each).

## How it works

First principles: the reference spends its time stepping one character at a
time (its inline renderer calls a regex replace per character), re-parsing
with regexes everywhere, and building each post's card twice. Most of a body
is plain text, so this implementation:

- **Renders Markdown in one pass** over the body's lines, with no token
  array: blocks are recognised and emitted directly, and inline markup is
  scanned with a native regex that jumps straight to the next special
  character.
- **Fast-paths escaping and slugs**: strings with no special characters are
  returned untouched; ASCII slugs skip Unicode normalisation; slugs are
  memoised within a case (titles, tags and headings repeat a lot).
- **Renders templates with a tight tree walk** that appends to an array and
  joins once, reuses one loop record per `for` (rendering is synchronous),
  and resolves each link target once per source.
- **Builds each page's card once** and reuses it for the post list, the
  index, tag pages and the page itself; word counts scan for whitespace
  runs instead of allocating a match array.

## Verification

- Practice set: **3/3 cases identical** to makefun-site 1.0.0 (`grade-local`).
- 42 extra cases drawn from fresh seeds with the competition's own
  generator: **42/42 identical** to the oracle.
- All 1,056 practice bodies checked block by block against the reference
  Markdown renderer: **0 differences**.

## Files

- `solve.js` — the entry: `run(input)` returns makefun-site's output tree.
- `main.ts` — the harness driver: reads `/input/cases.json`, writes
  `/output/answers.json` (V8 incremental marking off, as the baseline).
- `LICENSE` — MIT.
