# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted
  **14 November 2026, 8:40 am IST**. Self-contained: the figures are baked into the file,
  so it needs no network and no connector. Campaign rail, per-ad tables, day-by-day,
  age bands, and the GST-inclusive billing line.

## Status as of 14 Nov 2026: the account is off

**Both remaining campaigns were switched off at 10:33 pm on 13 November 2026** by Abhijeet
Lenka from the Ads Manager app for iOS (activity log, `event_category: "status"`):
`India | MBBS Students - Doctors | App Downloads | signups` and
`Qualified Leads | Campaign`, both Active → Inactive in the same minute. So the 13th is a
part-day of ₹4,696.03, **the 14th carries ₹0.00** against four late-attributed installs and one registration,
and **all ten campaigns now read PAUSED**. This is a deliberate stop, not a billing or
delivery fault — do not write it up as one.

Because nothing is ACTIVE, **the page's default scope is now derived rather than assumed**:

```js
state.activeOnly = CAMP.some(c => c[2] === "ACTIVE");
```

Without that line the report opens on its own "Nothing is running right now" empty state,
which is correct behaviour for a filter but a blank page for a client. The empty state is
still reachable by choosing "All active"; leave both in place.

### Lifetime

| | |
|---|---|
| Media spend | **₹5,21,223.10** across 106 delivery days (31 Jul – 13 Nov) |
| Billed, incl. 18% GST | ₹6,15,043.26 |
| Installs | 45,161 — ₹11.54 each |
| Registrations | 20,659 — ₹25.23 each |

The account grew from ₹1,61,870.20 at the 29 Sep snapshot, and **one campaign is now four
fifths of it**: `India | MBBS Students - Doctors | App Downloads | signups` has taken
₹4,24,384.38 for 18,919 registrations, **₹22.43 each**, against ₹89,451.65 and ₹32.89 at
the last cut. Cost per registration fell month on month and then turned: **₹68.21 in
August, ₹33.68 in September, ₹20.17 in October, ₹25.04 in November.** October is the best
month this account has had.

**A tenth campaign, `Qualified Leads | Campaign` (`120250327482630482`), opened on
6 November** on its own ₹5,000/day. It took ₹24,278.07 for 705 registrations, **₹34.44
each** — it runs copies of the big campaign's own creatives and so far buys the same
registration for half as much again. Worth a question before it is restarted.

The three biggest creatives on the account, all in the signups campaign:
`E3-08a 3 medical apps to 1` (₹1,26,819.57, 6,053 regs, ₹20.95),
`influencer 20oct – Copy` (₹58,874.61, 3,550 regs, **₹16.58** — the cheapest at scale) and
`v5 - I am a med student` (₹54,351.01, 1,734 regs, ₹31.34).

### Known Meta inconsistencies in this cut

- **Campaign 5's day rows total 10,368,003 impressions against the campaign's own lifetime
  10,368,004** — one impression in 10.4 million. Spend, clicks, installs and registrations
  agree exactly on every campaign, and the per-day campaign sums match the
  `level: "ad_account"` daily totals on spend for all 107 days. The same read on 13 Nov
  came back 4 impressions apart from the account rollup. Nothing is derived from the
  difference; it is reported rather than reconciled away.
- **One registration was attributed late into an August campaign.** `India | MBBS Students
  | ios` / age 25-34 now reports 73, not 72, which is what makes its age rows add back to
  the campaign's 184. Age rows for a long-dead campaign are not frozen — re-check their
  install and registration columns every refresh, even when spend cannot move.

## What the page shows

**"The last 10 days"** sits at the top of the all-campaigns view. It rolls `DAILY` up
across whichever campaigns are in scope and shows spend, impressions, CPM, CTR, clicks,
installs, cost per install, registrations and cost per registration, newest first, with a
window total. `DAY_WINDOW` at the top of the rollup controls the length.

Two things it does deliberately:

- **CPM and CTR are re-derived** from each day's own spend, impressions and clicks
  rather than averaged from the per-campaign values. Averaging two campaigns' CPMs
  gives a number that is not any real cost.
- **Reach is not shown at all.** Meta de-duplicates people, so a day's reach is not
  the sum of its campaigns' reach. Per-campaign reach stays on each campaign's tab.
  This is the same rule the campaign and age tables already follow for their totals.

Two more rules, both about not letting a bad day set a number:

- **The window total covers the settled days only.** `SNAP_DAY` keeps its own row,
  labelled *part day so far*, but it is out of the total and every rate beside it,
  and the total row says how many settled days it covers and which day it dropped.
  Meta revises the last two days all day; without this the window's cost per
  registration moved between one rebuild and the next.
- **The "cheapest day" footnote ignores days that barely spent.** A throttled or
  near-dead day still books registrations earned by the days before it, so its cost
  per registration is both meaningless and unbeatable. Only days carrying at least
  half the window's average settled spend are eligible.

**The "Latest day" card hides itself** when no campaign in scope spent anything on the last
day in the data. That is the case in this cut, so it is not on the page — it is derived from
the rows, not switched off by hand.

**"Why the cost per registration moved"** sits on each campaign. Cost per registration is
exactly CPM divided by registrations per 1,000 impressions, so the card plots all three on
one day axis: a rise is either dearer impressions or a weaker response, and the shapes say
which. Under it, a creative-by-creative split compares two equal windows of settled days
(the part-day is excluded from both) and labels each ad — *impressions costlier* is a price
problem, *response fading* is the one a new creative fixes.

That split reads `ADAY`, ad-level day rows indexed by `ANAME`. **`ADAY` has to be refreshed
alongside `DAILY`** or the split silently goes stale while the charts above it move.

