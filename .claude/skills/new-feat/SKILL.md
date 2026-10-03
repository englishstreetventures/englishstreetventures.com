---
name: new-feat
description: Start a feature or fix in the English Street Ventures website repository. Clarify the observable result, inspect the Astro and Cloudflare setup, plan the files and verification, then implement on the current task branch.
---

Start work for `$ARGUMENTS`.

## Before implementation

1. Read the repository instructions and inspect the current branch and working tree. Preserve existing user changes.
2. If the task names a GitHub issue, read it and confirm its premise against the current site. Otherwise, do not invent an issue or claim one was filed.
3. Inspect `package.json`, `astro.config.mjs`, `wrangler.jsonc`, and the relevant files under `src/` before choosing an implementation.
4. Write a short plan with the user-visible outcome, files to change, and the checks that will verify it. For a small change, keep the plan in the working context rather than adding a scratch file.
5. If the scope or design is open, agree on the intended direction before building. If it is already specified, make sensible implementation choices and proceed.

## Implementation

- Keep the site static and avoid client JavaScript unless the requested interaction needs it.
- Keep framework, build output, and Wrangler asset configuration in agreement. Astro output is `dist`; `wrangler.jsonc` serves that directory.
- Keep external claims factual and verify them against a trustworthy source before publishing them.
- For a new dependency, explain why existing browser or Astro capabilities are insufficient.
- Update the README when setup, preview, or deployment instructions change.
- Do not deploy to the live custom domain unless deployment was explicitly requested.

## Finish

Summarize the user-visible changes, files touched, checks run and their results, and any deployment step that remains. Do not report an unrun check as passing.
