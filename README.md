# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 10 October 2026, 8:05 am IST.
  Self-contained: the figures are baked into the file, so it needs no network and no connector.
  Campaign rail, per-ad tables, day-by-day, age bands, and the GST-inclusive billing line.
  Defaults to the campaigns that are active; a switch shows all nine.
  **Only one campaign is live now** — `India | MBBS Students - Doctors | App Downloads | signups`.

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

**October: the account was scaled up hard, and got cheaper doing it.**
The live campaign's daily budget went **₹1,000 → ₹8,000 on 3 Oct at 11:07 am**
(activity log, ads MCP server) and **₹8,000 → ₹12,000 on 4 Oct at 12:10 pm**
(Ads Manager App for iOS), with ₹30,000 added to the prepaid balance on the 4th
and ₹70,000 on the 7th. Daily spend went from about ₹1,100 to ₹8,600–₹14,800,
and **cost per registration fell**: ₹17.45 (4th), ₹18.54, ₹18.98, ₹18.48,
₹19.67, ₹22.22 (9th), against the ₹30.30 of 27 Sep that had been the benchmark.
The last ten settled days took ₹80,007.41 for 4,014 registrations — **₹19.93
each**. Anything from 4 Oct onward is a third regime; do not read it against
either the ₹6,000/day days or the ₹2,000/day days.

**Eleven new creatives did it — the `E3-` series.** All eleven were *created*
on 3 Oct 2026 between 11:56 am and 12:00 pm and went live that afternoon
(`ads_account_get_activity_logs`, `event_category: "ad"`, all `Ad created`, no
`Ad name updated` and no `update_ad_creative` anywhere in the window — so they
are genuinely new creatives, **appended** to `ANAME` at indexes 8–18, and no
historical `ADAY` row re-labels itself). Three of them now carry the account:

| Ad | Spend | Registrations | Cost/reg |
|---|---|---|---|
| `E3-08a 3 medical apps to 1` | ₹45,850.91 | 2,390 | **₹19.18** |
| `E3-09 Asking medicos which AI app they trust` | ₹17,928.13 | 916 | ₹19.57 |
| `E3-10 Rounds with the strictest senior` | ₹12,034.85 | 600 | ₹20.06 |
| `v5 - I am a med student` (previous best) | ₹54,339.92 | 1,732 | ₹31.37 |

`E3-08a` is already the second-biggest ad on the account. The other eight E3 ads
are between ₹66 and ₹747 lifetime and have barely delivered — they are in `ADS`
but should not be judged yet.

**`India | MBBS Students - influencers` (`120249585873390482`) is finished.**
It closed PAUSED at ₹7,700.08 for 160 registrations, **₹48.13 each** — roughly
twice the live campaign's rate. `Influencer - 11sep` was also paused inside the
live campaign on 3 Oct at 12:14 pm.

**Earlier regimes, kept for context.** A second campaign opened on 24 Sep 2026
on its own ₹3,000/day alongside the original ₹3,000/day, which is why account
spend roughly doubled that day. **Budgets were cut on 27 Sep 2026 at 7:36 am** —
both campaigns from ₹3,000 to ₹1,000/day (activity log, Power Editor, actor
Abhijeet Lenka) — and 27 Sep settled at ₹1,939.44 for 64 registrations, ₹30.30
each. **28 Sep is still not a fair comparison**: it settled at ₹712.22 for 31
registrations (₹22.97) because delivery thinned all day and all but stopped from
3 pm to 9 pm, resuming only after ₹30,000 was added to the balance at 9:32 pm.
At 7:34 am on the 27th the influencer ad set `120249585873460482` also moved to
"Optimize bid for actions" with a **₹19.00 bid cap**. `influencer - hasaan` was
switched off in the main campaign at 9:25 pm on 26 Sep and is PAUSED in `ADS`.

## Drift this refresh is Meta's own, and it is small

Everything reconciles except four count differences that come from Meta
reporting a lifetime total slightly above (or below) the sum of its own
breakdown of that same total. None is a missed row — all the spend reconciles
exactly — so they are carried rather than forced:

| Check | Breakdown | Meta's total |
|---|---|---|
| campaign 5, `DAILY` registrations | 6,826 | 6,827 |
| campaign 5, `AGE` installs | 16,497 | 16,494 |
| campaign 5, `AGE` registrations | 6,829 | 6,827 |
| campaign 0, `AGE` registrations | 183 | 184 |

The campaign-0 one is pre-existing and unchanged; that campaign has been paused
for weeks. Assert spend to the paisa and allow a handful of counts, but **print
them** — a count drift of more than about three would be a real error.

## A zero-spend ad row can still carry a result — do not filter it out

Pulling ad-level days with `filtering: amount_spent > 0` **silently loses rows
that spent nothing but were still credited with a late install or
registration**, and that breaks the `sum(ADAY) == DAILY` assertion by one.
It bit this refresh twice: `influencer - hasaan` on 28 Sep and
`Influencer - 11sep` on 8 Oct each had ₹0.00 spend and one install. Pull
ad-level daily rows **unfiltered** and drop the empty ones locally, the way
`o2-reports` already does for campaigns.

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
