# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 3 October 2026, 8:10 am IST.
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

A second live campaign, **India | MBBS Students - influencers** (`120249585873390482`),
opened on 24 Sep 2026 on its own ₹3,000/day budget alongside the original ₹3,000/day
campaign. Account spend roughly doubled that day — that is a deliberate second campaign,
not a budget change on the first. It runs the same three influencer creatives, and so far
only `Influencer - 11sep` has any real delivery in it.

**Budgets were cut on 27 Sep 2026 at 7:36 am** — both campaigns went from ₹3,000/day to
₹1,000/day (activity log, Power Editor, actor Abhijeet Lenka), so the account cap fell
from ₹6,000/day to ₹2,000/day. **Five settled days later the account is running at about
₹1,140 a day, well under the cap**, because the influencer campaign has all but stopped
delivering: ₹2,902.25 on the 26th, then ₹478.36, ₹22.73, ₹56.17, ₹58.57, ₹34.80 and
₹3.54 on 2 October. Almost everything the account now spends is the signups campaign.

**Cost per registration since the cut is noisy, not directional**: ₹30.30 on the 27th,
₹22.98, ₹28.69, ₹44.04 on the 30th, ₹23.82 on 1 October, ₹30.01 on the 2nd. The settled
nine days of the current window come to ₹24,968.57 for 674 registrations, **₹37.05
each**. Read the window total, not any single day — which is exactly what the rollup at
the top of the all-campaigns view does.

**Two days in the last snapshot were wrong to read, and both have now settled.** The 28th
was published at ₹709.48 and settled at ₹712.22 — the funding stall was real (delivery
all but stopped from 3 pm to 9 pm and resumed after ₹30,000 was added to the prepaid
balance at 9:32 pm), but the day was not as cheap as it looked: 31 registrations at
₹22.98, not ₹22.89. The 29th was published as a ₹363.16 part-day and settled at
₹946.93 for 33 registrations. That is the part-day guard earning its keep: had the 29th
set a headline it would have read ₹11.01 a registration.

At 7:34 am on 27 Sep the influencer ad set `120249585873460482` also moved from
"Automatically bid for actions" to "Optimize bid for actions" with a **₹19.00 bid cap**.
Anything from 27 Sep onward is a different regime; do not read it against the
₹6,000/day days. `influencer - hasaan` was switched off in the main campaign at 9:25 pm
on 26 Sep and is PAUSED in `ADS`.

**No ad was renamed between the 29 Sep and 3 Oct snapshots** — every name in the
ad-level read matched `ANAME`, and `ANAME` is unchanged and unreordered.

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
