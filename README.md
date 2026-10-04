# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 4 October 2026, 7:00 am IST.
  Self-contained: the figures are baked into the file, so it needs no network and no connector.
  Campaign rail, per-ad tables, day-by-day, age bands, and the GST-inclusive billing line.
  Defaults to the campaigns that are active — **just one now** — and a switch shows all nine.

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

## The account changed gear on 3 October 2026

Three things happened within an hour, all in the activity log, actor Abhijeet Lenka:

- **11:07 am — the main campaign's daily budget went from ₹1,000 to ₹8,000**
  (`Campaign budget updated`, 100000 → 800000 paise, "Per day"). An eightfold
  raise. The ad set also moved to "Optimize bid for actions" with a ₹30.00 cap at
  11:07 am, and then back to "Automatically bid for actions" at 8:01 pm the same
  day, so the cap lasted nine hours and is not in force now.
- **11:56 am – 12:00 pm — eleven new ads were created** and pushed through review,
  the **E3 series**: `E3-01` … `E3-07`, `E3-08a`, `E3-08b`, `E3-09`, `E3-10`. All
  went live that afternoon. They are appended to the end of `ANAME` (indexes
  8–18); nothing was reordered.
- **12:14 pm — `Influencer - 11sep` was switched off in the main campaign**, and
  **12:15 pm — the second campaign `India | MBBS Students - influencers`
  (`120249585873390482`) was paused outright.** It had already dwindled to a few
  rupees a day. It closes at ₹7,700.08 for 386 installs and 160 registrations,
  **₹48.13 a registration** — well above the account average.

**So one campaign is live now, not two, and it is running new creative on eight
times the budget.** Anything from 3 October onward is a third regime; do not read
it against either the ₹6,000/day days or the ₹2,000/day days that followed.

**Day one of the new regime looks good, and it is the new creative doing it.**
3 October took ₹4,619.67 for 140 registrations, **₹33.00 each**, against ₹30.12 on
the 2nd at a quarter of the spend — so an eightfold budget raise cost about 10% on
efficiency, which is cheap. Within that day:

| 3 Oct | spend | registrations | each |
|---|---|---|---|
| the eleven new E3 ads | ₹3,459.72 | 113 | **₹30.62** |
| the five older creatives | ₹1,157.44 | 27 | ₹42.87 |

Three of the eleven carry almost all of it — `E3-09 Asking medicos which AI app
they trust` (₹1,616.70, 59 registrations, ₹27.40), `E3-10 Rounds with the
strictest senior` (₹688.55, 23, ₹29.94) and `E3-08a 3 medical apps to 1`
(₹656.11, 24, ₹27.34). The other eight took ₹498.36 between them for 7
registrations. One day is one day, but the ranking is worth watching rather than
assuming the whole series works.

**4 October is a part-day and is excluded from every headline.** It is also the
clearest proof yet of why: **it moved by ₹175.43 between two connector calls
minutes apart** while this snapshot was being built (₹3,280.29 → ₹3,455.72,
installs 384 → 426, registrations 119 → 132). Never let that day set a number.

## The earlier regimes, for reading older rows

A second campaign, **India | MBBS Students - influencers**
(`120249585873390482`), opened on 24 Sep 2026 on its own ₹3,000/day budget
alongside the original ₹3,000/day campaign — which is why account spend roughly
doubled that day. It ran the same three influencer creatives and only
`Influencer - 11sep` ever had real delivery in it. It was paused on 3 Oct.

**Budgets were cut on 27 Sep 2026 at 7:36 am** — both campaigns went from
₹3,000/day to ₹1,000/day, so the account cap fell from ₹6,000/day to ₹2,000/day.
The first day under the cap was the cheapest normal day in a fortnight: 27 Sep
settled at ₹1,939.44 for 64 registrations, ₹30.30 each, against ₹41.74 on the 26th
at three times the spend. **28 Sep is not a fair comparison**: it settled at only
₹712.22 (31 registrations, ₹22.97 each) because delivery thinned all day and all
but stopped from 3 pm to 9 pm, resuming only after ₹30,000 was added to the
prepaid balance at 9:32 pm. Watch the balance: a stall like that looks like a
cheap day in the tables. `influencer - hasaan` was switched off in the main
campaign at 9:25 pm on 26 Sep and is PAUSED in `ADS`.

The settled days either side of the last snapshot: 29 Sep ₹946.93 / 33
registrations / ₹28.69, 30 Sep ₹1,145.17 / 26 / ₹44.05, 1 Oct ₹1,143.27 / 48 /
₹23.82, 2 Oct ₹1,144.37 / 38 / ₹30.12. Meta also revised 28 Sep up by ₹2.74 after
publication, and the 29th — published as a ₹363.16 part-day — settled at ₹946.93.

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
