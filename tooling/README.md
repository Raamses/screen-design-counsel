# Tooling — the pieces and their ship states

The build runs under workboard card `8d574e39-8992-4313-9563-baba9485f3b9` (labels: hma / skill / pilot) — the **piggyback pilot**: attach the runtime + the kind markup to the LIVE blitz mock (`~/html-out/hma-blitz-equation-v2.html`) and serve on :8909. Ram's taps ARE the pass bar — stop there and hand it to him.

Ship-order deliverables:

1. **`ann-runtime.js`** (≤200 lines) — SPEC-TO-BUILD
   runtime-assigned ids (never hand-stamped); traverses the rendered artifact; event delegation at root with `closest('[data-ann]')`; tap toggles `data-ann-tagged` = MULTI-SELECT (set membership); chips numbered, anchored to the element's corner, clamped in-frame; dense screens = number-only chips + a bottom legend; warn above 20 tags/session; granularity ENFORCED BY SCHEMA (`data-ann-kind ∈ {section, banner, text, control, decoration}`); no kinded ancestor → attach to the nearest kinded ancestor; none → `kind=other` + a summary warning. State persists in localStorage keyed by design-id (survives reload/offline). Long-press anywhere = untag-all (confirm); re-tap a tagged element = open/edit its note.

2. **`mockup-server.py`** — SPEC-TO-BUILD
   the static share server + ONE ~25-line loopback-only append handler: `POST /selection` → `passes/<pass>/selection.json` (append-safe; no url-state; no cross-device sync). The copy-back summary button (execCommand('copy') — plain http) is RETAINED as the fallback: the protocol degrades gracefully, never blocks.

3. **`verify.py`** (extends `tools/verify.py`) — SPEC-TO-BUILD
   every artifact at 1440 / 834 / 390 **+ a 320 floor**, in he AND en → PNGs + an overlap report per artifact; element states: `ok | orphaned@viewport | hidden@phone`. **Phone 390 full fidelity = THE acceptance viewport**; tablet + desktop = layout-true only (no pixel art-direction claims). Annotation never replaces the harness PNGs — the fidelity evidence stays non-interactive.

4. **`extract_tokens.py`** — SPEC
   per-repo palette/spacing token extraction from tailwind config; feeds the frame templates — no palette drift.

5. **`gen_wireframes.py`** — SCAFFOLD-AFTER-PILOT
   intent brief (TG/mailbox one-liner + kid age + mode) → ~6 numbered skeleton directions. Build ONLY after 1–4; the generative stage itself runs after the annotation pilot passes (the same gate discipline).

Hard rules carried in: touch targets ≥40px; RTL-native (math/equations `dir="ltr"` + `unicode-bidi:isolate` — Strict Block Isolation); mock copy he/en toggled; EVERY artifact render-checked (verify.py) before done.