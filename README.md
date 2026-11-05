# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 5 November 2026, 8:11 am IST.
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

## Snapshot: 5 November 2026 — the account has changed scale

**Lifetime spend has tripled since the last cut and the cost per registration has
almost halved.**

| | 29 Sep cut | **5 Nov cut** |
|---|---|---|
| Media spend | ₹1,61,870.20 | **₹4,56,975.37** |
| Billed (incl. 18% GST) | ₹1,91,006.84 | **₹5,39,230.94** |
| Installs | 9,509 | **40,120** · ₹11.39 each |
| Registrations | 3,741 | **18,509** · **₹24.69** each |
| Lifetime cost per registration | ₹43.27 | **₹24.69** |

**It is one campaign.** `India | MBBS Students - Doctors | App Downloads | signups`
(index 5) went from ₹89,451.65 to **₹3,84,414.72** — 84% of all spend the account has
ever made — and its registrations went 2,720 → **17,474** at **₹22.00** each, down from
₹32.89. Its daily budget is back at **₹6,000/day**; the ₹1,000/day cut of 27 Sep did not
hold. It is the only ACTIVE campaign on the account.

**`India | MBBS Students - influencers` (index 8) is now PAUSED** and spent only ₹142.10
more than at the last cut (₹7,557.98 → ₹7,700.08). The second-campaign experiment is over;
everything now runs inside campaign 5.

**Sixteen new creatives, and the good ones are the influencer cuts:**

| creative | spend | regs | each |
|---|---|---|---|
| `influencer 20oct – Copy` (ACTIVE) | ₹50,629.39 | 3,294 | **₹15.37** |
| `new influencer 1nov` (ACTIVE) | ₹19,539.38 | 1,169 | **₹16.71** |
| `E3-08a 3 medical apps to 1` | ₹1,26,819.57 | 6,053 | ₹20.95 |
| `E3-09 Asking medicos which AI app they trust` | ₹53,243.63 | 2,451 | ₹21.72 |
| `E3-10 Rounds with the strictest senior` (ACTIVE) | ₹31,011.26 | 1,373 | ₹22.59 |
| `v5 - I am a med student` (ARCHIVED) | ₹54,351.01 | 1,734 | ₹31.34 |
| `Influencer - 11sep` | ₹20,817.14 | 569 | ₹36.59 |

`E3-08a` is the biggest single line the account has ever run and it is respectable at
₹20.95, but **the two influencer cuts beat it by 25–30%** on a fraction of the budget.
The old `v5 - I am a med student` workhorse is archived at ₹31.34 — it was the most
expensive large creative by the end.

**The window card reads a 25% improvement and names the cause.** A registration cost
₹23.26 across 28–31 Oct and **₹17.51 across 1–4 Nov**. Impressions were 15% *dearer* over
those windows, but each 1,000 of them produced **52% more registrations** — a response
gain, not a price gain. The mix is the reason: `new influencer 1nov` took **0% of spend in
the first window and 78% in the second**. The page works all of this out from `ADAY`.

**No rename or creative swap this time.** `ads_account_get_activity_logs`
(`event_category: "ad"`, since 29 Sep) returns 176 events: 110 status changes, 50 review
transitions and **16 `Ad created`** — exactly matching the 16 new names — with **zero
`Ad name updated` and zero `Ad updated`**. Every one of the 16 is a genuinely new ad
object, so all 16 were **appended** to `ANAME` (8 → 24 entries) and no existing index
moved.

**The two `REPLACED` rows still reproduce exactly.** A ranged pull to **21 September**
returns `v4 - Ecg` at ₹10,361.32 / 303,917 / 159,924 and `v1 - male` at ₹1,076.22 /
23,969 / 16,423 — identical to the published rows, to the paisa. The 21/22 September
boundary the page documents is the right one; an off-by-one boundary (22/23) moves
₹6.63 and ₹14.39 between the halves, so **use 21 September, not the detection date of
the 23rd.**

**One inherited 1-registration gap is closed.** `CAMP[0]` read 184 registrations while its
`AGE` rows summed to 183. Re-pulling campaign 0's age breakdown shows Meta has since
late-attributed one registration to its 25–34 band (72 → 73), and the two now agree.

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
