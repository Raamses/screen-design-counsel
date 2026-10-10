# The process spec

## The loop (6 steps)

1. **Anatomy extraction** — screenshot (ask if none) + the real component (DOM + CSS) → the precise defect list: crowding (elements-per-row), overlap (colliding boxes), bidi mixing (RTL/LTR entangled in one span), touch-target sizes (<40px).
2. **Design directions** — 3-4 alternatives; the cheapest *reactive* artifact is a SPREAD of skeletons, not one polished guess.
3. **Generative mode** — "design me a new X": the FIRST artifact = a **wireframe-grid sheet** (~6 numbered skeleton directions: band layout, real copy, repo tokens, RTL-correct, fluid primitives). Annotation of placeholders = REJECTED (generative stays generative); the picked direction expands to hi-fi; only THEN does the annotation session open (selection.json).
4. **The Annotation Canvas** — figma-like, simplified (the mechanics in `SKILL.md` step 4): structural ids + kinds; tap = tag/untag; multi-select; the hold-climb; chip-tap notes; the 3-layer capability reminder.
5. **The annotated spec** — selection.json (the contract below).
6. **Gated build** — builder (glm/agy per the policy) implements → the playwright 390×844 screenshot of the real screen vs the locked mock = side-by-side proof in the PR → the ladder → no self-merge → the nightly verifies.

## The selection.json contract

```json
{"design_id":"<pass>/<screen>-<variant>-<ts>", "viewport":"phone|tablet|desktop",
 "tags":[
   {"id":"...", "kind":"section|banner|text|control|decoration",
    "text_sample":"≤40ch", "text_hash":"fnv1a of trimmed sample",
    "dom_order":0, "band":"top|middle|bottom",
    "note":"≤120ch | absent", "selected":true, "ts":1690000000000}
 ]}
```

- Every tag records its viewport + band position — the notes stay readable without the mock open.
- Matching across regenerations: `id`, else `(kind, text_hash, dom_order)`; **remapped tags are FLAGGED in the copy-back — disclosed, never hidden**.
- Notes live IN selection.json — they ride their tags; a chat paste is only a fallback, never the store.

## The fidelity ladder

- **PHONE 390** = full fidelity + THE acceptance viewport ("phone compatible" = the class: fluid 320–430px, also floor-tested at 320).
- **TABLET 834 + DESKTOP 1440** = layout-true (structure, spacing, RTL correct; **no pixel art-direction claims**).
- Nothing absolute-positions over a header band; sizes in rem/%/clamp(); horizontal content capped via min()/max-width; safe-area padding vars (env(safe-area-inset-*)).
- Annotation never replaces the harness PNGs — the fidelity evidence stays non-interactive.

## The reminders (3 layers)

1. First-run coach-mark — the gesture legend floats once per pass; dismissed by the first canvas tap; never repeats.
2. "?" chip — persistent in the mock's corner; the full legend any time; closes on tap-out.
3. Idle nudge — the one-line hint fades in when nothing is tagged for ~6s; gone after the first successful tag — speaks only pre-first-tag.

**Non-blocking, dismiss-everywhere, state-aware: the nudges shrink as knowledge is demonstrated.**

## The pilot pass bar

One generative task + one fix-mode task. Passes when: 10+ elements tagged on a real phone; no misfires; ZERO "which one did you mean?" follow-ups; tags survive one mock revision; a six-width screenshot sweep is clean. Failures disqualify.

## Hard rules

RTL-native + Strict Block Isolation (Hebrew blocks RTL + isolated; equations/every-math ALWAYS `dir="ltr"` + `unicode-bidi:isolate`; never mixed spans) · all strings via react-i18next t() (he/en parity) · general artifact language = ENGLISH · touch targets ≥40px · copy on plain http = execCommand('copy') primary · no implementation before Ram's tags · no builder self-merge · NO paid OpenRouter models · the review ladder per the standing policy (agy gemini-3.1-pro-high = BINDING rung 1; claude-opus-5-5-high VIA AGY = the high-stakes rung 2; the Claude CLI = at the quota reset only).