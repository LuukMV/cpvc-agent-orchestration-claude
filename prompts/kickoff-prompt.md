# Kickoff prompt

Get `PROFILE.md` in place first, pick one:

- **Fast path:** paste your resume/LinkedIn text above the prompt block below. The `intake`
  teammate builds a first-draft `PROFILE.md` from it.
- **Thorough path:** ask your AI to copy `PROFILE_TEMPLATE.md` to `PROFILE.md` and fill it
  in by hand first.

Either way, add these two lines (write whatever is true for you):

```
Work authorization: U.S. citizen, no sponsorship needed
Currently enrolled: yes
```

Copy the block below into Claude Code as-is, pasting your resume/LinkedIn text above it on
the fast path.

---

We're running a job-hunting pipeline using an agent team. Read `RULES.md` first, it applies
to every teammate below.

If `PROFILE.md` doesn't exist and I've pasted resume/LinkedIn text above this prompt, spawn
**intake** first to build a draft `PROFILE.md`. Don't spawn anyone else until intake
reports back finished. If `PROFILE.md` already exists, skip to the next part.

Each agent file in `.claude/agents/` already names the smallest model that fits its job.
Spawn each teammate with its agent type and don't name a model, never run a teammate on
Opus. Don't do a teammate's work yourself.

Track the run in your task list: one task per stage (profile, find, validate, tailor,
fact-check), then one task per GO listing named `<Company>: <Job title>`, completed with
PASS or FAILED once its packet has a fact-check verdict.

Then spawn four teammates:

- **finder**: search job boards and company sites for roles matching my profile, write to
  `runs/found.md`
- **validator**: check each candidate for ghost-listing signals and fit, write verdicts to
  `runs/validated.md`
- **tailor**: for every GO verdict, draft a tailored resume, cover letter, and
  question answers to `runs/packets/<company>-<role>/packet.md`
- **fact-checker**: the moment tailor finishes a packet, verify every claim traces to
  `PROFILE.md` or the posting, append a PASS/FAILED section

If fact-checker marks a packet FAILED, have tailor revise only the flagged lines, then
fact-checker rechecks. One revision round per packet; if it fails twice, leave it FAILED.

Let handoffs overlap, don't wait for full batches between stages. When done, run
`node scripts/applications.js`, then summarize: how many found, how many GO, how many
passed fact-check outright, and name any that failed.

---

### Going further

Rerun with a different `PROFILE.md` to build a fresh batch. On the fast path, go back and
fill in more of `PROFILE.md`, then rerun, the packets get sharper.

Ready to apply? See `prompts/form-filler-prompt.md`, a separate step since it's the only
part that touches a real website with your real information.
