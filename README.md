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
  map.html           experimental, staff only: the published FAQ laid out by meaning
  css/app.css · css/gate.css · css/meaning-map.css
  js/                github.js · gate.js · storage.js · data.js · merge.js · edit.js
                     rules.js · writing.js · checks.js · export.js · factsheet-export.js · faq.js
                     richtext.js · canvas.js
                     semantic.js · semantic-worker.js · probe.js · meaning-map.js · probe-map.js
                     spot-hint.js
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

**`map.html`** sits behind the same sign-in. It starts hidden and runs only
once GitHub confirms the token kept in the browser can still read
ProcessHub2-data; anyone signed in to the app on that computer goes straight
in. It asks search engines not to index it. What it shows is the public FAQ,
so the sign-in keeps the tool to staff — it cannot hide the file itself,
which GitHub Pages serves to anyone who asks for it by name.
`exports/FAQ.json` stays public.

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

## Probe's meaning map

Between the search box and the closest answers, Probe shows the whole FAQ
laid out by meaning (see *The meaning map* below), drawn from the draft —
unpublished questions included, as hollow rings:

- A probed question is a **★**, placed among the answers Probe ranked
  highest, with lines to them (heavier for a higher score). The caption reads
  the picture: the closest answers sitting together means the model knows
  what the question is about (and if none scores well, that part of the FAQ
  has a gap); answers spread across different parts of the map means it
  can't tell — that is when a search by meaning picks the wrong answer, and
  an *Also asked as* phrasing on the right answer is the fix.
- On the dashboard, open content gaps are **◇ diamonds**, each placed among
  the answers it comes closest to; a patch of them is a topic the FAQ is thin
  on. Tap one to probe it again.
- Tap a dot for its question, status and meaning group, with a link to it.

The map waits until every answer has been read, lays itself out once, and
again only when an FAQ question or answer changes. It needs at least five
FAQ questions.

## The meaning map (experimental)

`map.html` lays the published FAQ out on one page by meaning: every answer is
a dot, and answers the model reads as being about the same thing sit close
together, whatever tab they are on. It is behind the sign-in (see *Signing
in*), then reads `exports/FAQ.json` and the model in `vendor/`:
`https://aaronunify2.github.io/ProcessHub2/map.html`. The same map, drawn
from the draft, is in the Probe section. Both use `js/meaning-map.js`.

Process Hub's map takes ready-made fingerprints from its `ask.html`; this app
has no `ask.html`, so `map.html` works them out with `js/semantic.js` the
first time and keeps them in the same browser store as Probe
(IndexedDB `bchub-vectors`). Anyone who has used Probe on that computer has
most of them already; after that it opens in a few seconds.

- **Read: Whole answers** averages each question with every passage of its
  answer; **Questions only** uses the question as Probe reads it.
- The layout is t-SNE, worked out in the browser and seeded, so the same
  content gives the same map; **Re-run layout** draws it from another start.
  Nearness is meaningful, distance across the page is not.
- **Colour by: Meaning group** shows the model's own grouping (k-means on the
  fingerprints, ignoring tabs), named after each group's most distinctive words.
- The side panel lists **possible overlaps** (pairs read as near-identical)
  and **answers far from their tab** (most of their nearest answers are on
  other tabs) — candidates for merging or cross-linking.
- Typing a question loads the model and drops a star where it lands, with
  lines to the closest answers.
- **Tap empty space** to ask *what would go here?* — an experiment in
  wording. The model can only read, not write, so `js/spot-hint.js` makes
  the suggestions in steps: the spot's fingerprint is estimated by blending
  the answers around it, nearest counting most; candidate questions are
  written from the phrases those answers use most and the rest of the FAQ
  uses least, set into everyday question shapes ("Who do I contact about
  …?"); and the model keeps the three closest to the spot. The wording is
  stiff — a glimpse of what the model thinks belongs there, not questions
  to use. Tap one to place it on the map.

`js/meaning-map.js`, `js/probe-map.js`, `js/spot-hint.js`,
`css/meaning-map.css` and `css/gate.css` are kept the same as Process Hub's
(apart from names in comments and captions), so changes can be carried
between the two.

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
