# Handoff — GitHub profile README rewrite

Continued from a session in `/Users/mac/Documents/Code/jabb/jb` on 2026-08-15.
Nothing has been pushed. `README.md` is still the old live version.

## Goal

Rewrite `BrightProgrammer7/BrightProgrammer7/README.md` so it gives an overview of
**Abdelilah Akhmim the engineer** — not a JABB advert. Trigger was an Instagram post
by @gitskins: "Your GitHub profile should tell your story."

## Files here

| File | What |
|---|---|
| `README.md` | Live version, last pushed 2025-01-14. Do not treat as good. |
| `README.draft.md` | Proposed replacement. Awaiting approval. |
| `.gitskins-slide1.jpg` | The IG slide that started this. Reference only. |

## Verified state of the live README (checked at the rendered profile, not the source)

- Last commit **2025-01-14** — 19 months stale.
- **All three stat widgets are broken right now.** `camo.githubusercontent.com` returns
  404 on five image hashes on the live profile page.
  - `github-readme-stats.vercel.app` → 503 on 3 consecutive retries (shared instance
    is rate-limited). Used twice, lines 51 and 53.
  - `github-readme-streak-stats.herokuapp.com` → line 55.
  - ~~`streak-stats.demolab.com` is **also dead**~~ — **this was wrong.** Re-checked
    2026-08-16: it returns 200 `image/svg+xml` with real data (4,817 total contributions,
    current streak 3, longest 31). It is in the draft now. The heroku host also answers
    200 today. Do not trust the earlier "dead" call.
- Lines 16, 18, 24 use bare domains with no `https://`. GitHub renders those as
  relative links → 404.
- Ko-fi URL contains a literal space: `https://ko-fi.com/ BrightKofi`.
- `<!-- BLOG-POST-LIST:START -->` markers never fill — repo has no
  `.github/workflows/` at all.
- 47-icon tech wall is verbatim rahuldkjain/github-profile-readme-generator output.
- Bio reads "Computer Science & AI Programming enthusiast".

## Widget audit — every endpoint checked 2026-08-16

The first pass cut all visuals. That went too far: it treated "some widgets are dead" as
"widgets are dead". Each candidate was actually requested and checked. Status codes below
are real responses, not assumptions — several of these return 200 with an *error card*,
so size and content-type are recorded too.

**Live — now used in the draft:**

| Endpoint | Result |
|---|---|
| `streak-stats.demolab.com` | 200 svg, real data (4,817 / 3 / 31) |
| `github-profile-summary-cards.vercel.app` — `repos-per-language`, `most-commit-language`, `productive-time`, `stats`, `profile-details` | 200 svg, all five |
| `github-readme-activity-graph.vercel.app` | 200 svg |
| `komarev.com/ghpvc/` | 200 svg |
| `skillicons.dev/icons` | 200 svg |
| `img.shields.io/badge/` | 200 svg |

**Dead — do not put these back:**

| Endpoint | Result |
|---|---|
| `github-readme-stats.vercel.app/api` | **503**, 99 bytes text |
| `github-readme-stats.vercel.app/api/top-langs` | **503**, 99 bytes text |
| `github-profile-trophy.vercel.app` | **402 Payment Required** — the trophy/leaderboard wall is gone |

`github-profile-summary-cards` is the working replacement for the 503'd
`github-readme-stats` cards, and it is what the draft uses.

**Available, deliberately not used:** `capsule-render` (200 — generated wave banner,
reads as template), `readme-typing-svg` (200 — animated typing line, same), the GitSkins
API (200 — third-party product, watermarked on the free tier).

**Self-hosted, the only failure-proof option:** `Platane/snk` or the GitSkins Space
Shooter pattern — a GitHub Action renders the asset daily and commits it to an `output`
branch, so the image is served from this repo. Still not adopted; it is the upgrade path
if any hosted widget above dies.

## GitSkins sections — tested against the real username 2026-08-16

Earlier this file argued against them on dependency and watermark grounds, taken from the
pricing page. That was an assumption. All eleven `api/section/*` endpoints were then
called with `username=BrightProgrammer7&theme=aurora&mode=dark`: every one returns 200
`image/svg+xml`. The rendering works. The problem is what it renders.

