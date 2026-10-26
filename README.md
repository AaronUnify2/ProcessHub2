# Business Concierge Hub

*ProcessHub2 — a blank Process Hub for experimenting.*

FAQ answers, fact sheets, processes and variables in one editor, all built
from one set of variables, with Probe to find the questions the FAQ doesn't
answer yet.

It is Process Hub with the content taken out: the same app, started empty,
for the Business Concierge project. It began as a copy of the
[ProcessHub](https://github.com/AaronUnify2/ProcessHub) app; the two now
change separately.

Live at **https://aaronunify2.github.io/ProcessHub2/** once GitHub
Pages is switched on for this repository (Settings → Pages → Deploy from a
branch → `main`, root). Every push to `main` is live a minute or two later.

## Two repositories

| | | |
|---|---|---|
| **ProcessHub2** (this one) | public | The app, and `exports/FAQ.json` — the published FAQ content |
| **ProcessHub2-data** | private | The content: `data/processes.json`, `library.json`, `variables.json` |

The content lives in the private repository and is read through the GitHub
API with each person's own token. Without a token that can read
ProcessHub2-data there is nothing to load, and the app shows a sign-in
screen instead.

## Layout

```
  index.html         the app
  css/app.css
  js/                github.js · gate.js · storage.js · data.js · merge.js · edit.js
                     rules.js · writing.js · checks.js · export.js · factsheet-export.js · faq.js
                     richtext.js · canvas.js
                     semantic.js · semantic-worker.js · probe.js
                     ui-sidebar.js · ui-detail.js · ui-probe.js · app.js
  exports/
    FAQ.json         the published FAQ content
  vendor/            self-hosted copies of Transformers.js and the language model
                     Probe uses — see vendor/README.md
```

## Signing in

The first time on a computer, the app asks for a fine-grained personal access
token:

- Repository access: **Only select repositories → ProcessHub2-data and ProcessHub2**
- Permissions: **Contents → Read and write**, nothing else
- An expiry of 90 days is sensible

The token is kept in this browser's `localStorage` (`bchub.github.token`) on
this computer only, and only ever sent to `api.github.com`. **Sign out**
(under Export → GitHub connection) removes the token and the local copy of
the content.

This app keeps everything it stores in the browser under its own names
(`bchub…`), so it can be used alongside Process Hub on the same computer —
they are served from the same address — without either seeing the other's
token, draft or settings.

## Sections

| Section | What it holds |
|---|---|
| Variables | every fee, phone number, address and link, each with the public document it comes from; the answers and fact sheets use them, so a change is made once |
| FAQ | questions and answers, in tabs and sections, as published in `FAQ.json`; each question can have **Also asked as** phrasings and be marked an **Everyday question** for Probe |
| Fact sheets | the longer public version of what the answers say: Draft → In sign-off → Approved → Live, with a review pack for sign-off |
| Processes | step-by-step maps of how a request is handled, with the answers and fact sheets each step uses |
| Rules | writing checks (Who does this?, Long sentence, No next step), housekeeping checks, and your own "text that should no longer appear" rules |
| Issues | what people have raised about any item, and the content gaps logged from Probe; the tiles filter the list |
| Categories | the categories and subcategories everything is filed under, each with its progress; click a name to rename it |
| Probe | ask a question the way a customer would, see whether the FAQ answers it, and log it as a content gap if not |

**Categories** replace Process Hub's departments: one level of categories,
each with subcategories, created with **+ New category** and **+
Subcategory** and renamed by clicking the name. Variables, FAQ answers and
fact sheets are filed under one as their owner; a process sits under one;
each process step names the category that handles it.

## Starting from empty

Everything is added in the app:

1. **Categories** — + New category, then + Subcategory on its page.
2. **Variables** — + New variable; fill in the question, type, owner and the
   public document it comes from.
3. **FAQ** — + New tab, then + Question; write the answer with variables in
   it.
4. **Fact sheets**, **Processes** (the + beside a category in the Processes
   tree), **Rules** as needed.
5. **Publish** (Export → Publish to GitHub). Nothing is saved to GitHub until
   then; until then it is a draft in this browser.

## Publishing, and the history

**Publish to GitHub** writes the three data files to ProcessHub2-data
as one commit, then `exports/FAQ.json` here. The commit message lists every
item added, changed or removed, and the commit is recorded under the GitHub
account whose token published it — so the repositories' commit history is
the record of who changed what, and when, and any earlier version can be
restored from it.

Before publishing, the app checks nobody else has published since the draft
started; if they have, reload and use **Review and merge**, which combines
the two item by item.

With one shared token every commit carries the same name. For a proper audit
trail, give each person their own GitHub account and token.

## Testing

Serve the repository over http and drive it in a headless browser with the
GitHub API mocked — never by putting content in this repository.
