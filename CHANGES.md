# Changes in rules version 1.1.0

Fixes for the 19 errors found in the test report, plus extra protection layers.

| Area | Change | Files |
|---|---|---|
| Normalisation | Rules run on a cleaned "match view" (NFKC, zero-width characters removed, single line breaks become spaces). Matches are mapped back so quotes stay verbatim. Fixes E01, E03, E04. | `src/utils/analyzer.js` |
| Look-alike keywords | OTP and CVV match "O T P", "0TP", "C V V". Fixes E05, E06. | `src/data/rules.js` |
| Negation | Clause-aware: negator must be in the same clause (breaks at . ! ? ; line break, "but", "however"...), cannot be followed by "and/then/just/worry/hesitate...", and is also checked inside Hindi and Hinglish matches. Fixes E02, E10, E11. | `src/utils/analyzer.js` |
| Scoring | Explained bonuses (repeat, combo, hidden characters) exposed as `result.scoreAdjustments`. Fixes E07, E08. | `src/utils/analyzer.js` |
| Rules | New: `CRD-02` (forward the code), `CRD-03` (passive card details), `PAY-03` (gift cards), `OFR-02` (guaranteed returns / doubler, split from OFR-01), `FEE-03` (bare fee mention, weak), `EXT-01` (blackmail) and a `blackmail` category with advice. `FEE-01` now needs a demand verb or a stated amount. Fixes E09, E12 to E16. | `src/data/rules.js`, `tactics.js`, `advice.js` |
| Hidden characters | Zero-width or full-width characters add +2 and show a warning. | `analyzer.js` |
| Links | Brand look-alike check (e.g. `hdfcbank-kyc.com`, `paytm.com.evil.xyz`); punycode prefix no longer counted as hyphens. | `src/utils/linkCheck.js` |
| Language | Hinglish is detected. | `analyzer.js` |
| Auth | Stricter email check, non-Latin letters allowed in passwords. Fixes E17 to E19. | `src/utils/authValidation.js` |
| Tests | 251 tests (was 27): per-rule examples, evasion variants, negation table, randomised robustness test, and a labelled scam/genuine set (`evalset.test.js`). `npm test` now runs all. | `src/utils/*.test.js` |
| Transparency | Privacy page and README describe the new normalisation honestly. | `PrivacyPage.jsx`, `README.md` |
| Hardening | Security headers and a report-only Content-Security-Policy (check the browser console, then switch to enforcing). | `vercel.json` |

Behaviour changes to be aware of
- `a@b.c.d` is no longer a valid email (one-letter ending).
- A bare "processing fee" or "registration fee" is now weak (low) on its own; paying it, or a stated amount, is strong.
- "Guaranteed returns" moved from `OFR-01` to `OFR-02` (stronger).

# Changes in rules version 1.1.1 (22 Oct 2026)

Fixes from `TEST_REPORT_2026-10-22.md`.

| # | Change | Files |
|---|---|---|
| 1 | Signup trigger `handle_new_user` restored to the fixed version (no `NULLIF`). The old version crashed signup on the live project on 21 Oct. The live database already has the fix, so **nothing needs to be run in Supabase**. | `supabase/schema.sql` |
| 2 | Server-side errors (status 500+, `unexpected_failure`, "Database error…") now say "The account service hit a problem on its side…" instead of blaming the user's internet. | `src/utils/authValidation.js` + tests |
| 3 | `npm test` lists the 5 test files explicitly, so it works on Windows with any Node version (the `*.test.js` pattern needs Node 21+). | `package.json` |
| 4 | `OFR-02` now catches "guaranteed 3x returns", "assured 12% returns" and "3x returns"; `CHN-01` catches "join our Telegram/WhatsApp group/channel". | `src/data/rules.js` |
| 5 | New category **Electricity / utility disconnection** (`utility_bill`) with its own advice. New rule `THR-03`; electricity/power/connection wording removed from `THR-01` (bank/KYC). | `rules.js`, `advice.js` |
| 6 | Content-Security-Policy allows Google Fonts, so fonts keep working if the policy is switched from report-only to enforcing. | `vercel.json` |
| — | 8 new regression tests (259 total, all pass). Rules version 1.1.0 → 1.1.1. | `src/utils/*.test.js`, `analyzer.js` |

Not changed: simulator choice order (risky first, safe last) — a design decision for you.