**Every section carries a `gitskins.com` watermark.** Confirmed in the SVG text of all
eleven. Putting one on the profile advertises their product on his page.

**Two sections publish things that are not true:**

- `projects` — renders **invented repositories**: "awesome-project — A standout
  open-source project", "toolkit — Reusable building blocks and utilities", "2 pinned",
  "updated just now", "updated n/a", 0 stars. None of these exist. He has no pinned
  repos, which is why the placeholders appear. This would put fabricated repository names
  on his profile.
- `stack` — "HTML 95%, Python 4%". It is byte-weighted, so vendored assets dominate. The
  real distribution is JavaScript 31, Python 15, Jupyter 9, Java 9, TypeScript 6. An AI
  engineer's profile announcing HTML 95% misrepresents him.

**Sections that are accurate:** `stats` (4 stars, 3,163 contributions, 114 repos, 49
followers) and `highlights` (114 public repositories, 4 stars earned, 49 followers). Both
true, neither flattering — "4 stars earned" is not a line to lead with.

**Sections that are pure decoration** and invent nothing: `wordmark` (his name as 3D
ASCII), `portrait`, `heatmap`, `system-scan`, `chess`. These are usable if the watermark
is acceptable.

`hero` and `about` are decoration that pulls his **GitHub bio**, so they currently render
the stale "Just Code 👨‍💻 & You'll be Good 😄 by @BrightCoder" — including the retired
`@BrightCoder` handle. Fix the bio first or skip them.

Note: `stats` reports 3,163 contributions where `streak-stats` reports 4,817 — different
windows, not a contradiction.

## Separate fix — the GitHub bio field

`api.github.com/users/BrightProgrammer7` still returns
`bio: "Just Code 👨‍💻 & You'll be Good 😄 by @BrightCoder"`. That renders in the sidebar
*above* the README on the profile page, so rewriting the README alone leaves the old
voice in place. `twitter_username` is already `AGIWithAbdel` and `blog` already points at
the Netlify portfolio, so the bio is the only stale field.

## GitSkins sections that ARE now in the README

Abdelilah's call 2026-08-16: the `gitskins.com` watermark is acceptable. Four sections
are in, chosen so that none of them can state something false:

| Section | Why it is safe |
|---|---|
| `wordmark` | His name as 3D ASCII. Renders from the GitHub display name; the `label=` param is ignored (byte-identical output with and without it), and the name is already correct. |
| `highlights` | Driven entirely by our own `items=` string, so the text is ours: AI agents, 8 first places, GreenBee #4 on F6S. |
| `system-scan` | Decorative ASCII panel — subject, handle, status. |
| `heatmap` (`style=jet`) | Real contribution data, 3,163 in the last year. |

Still excluded, and the reason is accuracy rather than taste: `projects` (invents
repositories), `stack` (HTML 95%), `hero` and `about` (both read the stale GitHub bio;
passing `bio=` suppresses it but hero still ranks HTML first), `stats`/`portrait`
(redundant against the summary cards and the avatar).

## Images in the repo

None. `portrait.jpeg` was committed and then removed on Abdelilah's call — the README
uses `github.com/BrightProgrammer7.png?size=240`, his actual GitHub avatar. That is
strictly better: it needs no file in the repo, never 404s before a push, and tracks
whatever avatar he sets. The repo carries no image assets now.

## Where the draft's facts came from

All of it is lifted from the live portfolio at https://abdelilah-akhmim.netlify.app
(scraped 2026-08-15) — so every claim is Abdelilah's own wording, not invented.
Repo data corroborates the stack: 114 own repos + 21 forks; JavaScript 31, Python 15,
Jupyter 9, Java 9, TypeScript 6, HTML 6, Dart 2, HCL 2, Go 2.
Account: 135 public repos, 49 followers, created 2021-01-13. No pinned repos. No orgs.

## Open questions — resolved 2026-08-15 against the portfolio source

Source of truth is `portfolio/web-portfolio/src/site.config.ts` (`SOCIALS`), not the
rendered page — the site does publish the email, the earlier "portfolio hides email"
note was wrong.

