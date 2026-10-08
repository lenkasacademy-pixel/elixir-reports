# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 8 October 2026, 8:15 am IST.
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
own ₹3,000/day budget alongside the original ₹3,000/day campaign — account spend roughly
doubled that day, a deliberate second campaign rather than a budget change on the first. It
ran the same three influencer creatives and only `Influencer - 11sep` ever had real delivery
in it. **It was switched off at 12:15 pm on 3 Oct 2026 and is now PAUSED**, closing at
₹7,700.08 for 160 registrations. `India | MBBS Students - Doctors | App Downloads | signups`
(`120249183882130482`) is the only campaign still spending.

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

## Elixir 3.0: eleven new creatives on 3 Oct 2026

Eleven ads named `E3-01` … `E3-10` (with both `E3-08a` and `E3-08b`) were created through the
ads MCP server at 11:58–12:00 on 3 Oct 2026, cleared review the same afternoon and began
delivering between 12:02 and 3:14 pm. They are appended to `ANAME` at indexes 8–18 and to
`ADS`; cover frames are already in `ad-thumbs/`. `Influencer - 11sep` was paused in the main
campaign at 12:14 pm the same day, just before they started.

The budget moved with them, all on campaign `120249183882130482`:

| When | Daily budget |
|---|---|
| 27 Sep 7:36 am | ₹3,000 → ₹1,000 |
| 3 Oct 11:07 am (ads MCP server) | ₹1,000 → ₹8,000 |
| 4 Oct 12:10 pm (Ads Manager iOS) | ₹8,000 → ₹12,000 |

At 8:01 pm on 3 Oct the ad set `120249183882120482` also dropped its ₹30.00 bid cap
("Optimize bid for social") and returned to "Automatically bid for actions". **Anything from
3 Oct onward is a third regime** — do not read it against either the ₹6,000/day days or the
₹2,000/day days.

Prepaid balance top-ups: ₹30,000 on 28 Sep 9:32 pm, ₹30,000 on 4 Oct 6:48 pm, ₹70,000 on
7 Oct 6:04 pm. Keep watching the balance — a stall still looks like a cheap day in the tables.

**The new creatives are the cheapest registrations the account has bought:** ₹19.03 each
across 3–7 Oct, against ₹31.72 over 28 Sep–2 Oct and ₹33.06 lifetime. `E3-08a 3 medical apps
to 1` carries most of it alone (₹29,352.78 / 1,591 registrations / ₹18.45).

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

**On every later refresh the old name is simply gone.** Ad-level reads return one row per ad
id under the *new* name carrying the whole history, so `v4 - Ecg` and `v1 - male` no longer
appear at all. Do not read that as a deleted ad: the tell is that the split pair still sums
to Meta's merged row to the paisa (verified again on 8 Oct 2026 — 10361.32 + 96.01 =
10457.33, and 1076.22 + 413.03 = 1489.25). Keep the `REPLACED` rows exactly as published and
pull the new creative's own figures with a ranged read from the swap date (22 Sep), which is
also the only way to get its de-duplicated reach.

## Campaigns not in the report

`India | Influencer Videos | App Registrations` (`120249585723440482`) is on the
account, PAUSED, and has **never delivered**: Meta returns no spend, impression or
result fields for it at all. So it is deliberately left out of `CID` and `CAMP`. Add it
the day it spends, **appended to the end of `CID`** so no historical row re-labels itself.
