# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 31 October 2026, 11:50 am IST.
  Self-contained: the figures are baked into the file, so it needs no network and no connector.
  Campaign rail, per-ad tables, day-by-day, age bands, and the GST-inclusive billing line.
  Defaults to the campaigns that are active; a switch shows all nine. Only one is
  active now, so the page opens on that campaign's own tab.

**"The last 10 days"** sits at the top of the all-campaigns view. It rolls `DAILY`
up across whichever campaigns are in scope and shows spend, impressions, CPM, CTR,
clicks, installs, cost per install, registrations and cost per registration, newest
first, with a window total. `DAY_WINDOW` at the top of the rollup controls the length.

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
  half the window's average settled spend are eligible. Before this guard the
  footnote named 21 Sep at Rs 17.88 — a suppressed-delivery day that took Rs 733 on
  13,898 impressions against the Rs 3,000-4,000 either side of it — which is not a
  bar any normal day can clear.

**"Why the cost per registration moved"** sits on each campaign. Cost per registration is
exactly CPM divided by registrations per 1,000 impressions, so the card plots all three on
one day axis: a rise is either dearer impressions or a weaker response, and the shapes say
which. Under it, a creative-by-creative split compares two equal windows of settled days
(the part-day is excluded from both) and labels each ad — *impressions costlier* is a price
problem, *response fading* is the one a new creative fixes.

That split reads `ADAY`, ad-level day rows for the campaigns that are still running, indexed
by `ANAME`. **`ADAY` has to be refreshed alongside `DAILY`** or the split silently goes stale
while the charts above it move.

## Where the account stands — 31 October 2026

**October was the account's biggest month by a long way.** ₹2,60,121 of the
₹4,24,735 ever spent here — 61% of lifetime spend — and it bought 13,036
registrations at **₹19.95** against September's ₹33.69. Installs cost ₹9.65 in
October against ₹14.03 in September.

**The ₹1,000-a-day cap set on 27 September did not last.** Daily spend ran at
₹1,100 through 1–2 October, stepped to ₹4,617 on the 3rd and to **₹14,846 on the
4th**, then held between ₹10,000 and ₹13,500 for a fortnight before easing to
₹5,000–6,000 from the 24th. Nothing before 3 October is comparable with anything
after it.

**One campaign is doing all of it.** `India | MBBS Students - Doctors | App
Downloads | signups` (`120249183882130482`) is the only ACTIVE campaign and now
carries ₹3,52,174 of lifetime spend, 33,718 installs at ₹10.44 and 15,829
registrations at ₹22.25. Because it is the *only* live campaign, the page's
`autoTab()` now opens straight onto its tab rather than the all-campaigns view —
the **"last 10 days" rollup is one click away** (the all-campaigns rail button),
not on screen at load. That is existing behaviour, not a regression.

`India | MBBS Students - influencers` (`120249585873390482`) is **PAUSED**. It
last spent on 3 October and closes at ₹7,700.08 for 386 installs and 160
registrations. `LIVE`-style narrative about "a second live campaign" no longer
applies.

**Ten new creatives were built on 3 October** (`E3-01` … `E3-10`) and two of them
carry the month:

| Ad | Spend | Regs | Per reg | Status |
|---|---|---|---|---|
| E3-08a 3 medical apps to 1 | ₹1,23,614 | 5,967 | ₹20.72 | PAUSED |
| E3-09 Asking medicos which AI app they trust | ₹53,244 | 2,449 | ₹21.74 | PAUSED |
| **influencer 20oct – Copy** | ₹43,975 | **3,001** | **₹14.65** | **ACTIVE** |
| E3-10 Rounds with the strictest senior | ₹29,495 | 1,321 | ₹22.33 | PAUSED |
| v5 - I am a med student | ₹54,351 | 1,734 | ₹31.34 | ARCHIVED |
| influencer 27oct | ₹3,761 | 121 | ₹31.08 | ACTIVE |

