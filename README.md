# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 25 October 2026, 8:40 am IST.
  Self-contained: the figures are baked into the file, so it needs no network and no connector.
  Campaign rail, per-ad tables, day-by-day, age bands, and the GST-inclusive billing line.
  Defaults to the campaigns that are active; a switch shows all nine.

## Where the account stands — 25 October 2026

**The account has scaled hard and is now effectively one campaign.** Lifetime media spend is
**₹3,89,864.38** (₹4,60,039.97 billed with GST) for **33,107 installs and 14,930
registrations**, ₹26.11 each. It has spent **₹2,27,994.18 in the twenty-six days since the
last snapshot** — more than in the whole two months before it — and 81% of lifetime spend
now sits in one campaign, `India | MBBS Students - Doctors | App Downloads | signups`
(₹3,17,303.73, 13,895 registrations, **₹22.84 each**).

Daily spend ran at ₹11,000–₹14,000 from 4 to 19 October, peaking at **₹14,846.11 on the
4th**, against the ₹2,000/day cap that was in force in late September. Whoever lifted that
cap did not log it in the activity log; the budget note below describes the old regime and
is kept for the record.

**Eleven new creatives, the `E3` series, went live on 3 October**, and two more
(`influencer - 20oct`, `influencer 20oct – Copy`) on the 20th. Between them they have taken
₹2,22,393.56 — 57% of lifetime account spend — and they are what the scale-up is made of:

| Creative | Spend | Registrations | Cost per registration |
|---|---|---|---|
| `E3-08a 3 medical apps to 1` | ₹1,19,086.22 | 5,718 | ₹20.83 |
| `E3-09 Asking medicos which AI app they trust` | ₹52,326.38 | 2,388 | ₹21.91 |
| `E3-10 Rounds with the strictest senior` | ₹28,758.33 | 1,268 | ₹22.68 |
| `influencer 20oct – Copy` | ₹19,047.89 | 1,550 | **₹12.29** |
| `v5 - I am a med student` (now archived) | ₹54,351.01 | 1,734 | ₹31.34 |

`influencer 20oct – Copy` is the cheapest registration the account has ever bought and has
five days of data. `E3-08a` is the workhorse. The other eight `E3` creatives have taken
₹2,543.40 between them — about 1% of the series — and have not been given a real test.

**`India | MBBS Students - influencers` is finished.** It last spent on 3 October and is
PAUSED at ₹7,700.08 for 160 registrations (₹48.13 each) — roughly twice the cost of the
main campaign. Its `ADAY` rows stop where its delivery did.

**Watch the tail.** Spend fell to ₹1,560.94 on 24 October from ₹4,642.67 on the 23rd, and
nothing at all had been billed on the 25th when this snapshot was taken (6 installs and 12
registrations came in on late attribution). That is either a deliberate wind-down or a
balance/cap problem, and the activity log says nothing either way.

**No rename or creative swap happened in this period.** Every name in `ANAME` 0–7 still
resolves: the two that no longer appear in Meta's ad list, `v4 - Ecg` and
`v1 - male ( asking medicos how they study)`, are the already-split `REPLACED` halves of the
22 September swap documented below, not new disappearances. Thirteen names were **appended**
to `ANAME`; none was reordered.

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

**The paragraphs below describe the late-September regime and are kept for the record — the
budgets they refer to were lifted at some point in early October.**

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

`SNAP_DAY` is `2026-10-25` and the snapshot stamp reads 25 Oct 2026, 8:40 am IST in
**four** places in `index.html` (the head comment, the masthead stamp, the footnote and the
data-block comment) and once here.

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

**How the split is maintained on each refresh.** Do not carry the published halves forward
by hand. Re-pull the ad **ranged from the swap day** (`time_range` since 2026-09-23 for both
of these) — that is the live creative's row, including its own de-duplicated reach — and set
the `REPLACED` row to *lifetime minus ranged* on spend, impressions, clicks, link clicks,
installs and registrations, keeping its reach frozen at the value from its own ranged pull
when the window closed. Meta revises both halves: on the 25 Oct refresh the Rheumatoid
Arthritis ad's lifetime rose ₹3.97 while its ranged half fell ₹2.66, so the retired half
absorbed ₹6.63. Subtracting keeps the two rows summing to the ad's lifetime, which is what
the campaign reconciliation checks.

## Campaigns not in the report

`India | Influencer Videos | App Registrations` (`120249585723440482`) is on the
account, PAUSED, and has **never delivered**: Meta returns no spend, impression or
result fields for it at all. So it is deliberately left out of `CID` and `CAMP`. Add it
the day it spends, **appended to the end of `CID`** so no historical row re-labels itself.
