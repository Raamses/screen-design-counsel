---
name: screen-design-counsel
description: >
  Turn "design me this screen / this screen part is broken / crowded / confusing on mobile"
  into a repeatable design process with an interactive in-UI annotation canvas. Use when Ram
  asks to design a screen or part of a screen, reports a screen as broken/crowded/bidi-mixed
  on mobile, or asks for a mockup/ideation gallery. The canvas is Figma-like simplified:
  structural elements are tappable (tap = tag with a numbered chip, tap again = untag,
  multi-select by tapping), text is selectable only as whole blocks, viewports switch between
  desktop / tablet / phone, and the phone rendering is compatible with the whole class of
  phones (fluid 320-430px + safe areas), never one device's resolution. Ram's tagged picks +
  per-tag notes become the implementation spec (selection.json), which goes through the gated
  build: builder -> review ladder -> merge with a screenshot-vs-mockup proof. Never run paid
  OpenRouter models.
---

# Screen-Design-Counsel (v3 — PILOT-FIRST, 3/3 voices LOCKED)

Origin: the HMA blitz-screen loop (2026-10-10): complaint → anatomy → the agy counsel
(COMBO verdict) → the interactive pick galleries → Ram picked twice → the spec → the gated build.
Status: **PILOT-FIRST — LOCKED**. Round-1 verdicts stand (pilot-first; harness = the fidelity
gate; 3-tier cost ladder). Hermes Counsel v3 (mailbox 67069c9c, 2026-10-10) folds in Ram's
v2 re-spec; its reasons quote his verbatim rulings.

## The process

1. **Anatomy extraction** — screenshot (ask if none) + read the REAL component (DOM + CSS) →
   precise defects: crowding, overlap, bidi mixing, touch-target sizes.
2. **Design directions (fix-mode)** — 3-4 concrete alternatives.
3. **Generative mode** ("design me a new X"): the FIRST artifact = a **WIREFRAME-GRID SHEET** —
   ~6 numbered skeleton directions (band layout, real Hebrew copy, repo tokens, RTL-correct,
   fluid primitives). The cheapest thing Ram can react to is a spread of skeletons, not one
   polished guess. Annotation of placeholders is REJECTED: it collapses the generative intent
   back into fix-one-screen mode. Chain: intent brief (TG/mailbox one-liner + kid age + mode)
   → sheet → tap to pick directions → picked expand to hi-fi pages → THAT opens the annotation
   session → selection.json → build ladder. The wireframe stage builds ONLY after the
   annotation pilot passes (same gate discipline).
4. **The Annotation Canvas** — one HTML page on the Pi share (:8909), FIGMA-LIKE SIMPLIFIED:
   - `ann-runtime.js` (≤200 lines): runtime-assigned ids (NEVER hand-stamped); traverses the
     rendered artifact; event delegation at root with `closest('[data-ann]')`; tap toggles
     `data-ann-tagged`; multi-select = set membership. One gesture path: TAP = toggle.
   - Structure granularity ENFORCED BY SCHEMA: every tagged structural unit carries
     `data-ann-kind ∈ {section, banner, text, control, decoration}`; text is taggable ONLY at
     block level (the whole text run). Target with no kinded ancestor → runtime attaches to
     the nearest kinded ancestor; none exists → `kind=other` + warning in the summary.
   - Chips: numbered, anchored to the element's corner, clamped into the frame. Dense screens:
     number-only chips + bottom legend; WARN above 20 tags in one session.
   - **Per-tag notes (v1, form-fixed)**: ONE constrained line ≤120 chars, stored IN
     selection.json. Notes ride their tags through regeneration matching — they never live
     only in chat. Empty note = tag-only, renders as "?" — tags without notes stay useful
     (silent region marks). Chat notes remain optional extra context, never the store.
   - Long-press anywhere = untag-all (confirm).
   - **HOLD ~1s on a tagged element = the tag relocates to its PARENT** (Ram 22:47: "Maybe holding it? Tap and stay a second?" — the level-climb replaces the finicky retap-timing; hold again on the parent = climb further, up to the section root).
   - TAP a tagged element = UNTAG it (plain toggle — no timing split, multi-select stays free).
   - TAP an element's [#n] chip = open/edit its note (≤120ch, rides selection.json).
   - Empty-space taps = no-op; LONG-PRESS (2s+) = untag-all (confirm) — the only mass clear.
   - **Capability reminder (3 layers, non-blocking — Ram 22:57: "get a reminder on what I can in the ui")**:
     (1) FIRST-RUN coach-mark — a compact gesture legend floats over the mock once per pass (Tap = tag · Hold 1s = parent · Chip = note · Long-press = clear); dismissed by the first canvas tap; never repeats;
     (2) "?" chip — persistent in the mock's corner, tap = re-shows the full legend any time; closes on tap-out;
     (3) idle nudge — when NOTHING is tagged for ~6s, a one-line hint fades in; gone after the first successful tag (only pre-first-tag; the affordances carry the rest — the empty-note "?" chips invite the note by their look).
   **Principle: non-blocking, dismiss-everywhere, state-aware (the nudges shrink as the user demonstrates knowledge).**
