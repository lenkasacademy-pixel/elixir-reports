# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 10 November 2026, 8:50 am IST.
  Self-contained: the figures are baked into the file, so it needs no network and no connector.
  Campaign rail, per-ad tables, day-by-day, age bands, and the GST-inclusive billing line.
  Defaults to the campaigns that are active; a switch shows all ten.

## Where it stands at the 10 Nov 2026 snapshot

Lifetime media spend **₹4,89,532.49** (₹5,77,648.34 billed with GST), 42,774 installs at
₹11.44 and **19,659 registrations at ₹24.90**. `DAILY` covers 31 Jul – 10 Nov 2026; 10 Nov
is the part-day.

**The account is six weeks into a far larger regime than the one this report last
described.** `India | MBBS Students - Doctors | App Downloads | signups` has gone from
₹89,451.65 to **₹4,10,095.11** and now carries 18,432 registrations at **₹22.25** each,
against ₹32.89 at the last cut — it spent roughly four times its whole prior life in six
weeks and got cheaper doing it. It is ACTIVE on ₹4,500/day.

**A new campaign was appended as `CID` index 9**: `Qualified Leads | Campaign`
(`120250327482630482`), opened 6 Nov 2026 at 4:28 pm IST on its own ₹3,000/day, ACTIVE.
Four settled days (6–9 Nov) took ₹6,523.04 for 178 registrations, **₹36.65 each** — dearer
than the main campaign, on four days of data. Account cap is therefore ₹7,500/day.

**`India | MBBS Students - influencers` is now PAUSED** at ₹7,700.08 lifetime; it last
spent on 3 October. It was one of the two live campaigns at the last snapshot.

**Sixteen creatives were appended to `ANAME`** (indexes 8–23): the `E3-01`…`E3-10` block
plus `influencer - 20oct`, `influencer 20oct – Copy`, `influencer 27oct`,
`influencer31st oct` and `new influencer 1nov`. The first eight entries are untouched, so
every historical `ADAY` row still points at the creative that actually ran. The biggest
single ad on the account is now **`E3-08a 3 medical apps to 1`** — ₹1,26,819.57, 13,025
installs, 6,053 registrations at ₹20.95 — which has overtaken `v5 - I am a med student`
(₹54,351.01, now ARCHIVED). The best cost per registration among the big spenders is
`influencer 20oct – Copy` at **₹15.99**.

Three drifts, all Meta's own, all deliberately left in the data rather than forced:

- **`AGE` runs slightly ahead of `CAMP` on the two live campaigns** — +₹11.79 and +6
  installs on the main campaign, +₹8.40 and +1 install on Qualified Leads. Breakdown rows
  settle on their own schedule; every one of the eight paused campaigns reconciles to the
  paisa across `CAMP`, `ADS`, `DAILY` and `AGE`.
- **`ADAY` and `DAILY` disagree by ₹0.06 and ₹0.04 on 9 November**, the day before the
  snapshot, with every count agreeing exactly. That is Meta still revising inside its ~48h
  window between a campaign-level read and an ad-level read. The refresh assertion now
  allows ≤₹0.10 of spend drift on the snapshot day minus one and nothing anywhere else.
- **Meta revised 22 September up by one registration** on the main campaign after the last
  snapshot was published. `DAILY` was re-pulled in full and caught it; the carried-over
  `ADAY` rows had not, so `v5 - I am a med student` on 09-22 was corrected from 26 to 27.
  Re-pull a day at ad level before assuming a stale `ADAY` row is still right.

**One ad status was wrong in the previous snapshot and is fixed here.**
`influencer direct video ad - Rheumatoid Arthritis` in the main campaign was carrying the
synthetic `REPLACED` tag, which the page renders as "Creative replaced 22 Sep". It is the
*post*-swap half of that split and must carry Meta's real status, which is now `ARCHIVED`.
Only the pre-swap half (`v4 - Ecg`, and `v1 - male` for the other swap) gets `REPLACED`.

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

**India | MBBS Students - influencers** (`120249585873390482`) opened on 24 Sep 2026 on
its own ₹3,000/day budget alongside the original ₹3,000/day campaign. Account spend roughly
doubled that day — that is a deliberate second campaign, not a budget change on the first.
It runs the same three influencer creatives, only `Influencer - 11sep` ever had real
delivery in it, and it has been PAUSED since it stopped spending on 3 Oct 2026.

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
account, PAUSED, and still has **never delivered** as of 10 Nov 2026: Meta returns no spend, impression or
result fields for it at all. So it is deliberately left out of `CID` and `CAMP`. Add it
the day it spends, **appended to the end of `CID`** so no historical row re-labels itself.
