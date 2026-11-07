# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 7 November 2026, 8:10 am IST.
  Self-contained: the figures are baked into the file, so it needs no network and no connector.
  Campaign rail, per-ad tables, day-by-day, age bands, and the GST-inclusive billing line.
  Defaults to the campaigns that are active; a switch shows all ten.

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

## Where the account stands at 7 Nov 2026

**Lifetime media spend Rs 4,68,792.73** + 18% GST Rs 84,382.69 = **Rs 5,53,175.42 billed**.
41,133 installs at Rs 11.40 and **18,964 registrations at Rs 24.72**. The
active-only default view shows Rs 3,96,232.08 for 17,929 registrations at
**Rs 22.10**.

**October was the biggest month this account has ever had, by a long way.** The
signups campaign's daily budget was raised three times and then pulled back:

| When | Campaign | Budget | Actor / app |
|---|---|---|---|
| 3 Oct, 11:07 am | signups | Rs 1,000 -> **Rs 8,000**/day | Abhijeet Lenka, ads MCP server |
| 4 Oct, 12:10 pm | signups | Rs 8,000 -> **Rs 12,000**/day | Abhijeet Lenka, Ads Manager iOS |
| 20 Oct, 10:14 am | signups | Rs 12,000 -> **Rs 6,000**/day | Abhijeet Lenka, Ads Manager iOS |
| 6 Nov, 4:29 pm | Qualified Leads | Rs 6,000 -> **Rs 2,000**/day | Abhijeet Lenka, Power Editor |

Account spend went from Rs 1,145.17 on 30 September to **Rs 14,846.11 on 4
October**, the dearest day on record, and has eased to Rs 4,400-Rs 8,100 a day
since the 20th. **Rs 3,50,000 was added to the prepaid balance in six top-ups**
between 4 October and 3 November (Rs 30,000 on the 4th, Rs 70,000 each on 7, 12,
17 and 25 Oct, Rs 40,000 on 3 Nov). Anything from 3 October onward is a
different regime again; do not read it against the Rs 2,000/day fortnight.

**And it bought registrations much more cheaply.** Cost per registration by
month, from `DAILY`: **September Rs 33.69** (Rs 92,623.77 / 2,749),
**October Rs 20.17** (Rs 2,68,262.50 / 13,301), **1-6 November Rs 19.49**
(Rs 35,733.47 / 1,833). Scaling 12x did not make the registration dearer - it
made it 40% cheaper. That is the headline to put in front of the client.

**A new campaign opened on 6 November.** `Qualified Leads | Campaign`
(`120250327482630482`) has two days of data: Rs 1,262.23, 69 installs, 20
registrations, **Rs 63.11 each** - three times the signups campaign's Rs 22.05.
It was cut from Rs 6,000 to Rs 2,000 a day within hours of starting. Far too
early to judge, but it is the one line to watch. It was **appended to the end of
`CID` as index 9**, so no historical row re-labels itself.

**`India | MBBS Students - influencers` is finished for now.** It last delivered
on 4 October and reads PAUSED, closing at Rs 7,700.08 for 160 registrations
(Rs 48.13). `LIVE` is now the signups campaign and the new Qualified Leads one.

**The creative roster turned over completely.** Ten `E3-*` ads and five dated
influencer ads were created in October; `v5 - I am a med student`, which was the
biggest spender at the last snapshot, is now archived. Current top lines in the
signups campaign:

| Creative | Spend | Registrations | Each |
|---|---|---|---|
| `E3-08a 3 medical apps to 1` | Rs 1,26,819.57 | 6,053 | Rs 20.95 |
| `v5 - I am a med student` (archived) | Rs 54,351.01 | 1,734 | Rs 31.34 |
| `E3-09 Asking medicos which AI app they trust` | Rs 53,243.63 | 2,451 | Rs 21.72 |
| `influencer 20oct - Copy` | Rs 52,030.02 | 3,336 | **Rs 15.60** |
| `E3-10 Rounds with the strictest senior` | Rs 31,644.33 | 1,395 | Rs 22.68 |
| `new influencer 1nov` | Rs 28,060.81 | 1,540 | **Rs 18.22** |

The two influencer lines are the cheapest registrations on the account and the
two carrying the newest spend. `E3-08a` has taken a quarter of the account's
lifetime spend on its own.

## Archived ads are invisible to a default listing - this nearly broke the refresh

**On the 7 Nov pull, the signups campaign's ad rows came to Rs 3,29,418.84
against its own campaign total of Rs 3,94,969.85 - Rs 65,551.01 short.** Nothing
was renamed and nothing was wrong with the campaign figure. Four ad objects had
been **archived**, and an unfiltered ad listing does not return them:

| Ad | Status | Carries |
|---|---|---|
| `v5 - I am a med student` | ARCHIVED | Rs 54,351.01 |
| `influencer direct video ad - Rheumatoid Arthritis` (+ its `v4 - Ecg` half) | ARCHIVED | Rs 10,460.96 |
| `influencer - 20oct` | ARCHIVED | Rs 631.34 |
| `E3-06 My entire AI stack` | ARCHIVED | Rs 107.70 |

**Always request ads with `effective_status IN [..., "ARCHIVED", "DELETED"]`.**
Same trap as `halcyon-reports` hit at campaign level on 28 Sep. With them
included the ad rows equal the campaign total to the paisa. The reconciliation
is what caught it - it is not optional.

## No rename this time - but the check still has to be run

The activity log (`event_category: "ad"`, 28 Sep onward) returns **230 entries:
146 status changes, 50 post-review status updates and 34 ads created. Zero name
changes and zero creative swaps.** So every new name is a genuinely new ad
object, `ANAME` was only appended to (16 new names after index 7), and the
22 September split rows are untouched.

Those two splits still reconcile exactly. The `influencer - hasaan` ad object
totals Rs 1,489.25 = Rs 1,076.22 (`v1 - male`, to 21 Sep) + Rs 413.03 (from the
22nd), and the Rheumatoid object totals Rs 10,460.96 = Rs 10,361.32 (`v4 - Ecg`)
+ Rs 99.64, on spend, impressions, clicks, link clicks, installs and
registrations alike. The post-swap halves were re-pulled with a ranged query
from 22 Sep so the live side carries its own reach.

## What the 7 Nov refresh corrected in the published file

- **29 September was published as a part-day** at Rs 363.16 for 15
  registrations. Settled, it is **Rs 946.93 for 33** - two and a half times the
  spend. The part-day rule is what kept it out of the window total.
- **28 September settled up Rs 2.74** (signups campaign); counts unchanged.
- **Campaign 0's age rows were one registration short.** The `25-34` band read
  72 where Meta reports 73, so its age rows summed to 183 against a campaign
  total of 184. Corrected to 73; the band now ties. Campaign 0 has been paused
  since August, so this was a stale cell rather than new delivery.

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

`Qualified Leads | Campaign` (`120250327482630482`) was added exactly that way on
7 Nov 2026: appended as `CID` index 9, never inserted.