5. **The annotated spec (selection.json contract)**
   `{design_id:"<pass>/<screen>-<variant>-<ts>", viewport:"phone|tablet|desktop",
     tags:[{id,kind,text_sample(≤40ch),text_hash(fnv1a of trimmed sample),dom_order,
            band:top|middle|bottom,note(≤120ch|absent),selected,ts}]}`
   Every tag records its viewport + band position so notes are readable without the mock open.
   Matching across regenerations: `id`, else `(kind, text_hash, dom_order)` fallback;
   remapped tags are FLAGGED in the copy-back — disclosed, never hidden.
6. **Gated build** — builder (glm/agy per the policy) implements; playwright screenshot of the
   REAL screen (phone = 390×844 the acceptance viewport) vs the locked mock = side-by-side
   proof in the PR; the review ladder: **agy gemini-3.1-pro-high = binding rung 1;
   claude-opus-5-5-high VIA AGY = the high-stakes rung 2; Claude CLI = at quota reset only.
   NO paid OpenRouter models. No builder self-merge.**

## Viewports: ONE fluid frame + a segmented control

ONE fluid implementation re-rendered at DESKTOP 1440 / TABLET 834 / PHONE 390 — one truth,
three evidences (three static frames = three stale copies). Fidelity ladder: PHONE = full
fidelity and THE acceptance viewport (fluid 320–430px, also tested at 320 as floor — "phone
compatible" means the class, not one instance); TABLET + DESKTOP = layout-true (structure,
spacing, RTL correct; NO pixel art-direction claims). Fluid rules baked into the template:
no px-fixed canvas; sizes in rem/%/clamp(); horizontal content bounded via min()/max-width
caps; safe-area padding vars (env(safe-area-inset-*)); bands stack, chips wrap; NOTHING
absolute-positions over a header band (the blitz root cause). The template IS the repo's
lesson: it demonstrates the fluid/safe-area pattern (today only 2 repo files use safe-areas).

## The ladder (BOTH stages, asymmetric — cost-bomb rule)

- **Stage A — directions sheet:** ONE senior agy skim against the brief + RTL/viewport gates
  (free); the mechanical overlap/RTL harness check runs on ALL skeletons. NEVER a full
  N-directions × 3-models pass (the cost bomb).
- **Stage B — post-pick hi-fi, BEFORE any implementation:** full ladder — agy +
  opus-5.5-via-agy + Hermes — ONCE on the ONE picked direction. RTL/kid-cognition/fidelity
  risk concentrates here and a PR follows.
- Anti-drift: a failing direction is fixed INSIDE the same direction id; re-counsel fires
  only when the direction itself changed.

## Mockup server

The static share server gains ONE ~25-line loopback-only append handler: `POST /selection` →
`passes/<pass>/selection.json`. No url-state, no cross-device sync, no new surface beyond
localhost. The copy-back summary button is RETAINED as the fallback when the POST is not
reachable — the protocol degrades gracefully, never blocks.
(v3 reversal of the round-2 "zero backend": v3 sessions span MULTIPLE artifacts — sheet →
hi-fi pages — with accumulating multi-select; per-page clipboard copy-back is error-prone
on the phone.)

## Hard rules

- ALL visible strings via react-i18next `t()` (he.json + en.json parity), game content
  he/en-toggled, general artifact language = English.
- RTL-native design: Hebrew blocks RTL + isolated; math/equations ALWAYS `dir="ltr" +
  unicode-bidi:isolate` — Strict Block Isolation, never mixed spans.
- Touch targets ≥40px; fluid widths; `-webkit-text-size-adjust:100%`.
- Copy on plain http = execCommand('copy') primary (the clipboard API needs https).
- No implementation before Ram's tags/notes. No self-merge. Every artifact render-checked
  before delivery.
- ANNOTATION NEVER REPLACES THE HARNESS: the session captures WHAT/WHERE; the harness PNGs
  stay the fidelity evidence (non-interactive). Annotation is a toy if it substitutes for them.

## Harness (the fidelity gate — same gate for mocks and implementation PRs)

Playwright renders every artifact at 1440/834/390 + a 320 floor test, he AND en: PNG set +
overlap report per artifact. The blitz-scale rule holds: element vanishing/overlapping at a
breakpoint is a reportable defect (`ok | orphaned@viewport | hidden@phone` classification).

## Pilot pass bar (the promotion to "ship")

The annotation pilot PIGGYBACKS the RUNNING blitz card (workboard 498fd40e): Ram tap-tags the
LIVE blitz hi-fi mock (~/html-out/hma-blitz-equation-v2.html) as soon as it renders — zero new
plumbing to validate the runtime, tap-toggle, notes, selection.json and the multi-viewport
frame on live material. Passes when: 10+ elements tagged on a real phone; ZERO "which one did
you mean?" follow-ups; tags survive one mock revision (id/hash/order matching proven); the
six-width screenshot sweep is clean. Failures disqualify.

## V1 build order

1. `ann-runtime.js` (toggle/chips/notes/localStorage/POST + copy-back)
2. Fluid token template (clamp/safe-area/band primitives + data-ann-kind whitelist classes)
3. selection.json schema + matching rules
4. Harness: Playwright 1440/834/390 (+320 floor) he+en, PNGs + overlap report per artifact
5. `gen_wireframes` (intent brief → numbered skeleton sheet)
6. SKILL.md v2 (this file) — done in-session

## V1 exclusions

Free-pins, drawing, lasso, comment threads, live-app injection, drag manipulation (V2 if the
pilot shows the need).