The page carries `noindex, nofollow` so it stays out of search results.

A live version of the same report — one that queries Meta each time it is opened — is kept
as a private Claude artifact rather than here.

## Refreshing

Four snapshot timestamps live in `index.html` and one in this file:

1. the HTML comment under `<title>`
2. the masthead `<div class="stamp">`
3. the footer `<span>` — a hand-written narrative, so **read it and rewrite it**, do not
   only swap the date
4. the comment above the data block

Then `const SNAP_DAY`, which must be the last date present in `DAILY`.

### Re-pull `ADAY` back to the last unsettled day — and no further

The old `ADAY` rows for days that were *still settling* at the previous snapshot are wrong
by the time you come back. On this refresh 22, 28 and 29 September had all moved (the 29th
most of all: ₹349.67 → ₹890.76 on the big campaign, because it was that snapshot's
part-day). Those rows had to be re-pulled, not appended to.

But do **not** re-pull further back than the creative-swap boundary below. Rows before it
carry names that Meta no longer reports, and a fresh pull would re-label them.

In this refresh the boundary is **22 September**: everything from the 22nd on was re-pulled
with Meta's current ad names, everything before it kept its existing rows, and the two
halves reconcile against `DAILY` on spend, impressions, installs and registrations for
every campaign on every day — part-day included.

### A rename can hide a creative swap — check the activity log

**This bit the report on 23 September 2026.** Two ads were renamed at Meta and the
refresh carried their whole history forward under the new names. It was not a
rename: the *creative was replaced on the same ad id*, so Meta reported
`v4 — Ecg`'s 837 installs and 313 registrations under
`influencer direct video ad — Rheumatoid Arthritis`, a video that had produced
**nothing**. The client spotted it, not the reconciliation — every total still
balanced, because the numbers were real, just attached to the wrong creative.

The tell is in `ads_account_get_activity_logs` (`event_category: "ad"`), which
records all three events together:

    Ad updated        old_value ["2154354885490353"] -> new_value ["956543453540710"]
    Ad name updated   "v4 - Ecg" -> "influencer direct video ad - Rheumatoid Arthritis"
    Ad status updated Inactive -> Pending Process -> Pending Review -> Active

A pure rename never triggers Pending Review. **If a name changed, pull the
activity log before carrying history forward.** If the creative changed too,
split the ad into two rows at the swap date:

- the retired creative keeps everything up to the day before, status `REPLACED`
- the new creative starts from the swap day, with its own ranged pull for reach

and **append** the new names to `ANAME` while restoring the old names at their
original indexes — every historical `ADAY` row still points at the creative that
actually ran.

**The two splits in this file are frozen and must stay frozen.** `v4 - Ecg` (₹10,361.32)
and `v1 - male ( asking medicos how they study)` (₹1,076.22) are the pre-swap halves of
ad ids `120249212142170482` and `120249183882150482`. Each refresh, the surviving half is
the object's lifetime **minus** the frozen half — on this cut that gave
`influencer direct video ad - Rheumatoid Arthritis` ₹99.64 and left `influencer - hasaan`
at ₹413.03, both confirmed by a ranged pull from 22 September (which is also the only way
to get their reach, since reach cannot be subtracted).

**This refresh had no renames.** `ads_account_get_activity_logs` for `event_category: "ad"`
over 29 Sep – 14 Nov returned 257 entries: 35 `Ad created`, the rest status changes, and
**no `Ad name updated` and no `Ad updated`** at all. Every ad name that existed at the last
snapshot still exists, so nothing carried history under a new label.

### Everything else

- **Never reshuffle `CID`.** `CAMP`, `ADS`, `DAILY` and `AGE` rows all carry a positional
  index into it. New campaigns go on the **end** — `120250327482630482` was appended as
  index 9 on this refresh for exactly that reason.
- Everything except `DAILY` is lifetime (`date_preset: "maximum"`). `DAILY` is per campaign
  per day, dates `"MM-DD"` with `YEAR` prefixed.
- **Ad-level insights do not return `campaign_id` unless you ask for it by name.**
- Archived and deleted ads are **omitted from an unfiltered listing**. Campaign 5's ads came
  up ₹65,551.01 short until the read was re-run with every `effective_status` spelled out
  (`ACTIVE, PAUSED, DELETED, ARCHIVED, IN_PROCESS, WITH_ISSUES, CAMPAIGN_PAUSED,
  ADSET_PAUSED, AD_PAUSED, DISAPPROVED, PENDING_REVIEW, PREAPPROVED, PENDING_BILLING_INFO`).
  Four names had simply vanished; they were ARCHIVED.
- **Re-sort `ADS` descending by spend** after patching, and assert the sort. Do **not** sort
  `ANAME` — `ADAY` indexes into it by position.
- A `time_increment` pull caps out at **200 rows**, silently. Chunk by date and check that
  every entity and every day you asked for came back.

### Checks that must pass before publishing

For every campaign, its `CAMP` row must equal the sums of its `ADS`, `DAILY` and `AGE` rows
on spend, impressions, clicks, installs and registrations; and `sum(ADAY)` must equal
`DAILY` per campaign per day on spend, impressions, installs and registrations. Reach is
excluded from all of these on purpose — Meta de-duplicates it, so it never adds up.

On this cut every one of those passed except the single impression noted above.

## Campaigns not in the report

`India | Influencer Videos | App Registrations` (`120249585723440482`) is on the
account, PAUSED, and has **never delivered**: Meta returns no spend, impression or
result fields for it at all. So it is deliberately left out of `CID` and `CAMP`. Add it
the day it spends, **appended to the end of `CID`** so no historical row re-labels itself.
