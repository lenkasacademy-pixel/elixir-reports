# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 20 October 2026, 8:15 am IST.
  Self-contained: the figures are baked into the file, so it needs no network and no connector.
  Campaign rail, per-ad tables, day-by-day, age bands, and the GST-inclusive billing line.
  Defaults to the campaigns that are active; a switch shows all nine.

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

**India | MBBS Students - influencers** (`120249585873390482`) opened on 24 Sep 2026 on its
own ₹3,000/day budget alongside the original campaign, which is why account spend roughly
doubled that day. It spent its last rupee on **3 Oct 2026** and now reads PAUSED, closing at
₹7,700.08 for 160 registrations. Only `Influencer - 11sep` ever had real delivery in it.
**`India | MBBS Students - Doctors | App Downloads | signups` is now the only live campaign
on the account.**

## October 2026: Elixir 3.0, and a 12x budget

**Budgets were raised twice in two days** (activity log, actor Abhijeet Lenka): ₹1,000 →
₹8,000/day at 11:07 am on 3 Oct (via `ads MCP server`), then ₹8,000 → ₹12,000/day at
12:10 pm on 4 Oct (Ads Manager App for iOS). Daily account spend went from about ₹1,100 to
about ₹12,000. The prepaid balance has been topped up ₹70,000 at a time (7, 12 and 17 Oct)
— keep watching it, because a stall still looks like a cheap day in the tables.

**Ten new creatives, `E3-01` to `E3-10`, went live on 3 Oct** (the `ad-thumbs/elixir3/`
covers belong to these). They are cheaper as well as bigger: 4–19 Oct took 8,923
registrations on ₹1,93,644.84, **₹21.70 each**, against ₹41.89 over 23–28 Sep under the old
₹1,000 cap. `E3-08a 3 medical apps to 1` is the largest ad on the account (₹1,15,856.78) and
the cheapest at scale (₹21.26); `E3-09` ₹22.32 and `E3-10` ₹22.97. `E3-06` was archived
after ₹107.70.

**`v5 - I am a med student` has been ARCHIVED** at ₹54,351.01 / 1,734 registrations
(₹31.34). It is kept in `ADS` because it still carries spend.
**Archived ads are not returned by an unfiltered ad-level listing** — the first pull of this
refresh came up ₹64,919.67 short of the campaign total until `effective_status IN
["ARCHIVED","DELETED"]` was added. Pull both, every refresh, exactly as `halcyon-reports`
does for campaigns. Archived ads with no spend are still left out of `ADS`.

**A PAUSED ad can still have spent today.** On 20 Oct `E3-08a`, `E3-09` and `E3-10` all read
PAUSED yet all three booked spend that morning, and a new ad `influencer - 20oct` started the
same day. Re-pull the days; never infer a stop from a status.

## September 2026, for the record (superseded by the October regime above)

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
**₹19.00 bid cap**. Anything from 27 Sep onward was a different regime; do not read it
against the ₹6,000/day days — and from 3 Oct the October budgets make it a third one. `influencer - hasaan` was switched off in the main campaign
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
