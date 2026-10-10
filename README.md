# screen-design-counsel

A process + tooling for designing app screens **with** AI agents — from "this screen is broken on my phone" (a screenshot) to a screenshot-verified prod landing.

Origin: the HMA (hebrew-math-adventures) bubble-game Blitz screen rework loop, 2026-10-10 — live-validated end to end on real material.

## The loop (6 steps)

1. **Anatomy extraction** — screenshot + the real component's DOM/CSS → precise defects: crowding, overlap, bidi mixing, touch-target sizes.
2. **Design directions** — 3-4 concrete alternatives.
3. **Generative mode** ("design me a new X") — starts as a low-fi **WIREFRAME-GRID SHEET** (~6 numbered skeleton directions: band layout, real copy, repo tokens, RTL-correct, fluid primitives). The cheapest thing Ram can react to is a spread of skeletons, not one polished guess. **Annotation of placeholders is rejected** — it collapses the generative intent back into fix-one-screen mode. Chain: intent brief → sheet → tap to pick directions → picked expand to hi-fi pages → that opens the annotation session → selection.json → build ladder.
4. **The Annotation Canvas** — one HTML page on the Pi share (:8909). **FIGMA-LIKE, SIMPLIFIED:**
   - Structural elements only: `data-ann-id` + `data-ann-kind ∈ {section, banner, text, control, decoration}`; text taggable ONLY at block level.
   - Tap = tag (a ring + a numbered chip `[#n]`) → tap-again = untag; **multi-select by plain taps**.
   - **HOLD ~1s = the tag relocates to the PARENT** (the level-climb; hold again = climb further, up to the section root).
   - Chip-tap = edit the note (≤120 chars — notes ride selection.json and survive regenerations via id → kind+text-hash+dom-order matching; remapped tags flagged in the copy-back, never hidden).
   - Long-press = untag-all (confirm); empty taps = no-op — no accidental clears.
   - **Capability reminder** (3 layers, non-blocking, state-aware): a first-run gesture coach-mark (dismissed by the first tap), a persistent "?" chip for legend recall, an idle nudge that speaks only pre-first-tag.
5. **The annotated spec (selection.json contract)** — the implementation ground truth:
   `{design_id:"<pass>/<screen>-<variant>-<ts>", viewport:"phone|tablet|desktop", tags:[{id,kind,text_sample≤40ch,text_hash(fnv1a of trimmed sample),dom_order,band:top|middle|bottom,note≤120ch|absent,selected,ts}]}`
   Every tag records its viewport + band position — notes stay readable without the mock open.
6. **Gated build** — builder (glm/agy per the policy) implements → playwright 390×844 screenshot of the REAL screen vs the locked mock = side-by-side proof in the PR → the ladder: **agy gemini-3.1-pro-high = binding rung 1; claude-opus-5-5-high VIA AGY = the high-stakes rung 2; Claude CLI = at quota reset only. NO paid OpenRouter models. No builder self-merge.** The nightly verifies post-merge.

## Structure

- `SKILL.md` — the skill the agents invoke (the v3-locked draft)
- `docs/process.md` — the full process spec + the selection.json contract
- `tooling/` — ann-runtime.js / mockup-server / verify harness / token extraction / wireframe generator
- `examples/` — the pilot galleries (the HMA bubble-blitz loop: the before-strip + the picks bakes the spec)

## Extension points

- **tokens** — auto-extract per-repo from the tailwind config (no palette drift)
- **kinds** — extend `data-ann-kind` per domain (games/web/tools)
- **rungs** — the review ladder per the standing policy; cost-bomb safe: one senior skim over the directions, the full ladder only ONCE on the picked hi-fi
- **templates** — sheet / canvas / gallery variants per app

## Adoption

1. Clone; drop `SKILL.md` into every lane's skills dir (orchestrator / Hermes / claude-win).
2. The tooling ships into the build lane — the ship states live on the card tracker (the blitz piggyback pilot: card 498fd40e's board).
3. The Pi share = `~/html-out` static server on :8909 — the gallery's home.

---

MIT · AmosBot, 2026-10-10