1. **Email** — `akhmim.abdelilah@gmail.com`. Applied. `bright.procoder@gmail.com` was
   the stale one from the 2025 README.
2. **Instagram `abd_elilah_1`** — in `SOCIALS`, verified 200. Added to the draft.
3. **X `@AGIWithAbdel`** — in `SOCIALS`, verified 200. Confirmed, no change.

## Fixed in the draft after the portfolio cross-check

- **Agenix-Eye was missing.** It is `featured: true` on the portfolio and was not in the
  handoff's omitted list — a silent drop, not a decision. Added to Selected work.
  (Project count was also wrong here: 14 project files, not 12.)
- **Project names now link** to their live demos. All eight verified 200 on 2026-08-15:
  agenix-bot.surge.sh, hassna.vercel.app, llm-guardian.surge.sh, green-bee.onrender.com,
  alhakikanews.vercel.app, monuquest.surge.sh, iallo.surge.sh, niaama.surge.sh.
- `github-readme-activity-graph` re-verified 200.

## What the old README had that the draft had dropped

The first pass took this file's cut list on trust and never audited `README.md` itself.
Audited 2026-08-15. Correctly cut: the 47-icon wall, Discord, Buy-Me-a-Coffee, Ko-fi,
"fun fact", the "passionate enthusiast" headline, the dead stats/streak widgets.
Wrongly cut, now restored:

- **The call to action.** The old README had "looking to collaborate on AI Hackathon",
  "looking for help with RAG & AI Agents", "ask me about ...". The draft had no
  invitation at all — a profile that only recites. Added an "Open to" section.
- **Writing.** Old README had a Blogs section (markers never filled) plus Medium. The
  draft kept a bare Medium link and ignored the portfolio's own `/writing` route.
  Added.

Still cut, deliberately: `komarev` profile-view counter (verified live, 200 — it is the
one old widget that still works, so this is a taste call, not a technical one) and the
`top-langs` widget, whose content the "Working with" prose already carries.

## demo-gitskins (github.com/asamassekou10/demo-gitskins) — what is worth taking

Checked 2026-08-15. Thirteen embeds, twelve of them `gitskins.com/api/section/*` SVGs —
their own API, live now (200, `image/svg+xml`), and the single point of failure already
described above. Three techniques in it are dependency-free and worth having:

1. **`<picture>` + `prefers-color-scheme`** for light/dark image variants. Pure GitHub
   markdown. **This fixed a real bug in our draft:** `theme=github-compact` draws
   `stroke="#E4E2E2"` on a transparent background — near-invisible on GitHub's light
   theme. Draft now serves `github-light` (white ground) to light readers and keeps
   `github-compact` for dark.
2. **`<details>`/`<summary>`** collapsible blocks — native, zero deps. An option if the
   "Before" or stack list ever gets long enough to need folding.
3. **The Space Shooter pattern.** The only self-hosted asset in the whole demo: a GitHub
   Action renders it daily and commits the GIF to an `output` branch, and the README
   points at `raw.githubusercontent.com/<user>/<repo>/output/space-shooter.gif`. Same
   shape as `Platane/snk`. That is the honest way to get a moving contribution graph —
   the asset lives in your own repo, so no third party can 404 it. Not adopted; noted
   as the one available upgrade that does not repeat the widget failure.

## asamassekou10/asamassekou10 — the GitSkins author's own profile

Checked 2026-08-15, more useful than the demo repo because it is a real profile rather
than a feature tour. It mostly *validates* the draft's shape: prose bio, no icon wall,
**Selected work as a linked markdown table** — the same call we made — plus "Current
focus" where we have "Now". Even the author, selling the widget product, writes the
substance in plain markdown and uses his API only for decoration.

**The draft now uses his markup skeleton directly**, not just ideas taken from it:
`<p align="center">` blocks around the portrait, link rows, badges and every image; an
`<h1 align="center">` name; `&nbsp;·&nbsp;` as the separator in link rows; `##` headings
so GitHub draws its rule between sections; a centered CTA pair after Selected work
(`See the full portfolio · Visit jabb.cx`, where his reads `Explore ship-safe · Visit
shipsafecli.com`); `<details><summary>` from the demo repo folding "Before JABB" into a
table; `<picture>` + `prefers-color-scheme` on the activity graph; and his closing
centered `<sub>` line. What is swapped out is the payload — the eleven watermarked
`gitskins.com/api/section/*` embeds are replaced by the verified-live widgets listed in
the audit above, because two of his sections fabricate data on this account.

