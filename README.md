# CPVC Job Pipeline (Claude Code)

A real, working job-application pipeline built for the Cal Poly Vibe Coding Club. By the
end of this walkthrough you'll have AI teammates turning your resume or LinkedIn into a
profile, finding real job listings, checking them for ghost-listing red flags, tailoring
your resume and cover letter to each one, strictly fact-checking every claim before it
reaches you, and (optionally) pre-filling the actual application form in your browser,
stopping right before you'd click submit.

This isn't a toy. Point it at your own job search and it goes to work on real postings with
your real information. Treat every output like a first draft from a fast, tireless intern:
useful, but yours to review before anything goes out.

Using Codex instead? This is the Claude Code version. Head to the
[Codex version](https://github.com/LuukMV/cpvc-job-pipeline-codex) instead, same pipeline,
same rules, same output, run by a coordinator that spawns Codex subagents in waves.

## What you'll end up with

Seven teammates:

- **intake**: builds a first-draft profile from a pasted resume or LinkedIn, so you get
  real results in minutes instead of filling out a form first
- **finder**: searches real job boards and company career pages for roles matching your
  profile
- **validator**: re-checks each listing for ghost-listing signals (dead links, evergreen
  "always hiring" language, scraped/duplicate postings) and your actual fit
- **tailor**: drafts tailored resume bullets, a cover letter, and application-question
  answers, pulling only from facts you've actually written down
- **fact-checker**: a strict, zero-tolerance pass on every packet. Every claim either
  traces exactly to your profile or the packet is marked FAILED, not "close enough"
- **form-filler** (optional, needs browser-control tooling): opens the real application and
  fills it in, stopping before the Submit button
- **scout** (optional, run any time): point it at one part of the pipeline you want to
  improve and it researches 5 real, vetted GitHub repos that could help, with pros and
  cons, and lets you decide. It never installs or wires anything in on its own

The first five hand off to each other as they go, not in a rigid batch, so validator starts
checking a listing the moment finder reports it, and fact-checker starts the moment tailor
finishes one packet rather than waiting for the whole batch. Scout runs on its own, whenever
you ask for it.

## Prerequisites

- Claude Code Desktop, signed in
- Git
- For the form-filler step only: Claude in Chrome connected

## Step 1: get this repo

```bash
git clone https://github.com/LuukMV/cpvc-job-pipeline-claude.git
cd cpvc-job-pipeline-claude
```

## Step 2: get a profile in place (two ways)

**Fast path, see results in minutes.** Don't fill anything in by hand. When you paste the
kickoff prompt in Step 4, paste your resume text and/or your LinkedIn "About"/experience
section right above it. The `intake` teammate builds a first-draft `PROFILE.md` from it
automatically, everything it's not sure about gets flagged rather than guessed.

**Thorough path, more accurate, slower to start.**
```bash
cp PROFILE_TEMPLATE.md PROFILE.md
```
Open `PROFILE.md` and fill in your real info: contact details, what you're looking for,
education, and real experience with real numbers.

Either way, `PROFILE.md` is gitignored, it never gets committed, and every agent in this
pipeline reads this file and only this file for facts about you. Doing the fast path first
and refining `PROFILE.md` by hand afterward is the intended flow, not a compromise. Rerun
the pipeline once you've sharpened it and watch the packets get better.

Also skim `RULES.md`. It's short, and it's the whole reason this is safe to point at a real
job search: never fabricate a fact, verify every claim before it's considered done, never
submit anything for you, never check a consent box on your behalf.

## Step 3: open it in Claude Code Desktop

Open the folder as your project. Claude Code reads `.claude/settings.json` (agent teams is
already turned on) and `.claude/agents/` (the teammate definitions) automatically.

Worth actually reading `.claude/agents/fact-checker.md` before you run anything. It's a
plain markdown file with a little YAML on top, that's the entire mechanism.

## Step 4: run the kickoff prompt

Open `prompts/kickoff-prompt.md`, copy the prompt block, paste it into Claude Code (with
your resume/LinkedIn text above it if you're on the fast path).

Watch the agent panel populate as teammates spawn. Arrow keys to select one, Enter to watch
its transcript live.

### Optional: watch it on the dashboard too

Every agent writes its own small status file in `runs/status/` as it works (see
`RULES.md`), and `dashboard.html` turns those into a live status board, one card per
teammate, showing what it's working on right now and how many it's finished. In a second
terminal, from the repo root:

```bash
python3 -m http.server 8010 --bind 127.0.0.1
```

Then open `http://localhost:8010/dashboard.html` (opening the file directly won't work, it
has to be served). Leave it open in a tab while the pipeline runs.

Don't drop `--bind 127.0.0.1`. Without it the server listens on every network you're on,
and anyone on the same wifi could open your `PROFILE.md` and packets from their laptop.

## Step 5: read your packets

Once it's done, open `runs/validated.md` to see every listing's verdict and why (SKIP always
comes with a real reason, never a silent drop), then open whatever landed in
`runs/packets/`. Each packet has resume bullets, a cover letter, question answers, and a
`## Fact-check` section at the bottom, scroll straight to that first. PASS means every claim
traced to your profile. FAILED means exactly what it sounds like, don't use that packet as-is.

## Step 6 (optional): pre-fill a real application

Only do this for a packet whose fact-check says PASS and that you've actually read and want
to apply to. See `prompts/form-filler-prompt.md` for the exact steps. The form-filler opens
a brand-new browser tab for each application, fills what it can, and stops before Submit.
You click submit.

## What's actually happening here

- **Connectors**: web search/fetch for finder and validator, browser control for the
  optional form-filler step.
- **Reusable roles (skills-style)**: the agent definitions in `.claude/agents/` are plain
  markdown, you could drop them into any other project and reference them the same way.
- **Agent teams**: independent Claude sessions with their own context windows, coordinating
  through messages and files on disk (`runs/found.md` then `runs/validated.md` then
  `runs/packets/`) instead of you manually copy-pasting between them. That file-handoff
  pattern is the actual lesson here: orchestration doesn't need a special framework, just
  files with a clear contract between steps.
- **The safety rail is the point**: `RULES.md` is what makes this usable on a real job
  search instead of a demo you'd never trust. Notice the tailor agent would rather write
  `[CONFIRM: ...]` than invent a number, the fact-checker would rather fail a packet than
  wave through a plausible-sounding claim, and the form-filler would rather stop and tell
  you something looks wrong than guess.
- **Fast start without cutting corners**: `intake` is the one place in this pipeline allowed
  to infer anything (a likely target role/location from your resume), and even then it's
  only a search filter, clearly flagged, never something that ends up in a sentence sent to
  an employer. Everything downstream of that is held to the strict standard.

## Troubleshooting

- **Teammates not appearing**: make sure the task is meaty enough to warrant a team; if
  Claude thinks it's simple it may just do the work itself. Explicitly ask it to spawn the
  team if this happens.
- **Too many permission prompts**: teammate permission requests all bubble up to the lead's
  session, approve them there.
- **A teammate stalls or errors out**: select it in the agent panel, check what happened,
  either nudge it or ask the lead to spawn a replacement.
- **Form-filler can't find Claude in Chrome tools**: the extension needs to be connected in
  this conversation first, ask in `#help`.
- **A filled-out form disappeared**: almost always a tab got reused instead of opened fresh.
  `RULES.md` and `form-filler.md` both call this out, it's a real failure mode, not a
  hypothetical.
- **Dashboard says "Not connected yet"**: you opened `dashboard.html` as a file. Start the
  server from the repo root (`python3 -m http.server 8010 --bind 127.0.0.1`) and open
  `http://localhost:8010/dashboard.html`. "Address already in use" means port 8010 is
  taken, use 8011 in both places. A card stuck on "No update for N min" means that agent
  stalled, go check on it.
- **A packet keeps coming back FAILED**: that usually means `PROFILE.md` is too thin (common
  right after the fast path) for the tailor to write specific-enough bullets without
  reaching. Fill in more of the Experience/Projects tables and rerun.

## Going further

- **Pick one part of the pipeline to upgrade, then scout it.** Open `prompts/scout-prompt.md`,
  name the aspect you want to improve (better ghost-job detection, more reliable resume
  parsing, a smarter outreach step, anything), and paste the prompt in. Scout comes back
  with 5 real, actively-maintained repos, their pros and cons, and how each could plug into
  this pipeline, written to `runs/scouted/<aspect>.md`. You pick, nothing gets wired in
  automatically.
- Rerun `finder` with a different target role in `PROFILE.md` to see it build a fresh batch.
- Package this as a plugin so the rest of the club can install it in one step instead of
  cloning a repo.
