# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 22 November 2026, 8:15 am IST.
  Self-contained: the figures are baked into the file, so it needs no network and no connector.
  Campaign rail, per-ad tables, day-by-day, age bands, and the GST-inclusive billing line.
  Defaults to the campaigns that are active; a switch shows all eleven.

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

## Where the account stands at this snapshot

**The account has spent ₹572,778.21 in all for 22,437 registrations, ₹25.53 each.** At the
September snapshot it had spent ₹161,870.20 for 3,741 registrations at ₹43.27. The
₹410,908.01 spent in the 54 days since is more than two and a half times everything that came
before it, and the cost per registration came down by 41% while that happened.

**Eleven campaigns are in the report now, two of them new since September.** `CID` was appended, never
reordered: index 9 is `Qualified Leads | Campaign` (`120250327482630482`) and index 10 is
`Qualified Leads | AI Videos | 21 Nov` (`120250608611250482`).

- **`India | MBBS Students - Doctors | App Downloads | signups` is paused and finished.** It last
  delivered on **15 November** and closed at ₹425,427.74 / 18,968 registrations
  (₹22.43 each) — still three quarters of everything the account has ever spent. Its daily
  budget went ₹1,000 → ₹8,000 on 3 Oct, → ₹12,000 on 4 Oct, back to ₹6,000
  on 20 Oct, then ₹4,500 on 9 Nov and ₹3,500 on 10 Nov (activity log, actor Abhijeet
  Lenka). Eleven `E3-*` creatives were added to it on 3 October and `E3-08a 3 medical apps to 1`
  (₹126,819.57) is now the biggest-spending ad on the account.
- **`Qualified Leads | Campaign` is the only thing running.** Opened 6 November, raised from
  ₹2,000 a day to ₹8,000 in five steps between 9 and 18 November, and it is the campaign
  whose figures move on from this snapshot. `influencer3 nov` carries 64% of its lifetime spend and
  about 92% of the last week's.
- **`Qualified Leads | AI Videos | 21 Nov` ran one day and was switched off** at 7:39 am on 22 Nov:
  four AI-made reels on their own ₹3,000 a day took ₹1,905.74 for 11 registrations,
  ₹173.25 each, against ₹25.53 for the account. Do not read one day as a verdict, but it
  is the most expensive result the account has bought.
- **`India | MBBS Students - influencers` is paused**, last delivery 3 October, ₹7,700.08.

**14 November is a blank day and it is not a mistake.** The account spent nothing at all, and still
booked 13 installs and 31 registrations from the clicks of the days before it, so its cost per
result reads as ₹0.00. It is in `DAILY` with zero spend and zero impressions on purpose — a
day's results belong to the day Meta attributes them to, and dropping the row would lose 31
registrations out of the campaign total. This is exactly the case both day-table guards exist for.

**A `DAILY` or `ADAY` row can carry results with no spend and no impressions.** Rows are kept when
*any* of spend, impressions, clicks, installs or registrations is non-zero. Filtering on spend alone
loses late-attributed conversions and the campaign totals stop reconciling; campaign 5 came up 19
installs and 26 registrations short the one time it was done that way.

## History: the September regime

**Budgets were cut on 27 Sep 2026 at 7:36 am** — both campaigns then running went from
₹3,000/day to ₹1,000/day (activity log, Power Editor, actor Abhijeet Lenka), so the
account cap fell from ₹6,000/day to ₹2,000/day. 27 Sep settled at ₹1,939.44 for 64
registrations, ₹30.30 each, against ₹41.74 on the 26th at three times the spend.
**28 Sep is not a fair comparison**: it spent only ₹709.48 (31 registrations, ₹22.89
each) because delivery thinned all day and all but stopped from 3 pm to 9 pm, resuming right after
₹30,000 was added to the prepaid balance at 9:32 pm. Watch the balance: a stall like that
looks like a cheap day in the tables. `India | MBBS Students - influencers`
(`120249585873390482`) had opened on 24 Sep 2026 on its own ₹3,000/day, which is why account
spend roughly doubled that day — a deliberate second campaign, not a budget change on the first.
`influencer - hasaan` was switched off in the main campaign at 9:25 pm on 26 Sep.

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

This is not a one-off correction you can make once and forget. **Meta reports every
historical ad-day row under the ad's _current_ name**, so each refresh re-reads August
and September days for `influencer - hasaan` and `influencer direct video ad -
Rheumatoid Arthritis` and has to rename them back:

- ad `120249183882150482` is `v1 - male ( asking medicos how they study)` before 22 Sep 2026
- ad `120249212142170482` is `v4 - Ecg` before 22 Sep 2026

The `ADS` split works by subtraction and the ranged pull supplies only the reach, which
is what keeps `sum(ADS) == CAMP` exact: the live creative's spend, impressions, clicks,
link clicks, installs and registrations are the ad id's lifetime **minus** the frozen
`REPLACED` row, while reach comes from its own ranged pull because reach does not subtract.
Checked this refresh: the activity log holds **no** `Ad name updated` and no creative-change
event since 29 Sep 2026, so nothing else needs splitting.

## Campaigns not in the report

`India | Influencer Videos | App Registrations` (`120249585723440482`) is on the
account, PAUSED, and has **never delivered**: Meta returns no spend, impression or
result fields for it at all. Still true at 22 Nov 2026. So it is deliberately left out of
`CID` and `CAMP`. Add it the day it spends, **appended to the end of `CID`** so no
historical row re-labels itself.

Archived and deleted ads are **omitted from an unfiltered ad listing**, which silently
breaks `sum(ADS) == CAMP`. Pull ads with
`effective_status IN [ACTIVE, PAUSED, DELETED, ARCHIVED, CAMPAIGN_PAUSED, ADSET_PAUSED, ...]`.
Campaign 5 came up ₹65,551.01 short the one time this was missed.

## Reconciling before you commit

Every one of these held exactly at this snapshot, and all of them should be re-run:

- per campaign, `CAMP` spend / impressions / clicks / installs / registrations equal the
  sums of its `ADS`, `DAILY` and `AGE` rows
- `sum(ADAY) == DAILY` per campaign per settled day on spend, installs and registrations
- the `level: "ad_account"` daily series equals the sum of the campaign `DAILY` rows on
  both spend and impressions, **every day** — 117 days, ₹572,778.21, exact
- `ADS` is sorted descending by spend; `ANAME`'s first eight entries are unchanged

One caveat worth knowing: the live campaign's lifetime totals move between calls. Pull the
campaign, ad, age and day levels in **one parallel batch** or the age breakdown will miss
the campaign total by the few rupees spent in between (it read ₹47.19 short once here).
Meta's age breakdown is also revised late — campaign 0's bands were one registration short
of its total in the September snapshot and now add up.
