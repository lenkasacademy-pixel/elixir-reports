# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 15 October 2026, 8:05 am IST.
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

**The account was scaled roughly twelvefold in the first week of October, and it bought
registrations more cheaply, not less cheaply.** `India | MBBS Students - Doctors | App
Downloads | signups` (`120249183882130482`) is now the only campaign spending. Its daily
budget went **₹1,000 → ₹8,000 at 11:07 am on 3 Oct** (activity log, ads MCP server) and
**₹8,000 → ₹12,000 at 12:10 pm on the 4th** (Ads Manager App for iOS, actor Abhijeet
Lenka). ₹30,000 went onto the prepaid balance on the 4th and ₹70,000 more on the 7th and
again on the 12th. Daily account spend went from about ₹1,100 to ₹8,600–₹14,800.

| window | spend | registrations | each |
|---|---|---|---|
| 27 Sep (benchmark under the ₹1,000 cap) | ₹1,939.44 | 64 | ₹30.30 |
| 5–14 Oct (ten settled days) | ₹1,19,511.21 | 5,771 | **₹20.71** |

**But the rate is drifting the wrong way inside that window** — ₹18.54 on the 5th,
₹18.98, ₹18.36, ₹19.64, ₹22.21, ₹21.61, ₹20.51, ₹22.01, ₹21.27, ₹24.39 on the 14th.
Read it as a scale-up that is still cheaper than September and starting to cost more,
not as a settled win.

**Ten new creatives were created at 11:58 am on 3 Oct** — `E3-01` to `E3-10`, with `E3-08`
split into `E3-08a` and `E3-08b`, so eleven new names were **appended** to `ANAME`
(indexes 8–18). They now carry the campaign: `E3-08a 3 medical apps to 1` is the largest
ad on the account at **₹81,485 and ₹20.02 a registration**, ahead of `E3-09` (₹20.59) and
`E3-10` (₹21.68).

**Two ads were deleted at 7:39 am on 15 Oct, twenty-six minutes before this snapshot** —
`v5 - I am a med student` (₹54,351.01, 1,734 registrations, ₹31.34 each) and
`E3-06 My entire AI stack`. **A deleted ad stays in the ledger**, marked `ARCHIVED`,
because the money was spent; Meta still returns its lifetime figures, but **only if the
read includes deleted and archived ads** — an ordinary ad-level pull drops them silently
and the campaign total then exceeds the sum of its ad rows by whatever they spent. That is
what the `CAMP` vs `ADS` assertion is for.

**`India | MBBS Students - influencers`** (`120249585873390482`) opened on 24 Sep on its
own ₹3,000/day, was cut to ₹1,000/day with the main campaign on 27 Sep, last spent on
4 Oct and now reads **PAUSED**, closing at ₹7,700.08 for 160 registrations (₹48.13 each).
`Influencer - 11sep` was the only ad in it with real delivery.

**The 27 Sep budget cut** took both campaigns from ₹3,000/day to ₹1,000/day at 7:36 am
(activity log, Power Editor, actor Abhijeet Lenka), and the 27th settled as the cheapest
normal day in a fortnight at ₹30.30 a registration. **28 Sep was never a fair
comparison**: it spent only ₹689.49 because delivery thinned all day and all but stopped
from 3 pm to 9 pm, resuming only after ₹30,000 was added to the balance at 9:32 pm. Watch
the balance — a stall like that looks like a cheap day in the tables. At 7:34 am on the
27th the influencer ad set `120249585873460482` also moved from "Automatically bid for
actions" to "Optimize bid for actions" with a **₹19.00 bid cap**. Anything from 3 Oct
onward is a different regime again; do not read it against the ₹1,000/day days or the
₹6,000/day days before them. `influencer - hasaan` was switched off in the main campaign
at 9:25 pm on 26 Sep and is PAUSED in `ADS`.

## Drift this refresh — all of it Meta's, all of it inside tolerance

The assertions pass, but they are not all exact and it is worth knowing which:

| check | gap | on a base of |
|---|---|---|
| `camp 5` `ADS` installs | +2 | 22,166 |
| `camp 5` `DAILY` installs | +6 | 22,166 |
| `camp 5` `AGE` spend / imps / installs / regs | +₹25.06 / +405 / +6 / +1 | ₹2,34,059.30 |
| `ADAY` vs `DAILY`, 14 Oct, camp 5 | +₹0.07 / +3 imps | ₹11,535.85 |
| `camp 0` `AGE` registrations | −1 | 184 (**pre-existing**, camp 0 has not spent since August) |

Spend reconciles **to the paisa** on `ADS` and `DAILY` for every campaign, and installs
and registrations reconcile **exactly** between `ADAY` and `DAILY` on every settled day.
The gaps above are Meta disagreeing with itself between aggregation levels; the `camp 0`
one was already in the published file. Anything materially larger than these is a real
error — the builder fails on more than ₹30 of spend, 500 impressions, 10 installs or
5 registrations per campaign, and on **any** install or registration gap between `ADAY`
and `DAILY` on a settled day.

The page carries `noindex, nofollow` so it stays out of search results.

A live version of the same report — one that queries Meta each time it is opened — is kept
as a private Claude artifact rather than here.

## A rename can hide a creative swap — check the activity log

**Checked on this refresh: no renames.** `ads_account_get_activity_logs` with
`event_category: "ad"` over 28 Sep – 15 Oct returns **no `Ad name updated` event at
all**. Two ad names did disappear from the ad-level read — `v5 - I am a med student` and
`E3-06 My entire AI stack` — but the log shows both as `Active → Deleted` at 7:39 am on
15 Oct, not renamed, and re-reading them by id with deleted/archived included returns
their full lifetime figures. A disappearing name is not automatically a rename: check
for a delete before you go looking for the new name.

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