`E3-08a` is now the largest single ad the account has ever run. But the best rate
of anything with real volume is **`influencer 20oct – Copy` at ₹14.65 a
registration**, and it is still live — worth saying to the client before the
budget moves anywhere else. The other seven E3 creatives took ₹1,588 between
them and remain untested rather than beaten.

## A deleted ad disappears from the listing but keeps its spend

**This is the sibling of the archived-campaign trap in `halcyon-reports`, one
level down.** Two ads were deleted at Meta during October:

- `v5 - I am a med student` (`120249226764800482`) on **15 Oct**
- `influencer direct video ad - Rheumatoid Arthritis` (`120249212142170482`) on **20 Oct**

An unfiltered `level: "ad"` listing **does not return them**, but their spend
stays inside the campaign total — so `ADS` would have summed ₹65,000 short of
`CAMP` with nothing obviously wrong on the page. Both are carried here with
their full history and `effective_status` **ARCHIVED**, which is what Meta
reports for them now.

**Refetch deleted and archived ads by id.** Filter
`effective_status IN [..., "ARCHIVED", "DELETED"]`, or pass the known ids in
`object_ids` — the ids are in the activity log under *Ad status updated* with
`new_value: "Deleted"`. The reconciliation assert is what catches this; do not
skip it.

**No ad was renamed this time.** `ads_account_get_activity_logs`
(`event_category: "ad"`, from 28 Sep) returns 150 entries and **not one
`Ad name updated`** — 14 `Ad created`, the rest status changes. So nothing in
this report was relabelled, and the two 22 September splits stand exactly as
published: `v4 — Ecg` and `v1 — male` keep everything up to the 21st, and the
creatives that replaced them on the same ad ids carry only what they have earned
since (`influencer direct video ad - Rheumatoid Arthritis` ₹99.64,
`influencer - hasaan` ₹413.03, both unchanged in rate because neither has really
run).

## Reconciliation at this refresh

Every campaign's `CAMP` row equals the sums of its `ADS`, `DAILY` and `AGE` rows
exactly, **except the one live campaign**, where:

- `DAILY` and `AGE` sum to ₹3,52,188.16 against `CAMP`'s ₹3,52,174.35 — **+₹13.81
  and +381 impressions**, 0.004% of the total, Meta's own day/age breakdown
  rounding over 63 days plus a still-settling tail.
- `ADS` matches on spend and impressions to the paisa, and is 2 installs and 1
  registration over.
- The account-level lifetime read (₹4,24,763.57 / 1,01,70,752 impressions) sits
  **+₹28.57 and +793 impressions** above the sum of the campaign reads — the same
  part-day movement between two calls that this README has recorded before.

`ADAY` equals `DAILY` per campaign per day on spend, installs **and**
registrations for **every** day in the window, the part-day included. `ADS` is
sorted by spend descending. `ANAME` grew 8 → 22 by appending only; `CID` is
untouched; the last `DAILY` day is `SNAP_DAY`.

**One pre-existing discrepancy, unchanged and not introduced here:** campaign 0
(`India | MBBS Students | ios`, PAUSED since August) has 183 registrations across
its `AGE` rows against `CAMP`'s 184. It has been that way since publication and
the campaign has not spent since; it is Meta's own age-breakdown rounding.

The page carries `noindex, nofollow` so it stays out of search results.

A live version of the same report — one that queries Meta each time it is opened — is kept
as a private Claude artifact rather than here.

## A rename can hide a creative swap — check the activity log

**This bit the report on 23 Sep 2026.** Two ads were renamed at Meta and the
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

## Campaigns not in the report

`India | Influencer Videos | App Registrations` (`120249585723440482`) is on the
account, PAUSED, and has **never delivered**: Meta returns no spend, impression or
result fields for it at all. So it is deliberately left out of `CID` and `CAMP`. Add it
the day it spends, **appended to the end of `CID`** so no historical row re-labels itself.
