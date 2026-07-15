# VERIFY-REPORT — Ministry Barbers Concept Homepage
**Independent adversarial pass** · **Verifier:** Beatrice (independent of the builder, Winston) · **Date:** 2026-07-15
**Asset:** `index.html` (614 lines) · **Live:** https://jettsclaw.github.io/ministry-barbers-concept/
**Method:** live-URL fetch, asset reachability, CDN-failure reasoning, spec ↔ build reconciliation against STRATEGY-SPEC.md §7 brand rules. Ran the real checks — did not grade on prose.

---

## Verdict: **SHIP-READY as a pitch artifact, with one robustness fix recommended before it's MJ's first impression.**

The build is on-brand, honest, and genuinely live. Nothing fabricated, no typeset logo, no stock/AI faces, credit line present, placeholders labelled. One real fragility (Finding 1) could blank the page on a bad network — worth 10 minutes to harden before it goes to MJ.

---

## What was verified live (PASS)

| Check | Result |
|---|---|
| Live URL responds | `200`, 40,502 B (byte-matches local `index.html`) |
| All 7 referenced assets serve live | `logo.webp`, `mj-portrait.webp`, `academy-room-web.jpg`, `starter-kit.webp`, `clipper.png`, `trimmer.png`, `shaver.png` — all `200` |
| Git shipped | `origin/main` @ `43fe09e`, GitHub Pages serving |
| Real assets only (spec rule 1) | Real MJ portrait, real academy room, real chrome logo, real TUFT products — confirmed, no AI faces, no typeset brand name |
| Honest claims (rule 5) | 4.6★/230, 553K, 11M, SBS/Ch9, NSW-schools-first — all MJ's own verifiable claims; booking app & shop clearly marked "coming soon" |
| Anton display font (rule 3) | Loaded via Google Fonts, applied to all headings |
| Credit line (rule 7) | Footer: "Concept site by Automaitions — a taste of what's possible." ✔ |
| Reduced-motion | `@media(prefers-reduced-motion)` freezes animation + forces `.reveal` visible ✔ |
| Strategy ↔ build | Single primary CTA (Book), stat strip at position 2, hex-LED hero as the one signature moment, real-name reviews — matches STRATEGY-SPEC §5 selling sequence |

---

## Findings

### 1 — HIGH · Content visibility depends on the Lenis CDN loading
`index.html:566–583`. The scroll-reveal `IntersectionObserver` (line 580) runs **after** `new Lenis(...)` (line 567) inside the same `<script>`. If jsdelivr is blocked or down (corporate wifi, ad-blockers, flaky mobile), `new Lenis()` throws a `ReferenceError`, script execution aborts, and the observer never attaches — so **every `.reveal` block (all content below the hero) stays at `opacity:0`** and the page reads as blank below the fold. There is also no `<noscript>` fallback (JS-off = blank body).
- **Impact:** this is a pitch MJ will open on an unknown phone/network. A single CDN hiccup makes it look broken — the worst possible first impression for a "we ship the solution" pitch.
- **Both CDNs were reachable at verify time (200/200)** — so it works today. The risk is environmental, not present-state.
- **Fix (10 min):** wrap the Lenis init in `try/catch`, OR move the reveal observer above the Lenis block, OR add a failsafe (`setTimeout(()=>document.querySelectorAll('.reveal').forEach(e=>e.classList.add('in')),1500)`).

### 2 — LOW–MED · Brand rule 2 ("amber used once") is literally violated (3×)
Spec §7.2 locks amber as the single warm accent for the academy "SOLD OUT" only. Live it appears **3×**: SOLD OUT (intended) + the "Live pricing… coming soon" pill + the phone "Preview" pill. Defensible as a placeholder convention, but it's a deviation from the locked spec. Either desaturate the placeholder pills to chrome/muted, or amend the spec rule to allow amber for placeholders.

