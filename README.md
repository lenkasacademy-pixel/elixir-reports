# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 23 October 2026, 11:00 am IST.
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

## The account was scaled up hard in early October

**Read anything before 3 October as a different regime.** The live campaign,
`India | MBBS Students - Doctors | App Downloads | signups` (`120249183882130482`),
ran at roughly ₹1,000/day to 2 October, took ₹4,617.16 on the 3rd and
**₹14,846.11 on the 4th**, and has held ₹7,000–13,500 a day since. Its lifetime
spend went **₹89,451.65 → ₹3,12,087.11** and its registrations **2,720 → 13,385**
in under four weeks. The ₹1,000/day cap imposed on 27 September is long gone, and
the budget-cut narrative that used to sit in the masthead has been replaced.

**Cost per registration improved as it scaled**, which is the unusual part:
₹32.89 lifetime at the September snapshot, **₹23.32 lifetime now**, and ₹21.62
across the last nine settled days (14–22 Oct, ₹90,325.56 for 4,178 registrations).

**A new creative batch is what carried it.** Eleven `E3-xx` ads and two
`influencer 20oct` ads launched in early October:

| Ad | Spend | Registrations | Each |
|---|---|---|---|
| `E3-08a 3 medical apps to 1` | ₹1,18,486.90 | 5,645 | ₹20.99 |
| `E3-09 Asking medicos which AI app they trust` | ₹52,215.62 | 2,372 | ₹22.01 |
| `E3-10 Rounds with the strictest senior` | ₹28,711.79 | 1,260 | ₹22.79 |
| `influencer 20oct – Copy` | ₹14,588.20 | 1,139 | **₹12.81** |
| `v5 - I am a med student` (now ARCHIVED) | ₹54,351.01 | 1,734 | ₹31.34 |

`E3-08a` is now the largest ad the account has ever run. **`influencer 20oct – Copy`
is the cheapest line of any size on the account at ₹12.81** — worth more budget
before judging it. The other eight `E3-xx` ads are all under ₹1,000 and are tests.

**The influencer campaign has stopped.** `India | MBBS Students - influencers`
(`120249585873390482`), which opened on 24 Sep 2026 on its own budget, last spent
on 3 October and now reads **PAUSED** at ₹7,700.08 for 160 registrations (₹48.13
each). It is no longer live, so its `ADAY` rows are frozen.

The 27 September budget cut, the 28 September delivery stall and the prepaid-balance
top-up that used to be described here are all now well before the current regime and
have been dropped from the page's masthead. They remain visible in the day table.

## Archived ads disappear from an unfiltered ad listing

**This is the trap that bit this refresh.** On the 23 October pull the ad rows for
the live campaign summed to ₹2,46,536.10 against a campaign total of ₹3,12,087.11
— **₹65,551.01 missing**. The gap was four ads that are **ARCHIVED**, and Meta's
ordinary ad listing does not return them:

| Archived ad | Spend |
|---|---|
| `v5 - I am a med student` | ₹54,351.01 |
| `influencer direct video ad - Rheumatoid Arthritis` | ₹10,460.96 |
| `influencer - 20oct` | ₹631.34 |
| `E3-06 My entire AI stack` | ₹107.70 |

Re-pulling with `filtering: effective_status IN ["ARCHIVED","DELETED"]` returned
exactly those four and closed the gap to the paisa. **Always run that second pull**
and reconcile ad rows against the campaign total before writing the file — this is
the same failure mode `halcyon-reports` documents at campaign level.

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

Both 22 September splits are **still in force** and were re-derived this refresh
from a ranged pull (22 Sep onward) on each ad id: `v4 - Ecg` holds ₹10,361.32 /
837 installs / 313 registrations and `influencer direct video ad - Rheumatoid
Arthritis` the ₹99.64 earned since; `v1 - male` holds ₹1,076.22 / 88 / 29 and
`influencer - hasaan` the ₹413.03 since. Each pair adds back to its ad id's
lifetime total exactly.

**No new renames this refresh.** Every name that vanished from the live campaign's
ad listing was found again in the ARCHIVED pull with its own id and history intact,
so nothing was carried forward under the wrong creative.

## Campaigns not in the report

`India | Influencer Videos | App Registrations` (`120249585723440482`) is on the
account, PAUSED, and has **never delivered**: Meta returns no spend, impression or
result fields for it at all. So it is deliberately left out of `CID` and `CAMP`. Add it
the day it spends, **appended to the end of `CID`** so no historical row re-labels itself.
Still ₹0.00 as at 23 October.

## Reconciliation notes from the 23 Oct refresh

Every campaign's `ADS`, `DAILY` and `AGE` rows were checked against its `CAMP` row,
and `ADAY` against `DAILY` per campaign per settled day on spend, installs **and**
registrations. All pass. Three sub-material drifts, all Meta's own, none corrected:

- **`AGE` for the live campaign runs ₹11.73 above its `CAMP` spend** (on ₹3.12 lakh,
  0.004%) and 346 impressions over. Registrations match exactly.
- **`DAILY` for the live campaign is 1 registration short of `CAMP`** — the two
  reads were minutes apart on a campaign that is still spending.
- **`ADAY` and `DAILY` differ by ₹0.17 on 22 October**, the day before the snapshot
  and still inside Meta's ~48h revision window. Installs and registrations match
  exactly, and every settled day before the 21st matches to the paisa.

One pre-existing discrepancy was found and **left alone**: campaign 0's `AGE` rows
sum to 183 registrations against its `CAMP` row's 184. That gap is in the figures as
already published, campaign 0 has not spent since August, and nothing about it moved
this refresh.

`ad-thumbs/elixir3/` holds `reel1.jpg` … `reel10_ad.jpg`, which match the new
`E3-01` … `E3-10` creatives by number. **Nothing in `index.html` references them
yet** — if the client wants creative thumbnails in the ad tables, the images are
already staged.
