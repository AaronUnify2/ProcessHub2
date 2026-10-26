# Business Concierge Hub

FAQ answers, fact sheets, processes and variables in one editor, with Probe
to find content gaps. Process Hub, started empty, for the Business Concierge
project. A multi-file static web app: plain HTML, CSS and JavaScript, no
build step, no framework, no `package.json`, no dependency install.

Read `README.md` for how the app works. The content lives in the **private**
ProcessHub2-data repository.

## Public repository — keep it that way

This repository is public and served by GitHub Pages. **Never add content to
it**: no data files, no copies of fact sheets, processes or issues, no test
fixtures made from real content. The only content file here is
`exports/FAQ.json`, written by the app's Publish button.

## How to build things here

**Plain, readable JavaScript.** ES5-style modules on `window` (`Data`,
`Edit`, `Storage`…), one concern per file under `js/`. It should still make
sense in six months.

**No external dependencies unless asked.** The one exception is `vendor/`:
unmodified copies of Transformers.js and the all-MiniLM-L6-v2 model for
Probe, self-hosted so the app depends on no one else's servers. Keep them as
they are; to change them, follow `vendor/README.md`.

**Always produce complete files, not diffs or fragments.** Aaron prefers whole
files.

**Works on a phone as well as a desktop.**

**Test it before calling it done.** Serve the repository over http and drive
it in a headless browser, with `https://api.github.com/**` routed to a mock
that serves the data files. Check edge cases and computed results. Say plainly
if something doesn't work.

## Words

The app files things under **categories and subcategories**. In the code and
the data they are still the taxonomy (`taxonomy`, `taxonomyId`, `ownerId`,
and `departmentId` on a process step), shared with Process Hub; only what
people see says "category". Keep it that way, so changes can be carried
between the two apps.

## Browser storage

The app shares an address with Process Hub (`aaronunify2.github.io`), so
everything it keeps in the browser has its own name: the token
`bchub.github.token`, preferences `bchub.prefs.v1`, the draft in IndexedDB
`bchub`, Probe's fingerprints in `bchub-vectors`. Never reuse a `processhub`
name. The token must never be written anywhere else — not the draft, not an
export, not a log. Never ask Aaron for the token or put one in the
repository.

## Workflow

- Develop on the assigned working branch and commit with a clear message.
- Merge into `main` when the work is done. **No pull request is needed**
  unless Aaron explicitly asks for one.
- After merging, send him the live Pages URL so he can test it.
