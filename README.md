# CPVC Agent Orchestration Workshop (Claude Code)

A hands-on multi-agent orchestration project built for the Cal Poly Vibe Coding Club: a
team of specialized Claude Code teammates, coordinating through file handoffs and running
in parallel, with a fact-checker gate that can fail their output. The applied example is a
real job-application pipeline: teammates turn your resume into a profile, find real job
listings, check them for ghost postings, tailor a resume and cover letter to each one,
fact-check every claim, and (optionally) pre-fill the real application form, stopping
before Submit.

Using Codex? Use the [Codex version](https://github.com/calpolyvibecoding-01/cpvc-agent-orchestration-codex) instead.

## Teammates

- **intake**: builds `PROFILE.md` from a pasted resume/LinkedIn
- **finder**: searches job boards and company sites for matching listings
- **validator**: flags ghost listings, checks fit
- **tailor**: writes resume bullets, cover letter, question answers from `PROFILE.md` only
- **fact-checker**: fails any claim that doesn't trace to `PROFILE.md` or the posting
- **form-filler** (optional, needs Claude in Chrome): fills the real form, stops before Submit
- **scout** (optional): researches repos that could improve one part of the pipeline

## Prerequisites

- Claude Code Desktop, signed in
- Git
- Node.js 18+
- Optional: LibreOffice (for an exact one-page check)
- Form-filler only: Claude in Chrome connected

## Step 1: set up a folder, from the chat

1. Make an empty folder (e.g. `job-pipeline`) and open it in the Claude app's Code tab.
2. Paste this into the chat:

   ```
   Set this folder up for the CPVC job pipeline. Run these here, in this empty folder:
   1. git clone https://github.com/calpolyvibecoding-01/cpvc-agent-orchestration-claude.git .
   2. npm install --prefix resume-builder
   If git says the folder isn't empty and the only thing in it is a hidden .DS_Store file,
   delete just that file and try again. If anything else is in the folder, stop and ask me.
   If git or Node.js isn't installed, stop and tell me how to install it on my computer.
   If step 2 says npm isn't recognized even though Node.js is installed, you're on Windows
   and npm needs its .cmd extension in this shell: retry with
   "npm.cmd install --prefix resume-builder" instead.
   When both steps work, tell me it's ready.
   ```

3. **Start a new chat in the same folder.** Claude Code only loads `.claude/agents/` and
   agent teams when a chat starts, and the setup chat started before those files existed.
   Set that chat's model to **Sonnet** (`/model sonnet`); the lead only coordinates.

## Step 2: get a profile in place

**Fast path:** paste your resume/LinkedIn text above the kickoff prompt in Step 3. `intake`
builds a first-draft `PROFILE.md` for you. Add two lines, with whatever is true for you:

```
Work authorization: U.S. citizen, no sponsorship needed
Currently enrolled: yes
```

**Thorough path:** ask your AI to copy `PROFILE_TEMPLATE.md` to `PROFILE.md`, then fill it
in yourself.

`PROFILE.md` is gitignored and never committed. Every agent reads only this file for facts
about you. Skim `RULES.md` too, it's short.

## Step 3: run the kickoff prompt

Send one message: your resume/LinkedIn text and the two extra lines (fast path only), then
the prompt block from `prompts/kickoff-prompt.md`. The lead will ask how many listings to
look for before spawning finder, answer with a small number (5-10) for a faster run. Watch
progress in the chat and the agent panel.

## Step 4: read your packets

```
runs/
  APPLICATIONS.md        start here: one row per job
  validated.md           GO/SKIP for every listing, with the reason
  packets/<company>-<role>/
    packet.md             bullets, cover letter, answers, fact-check verdict
    resume.docx            one-page tailored resume
```

Open `runs/APPLICATIONS.md` first. Only use a packet whose fact-check says PASS; answer its
open questions before you apply.

## Step 5 (optional): pre-fill a real application

Only for a packet that passed fact-check and that you've read and want to apply to. See
`prompts/form-filler-prompt.md`. Opens a fresh tab, fills what it can, stops before Submit.

## Troubleshooting

- **No teammates spawn:** ask Claude explicitly to spawn the team.
- **Too many permission prompts:** they all bubble up to the lead, approve there.
- **Form-filler can't find Claude in Chrome:** connect the extension first.
- **Packet keeps failing:** `PROFILE.md` is too thin, fill in more and rerun.
- **"npm isn't recognized" on Windows, even with Node.js installed:** some Windows shells
  only resolve `npm.cmd`, not plain `npm`. Open PowerShell, `cd` into the folder, and run
  `npm.cmd install --prefix resume-builder` directly.

## Going further

- `prompts/scout-prompt.md`: point scout at one part of the pipeline you want to improve.
- Rerun `finder` with a different target role in `PROFILE.md`.
