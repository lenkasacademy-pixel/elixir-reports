# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 19 November 2026, 9:15 am IST.
  Self-contained: the figures are baked into the file, so it needs no network and no connector.
  Campaign rail, per-ad tables, day-by-day, age bands, and the GST-inclusive billing line.
  Defaults to the campaigns that are active; a switch shows all ten.

**The lifetime layers stop at the end of `SNAP_DAY - 1`.** `CAMP`, `ADS` and `AGE` are pulled
with `time_range` ending the day before `SNAP_DAY`, not `date_preset: "maximum"`. While a
campaign is live its lifetime total moves between one call and the next — on this refresh
campaign 9's figure read ₹48,573.16, then ₹48,573.29, then ₹48,573.49 across three calls
minutes apart, because Meta was still revising 18 November. Pull the three lifetime layers
against a closed window and they agree with each other to the paisa; pull them against
`maximum` and they never will. `DAILY` and `ADAY` still carry `SNAP_DAY` as the part-day row,
which is the only place the live day appears.

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

## What changed between 29 Sep and 19 Nov 2026

**The account scaled about twelvefold and then handed over to a new campaign.**
`India | MBBS Students - Doctors | App Downloads | signups` went from ₹1,000 a day to
₹8,000 on 3 Oct at 11:07 am and ₹12,000 on 4 Oct at 12:10 pm, back to ₹6,000 on 20 Oct,
₹4,500 on 9 Nov and ₹3,500 on 10 Nov (activity log, actor Abhijeet Lenka). It stopped
delivering after 15 Nov and is paused. **`Qualified Leads | Campaign`
(`120250327482630482`) is campaign index 9**, appended to the end of `CID`; it first
delivered on 6 Nov and is the only ACTIVE campaign, climbing ₹2,000 → ₹8,000 a day
between 6 and 18 Nov. Eleven E3-xx creatives were built on 3 Oct at 11:58 am through the
ads MCP server and seven more have been added since; `ANAME` grew from 8 names to 26,
every original index left where it was.

**14 Nov 2026 is a zero-delivery day that still books results.** The account spent nothing
and served no impressions, yet 13 installs and 31 registrations landed against clicks from
earlier days. Keep the row: drop it and the month no longer sums to the sum of its days.
`per()` now returns an em dash rather than `₹0.00` when spend is zero and the count is not,
because "₹0.00 per install" reads as free and it is not a cost at all.

**No ad was renamed or re-creatived between 28 Sep and 19 Nov** — checked in
`ads_account_get_activity_logs` (`event_category: "ad"`, three windows to get under the
per-call cap). Only status changes and new-ad creations. The two 22 Sep splits below are
unchanged.

A second live campaign, **India | MBBS Students - influencers** (`120249585873390482`),
opened on 24 Sep 2026 on its own ₹3,000/day budget alongside the original ₹3,000/day
campaign. Account spend roughly doubled that day — that is a deliberate second campaign,
not a budget change on the first. It runs the same three influencer creatives, and so far
only `Influencer - 11sep` has any real delivery in it.

**Budgets were cut on 27 Sep 2026 at 7:36 am** — both campaigns went from ₹3,000/day to
₹1,000/day (activity log, Power Editor, actor Abhijeet Lenka), so the account cap fell
from ₹6,000/day to ₹2,000/day. **The first day under the cap was the cheapest normal day
in a fortnight**: 27 Sep settled at ₹1,939.44 for 64 registrations, ₹30.30 each, against
₹41.74 on the 26th at three times the spend. **28 Sep is not a fair comparison**: it
spent only ₹709.48 (31 registrations, ₹22.89 each) because delivery thinned all day and
all but stopped from 3 pm to 9 pm (₹2.74 across those six hours, hourly breakdown). It
resumed in the 9 pm hour, right after ₹30,000 was added to the prepaid balance at 9:32 pm
(activity log, "Money added to balance"). Watch the balance: a stall like that looks
like a cheap day in the tables. At 7:34 am the influencer ad set `120249585873460482`
also moved from "Automatically bid for actions" to "Optimize bid for actions" with a
**₹19.00 bid cap**. Anything from 27 Sep onward is a different regime; do not read it
against the ₹6,000/day days. `influencer - hasaan` was switched off in the main campaign
at 9:25 pm on 26 Sep and is now PAUSED in `ADS`.

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
This is how `Qualified Leads | Campaign` joined on 19 Nov 2026, as index 9.

## Two pulls that silently come back short

Both of these cost a reconciliation failure on the 19 Nov 2026 refresh.

**ARCHIVED and DELETED ads are missing from an unfiltered ad listing, but their spend
stays in the campaign total.** Campaign 5's ads summed ₹359,876.73 against its own
₹425,427.74 — ₹65,551.01 short, all of it in four archived ads
(`v5 - I am a med student` ₹54,351.01, the Rheumatoid Arthritis ad ₹10,460.96,
`influencer - 20oct` ₹631.34, `E3-06 My entire AI stack` ₹107.70). Always pass
`effective_status IN [... "ARCHIVED", "DELETED" ...]` on every ad-level and
campaign-level pull. The same applies to the `age` breakdown: a `limit` that looks
generous is counted in rows, and campaign 9 lost its `65+` band (₹450.45) that way.

**A wide ad-level `time_increment: 1` pull is truncated without saying so.** Two 30-day
windows over three campaigns returned 396 of the 488 rows that exist — 4 Oct came back as
₹257.97 against the ₹14,846.11 the day really took. There is no `next_cursor` and no
error. **Pull `ADAY` in windows of about eight days** and reconcile each window against
`DAILY` before merging; eight such windows returned all 488 rows and every one of the
61 days then matched `DAILY` exactly on spend, impressions, installs and registrations.