### 3 — LOW · Invented barber name "Berri" in the phone mockup
`index.html:410` ("Today · with Berri"). Spec rule 1 = real names only. It sits inside a clearly-labelled "Preview" mockup so risk is low, but it's an unverified name on MJ's own brand. Swap to a real Ministry barber or a neutral label ("Your barber").

### 4 — LOW · Academy "Apply for 2027" CTA routes to the booking-app teaser
`index.html:384` → `#book`. An academy lead lands on a chair-booking mockup, not an application path. **Already flagged in STRATEGY-SPEC §9** as a known open question, so it's acknowledged, not a surprise — carry into the client build as the real application/deposit funnel (spec calls this the biggest quick revenue win).

---

## Not issues (checked, cleared)
- Address/phone (`219 Miller Rd, Bass Hill NSW 2197` · `0499 917 776`) consistent across `tel:` links and footer.
- Mobile: grids collapse to 1–2 col at 940px, clamp-sized type — structurally sound for 375–390px (recommend one real WebKit render before it becomes the live client site, per spec §120's Attention-AI follow-up note).
- Reviews use real first names and real review text (spec rule 1) — the "owner leads from the front" quote is the intended emotional core.

**Bottom line:** ships as a concept pitch now. Fix Finding 1 before it's the artifact MJ judges Automaitions by.

---

## RESOLUTION — fixes applied post-verify (Winston, 2026-07-15, commit ef07440)

A **second** independent pass (general-purpose agent, briefed only on goal + live URL) ran in parallel and surfaced conversion/mobile issues; both verifiers' findings were resolved together. Every fix re-verified by screenshot at 1440 + 390.

- **Finding 1 (HIGH — CDN blank-page risk): FIXED.** Lenis init wrapped in `try/catch` + guarded on `window.Lenis`; anchor scrolling falls back to native `scrollIntoView`; reveal observer decoupled from Lenis with a 2s force-show failsafe and a no-IntersectionObserver fallback. A CDN miss can no longer abort the script or hide content. Console clean, reveals confirmed firing.
- **Finding 2 (amber used 3×): FIXED.** Placeholder "coming soon" pills desaturated to chrome/muted; amber now appears **only** on the academy "SOLD OUT" — spec §7.2 honoured.
- **Finding 3 ("Berri" name): NO CHANGE NEEDED.** "Berri" (Mohammad Berri) is a **real** Ministry barber/manager — the most-praised name in their Google reviews (per research). Grounded, not invented; kept for authenticity.
- **Finding 4 (academy CTA → booking teaser): FIXED (interim).** Academy CTA now `tel:0499917776` (a real action); the dedicated application/deposit funnel remains the client-build item per STRATEGY-SPEC §9.

### Second-pass findings also resolved
- **BLOCKER — all Book CTAs dead-ended at "coming soon": FIXED.** Every Book CTA now `tel:0499917776`; app-teaser gained an explicit "call to book" fallback button.
- **HIGH — no mobile nav + unlabeled arrow CTA: FIXED.** Added hamburger → full-screen glass menu (verified open/close at 390px); nav CTA labelled "Book" + `aria-label`.
- **MEDIUM — 2010/2011 timeline contradiction: FIXED.** Prose reconciled with the timeline chips.
- **MEDIUM — fake-interactive phone mockup: FIXED.** Screen set `pointer-events:none`; "Preview" badge retained.
- **MEDIUM — hollow feature-card void: FIXED.** All service cards now top-align copy under the heading.
- **Jett polish — "legend" descender clipping: FIXED.** line-height + padding; "g" fully visible.
- **Jett polish — SBS/Ch9 trust banner + stats-as-marquee: DONE.** Real SBS wordmark + Nine "9" logo (Wikimedia Commons, mono-white) now scroll in one subtle trust marquee under the hero.

**Post-fix status:** showstopping AND converting (real call-to-book on every CTA) AND easy to use (working mobile menu, CDN-resilient). Remaining items are client-side (fact sign-off + the real booking/academy funnels), tracked in STRATEGY-SPEC §9.
