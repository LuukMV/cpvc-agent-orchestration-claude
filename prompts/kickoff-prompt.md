# Kickoff prompt

Two ways to get `PROFILE.md` in place, pick one:

- **Fast path** (see results in minutes): don't fill anything in yet, just paste your resume
  text and/or your LinkedIn "About"/experience section into your first message to Claude
  Code, right along with the prompt block below. The `intake` teammate will build a first
  draft `PROFILE.md` from it automatically.
- **Thorough path**: `cp PROFILE_TEMPLATE.md PROFILE.md` and fill it in by hand first, more
  accurate, slower to start.

You can also do the fast path now and refine `PROFILE.md` by hand later, that's the
intended flow, not a compromise.

Copy the block below into Claude Code as-is, pasting your resume/LinkedIn text above it if
you're on the fast path.

---

We're running a job-hunting pipeline using an agent team. Read `RULES.md` first, it applies
to every teammate below.

If `PROFILE.md` doesn't exist yet and I've pasted resume or LinkedIn text above this prompt
(or told you where a resume file is), spawn the **intake** teammate first to build a draft
`PROFILE.md` from it, then continue once intake is done. If `PROFILE.md` already exists,
skip straight to the next part.

Then spawn four teammates:

- **finder** (using the finder agent type): search real job boards and company sites for
  roles matching my profile, write candidates to `runs/found.md`
- **validator** (using the validator agent type): check each candidate for ghost-listing
  signals and fit against my profile, write verdicts to `runs/validated.md`
- **tailor** (using the tailor agent type): for every GO verdict, draft a tailored resume
  bullet set, cover letter, and application-question answers, written to
  `runs/packets/<company>-<role>/packet.md`
- **fact-checker** (using the fact-checker agent type): the moment tailor finishes a
  packet, verify every claim in it traces to `PROFILE.md` and append a PASS/FAILED
  fact-check section to that packet

Sequence: finder works first, but validator should start checking listings the moment
finder reports the first few rather than waiting for the full batch. Same for tailor once
validator marks something GO, and same for fact-checker the moment tailor finishes any one
packet, don't wait for full batches between steps, let the handoffs overlap. Once all four
are done, summarize back to me: how many found, how many GO, how many packets passed
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
