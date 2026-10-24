# ZetaQ Grammar Lab

Grammar practice apps for the Maharashtra State Board (e-Balbharati) English books, Classes 5 to 10.
Every lesson has 50 questions (10 per level, L1 easy to L5 hard). That makes 150 lessons and 7,500 questions.

| Class | Lessons | Questions | App |
|---|---|---|---|
| 5 | 33 | 1,650 | `class5/index.html` |
| 6 | 30 | 1,500 | `class6/index.html` |
| 7 | 25 | 1,250 | `class7/index.html` |
| 8 | 18 | 900 | `class8/index.html` |
| 9 | 20 | 1,000 | `class9/index.html` |
| 10 | 24 | 1,200 | `class10/index.html` |

## What is in this repo

```
index.html          landing page that links to each class
logo.png            used by the landing page only (each app has its logo built in)
class5/index.html   one self-contained app per class: questions, logo, code and styles are all inside the file
...
class10/index.html
data/SCHEMA.md      question format
data/c5/*.json      question banks, one file per lesson (the same questions that are built into the apps)
...
```

The apps are **static files**. They need no server code, database or build step, so serve the folders as they are.

## Hosting on the LogiQ server

1. Clone the repo once and copy the folder to the static web root, for example `https://logiq.zetaq.in/apps/grammar-lab/`:
   ```
   git clone https://github.com/Zetaq-LogiQ/zetaq-grammar-lab.git
   ```
2. To update later, run `git pull` and redeploy the changed files.
3. Open a class with its folder URL, for example `/apps/grammar-lab/class6/`.

**Serve from a `zetaq.in` address.** iPad Safari blocks or clears storage for iframes that come from another site.
If the app is on `logiq.zetaq.in` or any other `*.zetaq.in` subdomain, it counts as the same site as the LogiQ app, so progress saves normally.
Do not embed it from github.io or any other domain.

**Caching.** Each app is about 0.4 to 0.65 MB. Use `Cache-Control: no-cache` (revalidate) or add a version query (`?v=2`) after an update, so students get new versions.
Gzip or Brotli shrinks the files a lot.

**Fonts.** The apps load Baloo 2, Atkinson Hyperlegible and Caveat from Google Fonts. If Google Fonts is blocked or the device is offline, system fonts are used and the app still works.
You can self-host these fonts if you prefer.

## Saving scores in LogiQ

Progress lives in the browser's `localStorage`, under the key `zq_grammar_c<class>` (for example `zq_grammar_c6`).
That storage belongs to one device and can be cleared, so **LogiQ should store the attempts on its server.**

After every answered question the app reports the attempt in two ways:

**1. When it runs inside an iframe,** it sends a `postMessage` to the parent page:
```js
{ type: "zq-grammar-attempt", class: 6, chapter: "1.1", qid: "c6-1.1-07", score: 1, answer: "..." }
```
- `score`: `1` = right, `0` = wrong. A value in between is part marks (for example `0.5` when the student finds the wrong word but picks the wrong fix).
- `qid`: the question id. Look it up in `data/c<class>/<chapter>.json` for the question text, type, level and topic.
- `answer`: what the student chose or built.

Listener on the LogiQ page:
```js
window.addEventListener("message", (e) => {
  if (!e.origin.endsWith(".zetaq.in")) return;          // only accept our own apps
  const m = e.data;
  if (m && m.type === "zq-grammar-attempt") {
    fetch("/api/grammar/attempt", {                     // your endpoint
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ ...m, at: Date.now() })
    });
  }
});
```

**2. When it is the page itself** (no iframe), it calls `window.ZQ_onAttempt(attempt)` if that function exists, with the same object minus `type`.

## Question data

`data/SCHEMA.md` describes every question type: spot, fix, proof, fill, build, chat and change.
The JSON files are the source of truth for the questions built into each app. If LogiQ needs reports by topic or level, join attempts to these files on `qid`.

## Quality checks done

Before release, every class passed:
- an independent check of every question
- a sweep for "fix" questions that have a second valid answer
- a lint check, including duplicate questions across chapters
- answer-position balancing
- an automated browser test that answered every question both right and wrong through the UI, with 0 failures

---
© ZetaQ LogiQ. Built with Claude Code.
