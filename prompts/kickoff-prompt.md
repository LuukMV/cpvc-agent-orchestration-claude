# Kickoff prompt

Two ways to get `PROFILE.md` in place, pick one:

- **Fast path** (see results in minutes): don't fill anything in yet, just paste your resume
  text and/or your LinkedIn "About"/experience section into your first message to Claude
  Code, right along with the prompt block below. The `intake` teammate will build a first
  draft `PROFILE.md` from it automatically.
- **Thorough path**: ask your AI to copy `PROFILE_TEMPLATE.md` to `PROFILE.md` and fill it in by hand first, more
  accurate, slower to start.

You can also do the fast path now and refine `PROFILE.md` by hand later, that's the
intended flow, not a compromise.

Whichever path you pick, add these two lines (write whatever is true for you):

```
Work authorization: U.S. citizen, no sponsorship needed
Currently enrolled: yes
```

Resumes almost never say either one and most postings ask, so without them nearly every
packet comes back with `[CONFIRM]` questions about them.

Copy the block below into Claude Code as-is, pasting your resume/LinkedIn text above it if
you're on the fast path.

---

We're running a job-hunting pipeline using an agent team. Read `RULES.md` first, it applies
to every teammate below.

If `PROFILE.md` doesn't exist yet and I've pasted resume or LinkedIn text above this prompt
(or told you where a resume file is), spawn the **intake** teammate first to build a draft
`PROFILE.md` from it. Don't spawn any other teammate until intake has reported back
finished; finder needs the complete profile to search. If `PROFILE.md` already exists,
skip straight to the next part.

Keep the cost down. Every agent file in `.claude/agents/` already names the smallest model
that does its job well (Haiku for finder, Sonnet for the rest), so spawn each teammate with
its agent type and don't name a model in the spawn message, that would override it. Never
run a teammate on Opus. You're the coordinator: don't do a teammate's work yourself, and
keep your own messages short.

Track the whole run in your task list so I can watch it in this chat: one task per stage
(profile, find, validate, tailor, fact-check), then one task per GO listing named
`<Company>: <Job title>`, completed once its packet has a fact-check verdict, with PASS or
FAILED in the task. Update it every time a teammate reports back, and never complete a task
before its files are written.

Then spawn four teammates:

- **finder** (using the finder agent type): search real job boards and company sites for
  roles matching my profile, write candidates to `runs/found.md`
- **validator** (using the validator agent type): check each candidate for ghost-listing
  signals and fit against my profile, write verdicts to `runs/validated.md`
- **tailor** (using the tailor agent type): for every GO verdict, draft a tailored resume
  bullet set, cover letter, and application-question answers, written to
  `runs/packets/<company>-<role>/packet.md`
- **fact-checker** (using the fact-checker agent type): the moment tailor finishes a
  packet, verify every claim about me traces to `PROFILE.md` and every claim about the
  employer traces to the posting, and append a PASS/FAILED fact-check section to that packet

If fact-checker marks a packet FAILED, have tailor revise only the flagged lines and
fact-checker check it again. One revision round per packet; if it fails twice, leave it
FAILED and tell me.

Sequence: finder works first, but validator should start checking listings the moment
finder reports the first few rather than waiting for the full batch. Same for tailor once
validator marks something GO, and same for fact-checker the moment tailor finishes any one
packet, don't wait for full batches between steps, let the handoffs overlap. Once all four
are done, run `node scripts/applications.js` so `runs/APPLICATIONS.md` lists every packet
with its final fact-check result, then summarize back to me: how many found, how many GO, how many packets passed
fact-check outright, and name any that failed.

---

### Going further

Rerun this with a different `PROFILE.md` (a different target role, say) to see it build a
fresh batch. Open `runs/validated.md` after a run and you'll see every SKIP has a real
reason attached, not just a silent drop, that's the validator doing its job. Open a packet
and scroll to its `## Fact-check` section, that's the fact-checker doing its job.

If you did the fast path, go back and fill in the rest of `PROFILE.md` by hand, especially
"What you're looking for" and "Never say this", then rerun. You'll notice the packets get
sharper.

When you're ready to actually apply to one of the packets, see
`prompts/form-filler-prompt.md`. That's a separate step on purpose, since it's the only
part of this pipeline that touches a real website with your real information.