Two differences worth naming:

- **He links a top nav row** (`ship-safe · GitSkins · Projects`) directly under the hero,
  and repeats a CTA mid-document. Our draft buried every destination in "Find me" at the
  bottom. Added a three-link row under the headline: Portfolio, LinkedIn, and an anchor
  to Selected work.
- **His table links to his own public repos**; ours links to hosted demos, because the
  repos behind them are private or thin. That is the same gap this file already names at
  the bottom — it is the one thing a README rewrite cannot fix.

Not taken: his four-row table is tighter than our eight. Worth considering a trim to five
or six, but that is a taste call for Abdelilah, and every row we have is real work.

## Claims in the draft the portfolio does NOT source

Not wrong, just unverified — confirm before pushing:

- JABB stack line "Flutter, Flask, MongoDB, GCP, Gemini". No portfolio content file
  states a JABB stack.
- "Working with": Next.js, MCP, Postgres appear nowhere in the portfolio's content or tags.
- Medium `@abdelilahk627` exists (profile name "BrightWriter") but carries 2 posts, the
  last from Aug 2024, and is absent from the portfolio's `SOCIALS`. Thin link.

## GitSkins — what it actually is (gitskins.com, checked 2026-08-15)

The IG account is a product, not a creator. @gitskins, 2,460 followers, 23 posts, bio
link gitskins.com. Post caption: "Make your GitHub profile worth exploring. GitSkins
helps you turn your README into a polished developer showcase."

Web app (plus a Chrome extension) that generates profile READMEs: README Studio and
Profile Studio, prompt-driven, with themes (Renaissance, Aurora, Matrix, Studio, Dracula,
Cyber), AI README agent, avatar/persona generator, hosted profile skins, GitHub Wrapped.
Free tier watermarks; Pro $4.99 first month then $9/mo, $79/yr, or lifetime one-time.
Repo: github.com/asamassekou10/GitSkins.

**Why this matters for our draft:** GitSkins renders README sections as *images* served
from `gitskins.com/api/section/...`. That is the same third-party-image dependency that
already broke all three widgets on the live profile. A GitSkins-built README stays alive
only as long as their API does, and the free tier stamps a watermark on it. The slide's
premise ("your profile should tell your story") is the useful part; the tool is not.

## Decisions already made

- Cut the 47-icon wall. Icon walls read as "generated"; prose reads as engineer.
- Cut every dead widget, buy-me-a-coffee, ko-fi, Discord, profile-view counter, and the
  empty blog markers.
- Kept 7 of 12 portfolio projects. Omitted: DarnaKitchen, SalamtyFirst, SurfInGPT,
  JobLink, Portfolio v1. Twelve reads as a list; seven reads as a selection.
- JABB demoted to two lines under "Now".
- No client names. Intelcia and RAM×ONDA are unannounced pilots — keep them off.
- **No contribution-graph pixel art.** It is backdated empty commits. Reads as padding
  to anyone who knows the trick, and contradicts the draft's honest "private repos"
  line. Do not add it without Abdelilah explicitly asking.

## Also worth doing, separately

Zero repos are pinned. Public repos are thin — the best is `cmd-toolkit` at 1 star.
Pinning weak repos is worse than pinning none. The real fix is making one substantial
thing public.

## To finish

1. Get answers to the three open questions, patch `README.draft.md`.
2. ~~`mv README.draft.md README.md`~~ — done. The rewrite **is** `README.md` on
   `feat/profile-readme-rewrite`; there is no draft file any more. Still to delete before
   merge: `HANDOFF.md` and `.gitskins-slide1.jpg`.
3. Commit on a branch, PR into `main`. Do not push to `main` directly without asking.
4. Verify at the terminal surface: load https://github.com/BrightProgrammer7 and confirm
   the camo URLs return 200, not 404. A green `git push` proves nothing about rendering.
