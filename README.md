<img width="1254" height="643" alt="Screenshot 2026-10-28 at 3 23 47 AM" src="https://github.com/user-attachments/assets/60f6f7dd-64b1-4036-bf31-66cf9ab09ef6" />

# Ledger Reconciliation

A single-file web app that reconciles a transaction ledger against a daily bank statement. It distinguishes a newly introduced discrepancy from one that's simply carried forward from an earlier day, so you can tell at a glance where a problem actually started.

Open the HTML file in a browser and it runs.

## What It Does

Upload two CSVs — a transaction ledger (`date,amount`) and a daily bank statement (`date,balance`) — and the app:

- Computes a running expected balance from the transactions and compares it, day by day, to what the bank reported
- Flags mismatches, and separates them into two categories:
  - **New break** — the discrepancy originated on this specific day
  - **Carried forward** — the discrepancy is inherited from an earlier, already-flagged day
- Renders an expected-vs-actual balance chart so divergence is visible at a glance
- Surfaces transactions dated outside the bank statement's range as "not yet verifiable," rather than silently ignoring them
- Ships with two one-click sample datasets — a clean one and one with a seeded discrepancy — so the behavior is demoable without needing to find test files

## Why "New Break vs. Carried Forward"

A normal reconciliation just flags every day where the cumulative totals disagree. But if a single error is introduced on day 5, every day *after* day 5 will also show a nonzero difference — which reads as five separate problems when it's really one.

To fix that, the app separately compares each day's change in bank balance to that day's transaction total. If those agree, nothing new happened today — any diff is just carrying over from before. If they disagree, today is where the actual break occurred. That distinction is the core piece of logic the project is built around.

## Design

Styled around a "ledger paper" motif — a pale green ruled background and red-ink error highlighting. Typography pairs a slab serif for headers with a monospace face for all numeric/tabular data, so figures line up the way they would in an actual ledger.

<img width="968" height="399" alt="Screenshot 2026-10-28 at 3 25 16 AM" src="https://github.com/user-attachments/assets/312127e5-a7f3-4165-a0e9-f1959a7acb91" />
<img width="968" height="462" alt="Screenshot 2026-10-28 at 3 26 07 AM" src="https://github.com/user-attachments/assets/1e2c90a2-8ce9-4811-a58e-ab5705c4cb5f" />

## Tech

HTML/CSS/JS. The chart is hand-rolled SVG. Kept intentionally dependency-free so it's a single portable file.

## Known Limitations

- Can't detect two offsetting errors on the same day (e.g. a missing transaction and an extra one that happen to net to zero) — that would require transaction-level bank data rather than a single daily balance.
- Reconciliation is day-granularity only; intraday transaction order isn't modeled.

