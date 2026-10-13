# Form-filler prompt (optional, separate step)

This step touches a real website with your real information. Only run it on a packet you've
actually read, whose `## Fact-check` section says PASS, and that you want to apply to.
Requires the Claude in Chrome extension connected (ask in `#help` if you don't see it).

Copy the block below into Claude Code, replacing `{PACKET}` with the path to the packet you
want filled, e.g. `runs/packets/acme-corp-mfg-intern/packet.md`.

---

Read `RULES.md` in full, then use the form-filler agent type to pre-fill the application in
`{PACKET}`. Open the listing's URL in a brand-new tab, fill every field you can from the
packet and `PROFILE.md`, and stop before clicking Submit. Report back exactly what's filled,
what's left blank and why, and confirm the tab is still open and ready for me to review.

---

### If you're filling more than one application

Run this prompt again with a different `{PACKET}` for each one. Each run should open its
own new tab, don't ask it to reuse the last one, that's how you lose a filled-out form with
no way to get it back.
