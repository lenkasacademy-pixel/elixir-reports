# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 29 October 2026, 8:20 am IST.
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

**October was a different account.** The signups campaign
(`120249183882130482`) is the only one still spending, and its daily budget was moved
three times (activity log, actor Abhijeet Lenka):

| When | Budget | Via |
|---|---|---|
| 3 Oct, 11:07 am | ₹1,000 → **₹8,000**/day | ads MCP server |
| 4 Oct, 12:10 pm | ₹8,000 → **₹12,000**/day | Ads Manager App for iOS |
| 20 Oct, 10:14 am | ₹12,000 → **₹6,000**/day | Ads Manager App for iOS |

Daily spend followed it exactly: ~₹1,100/day to 2 Oct, ₹4,617 on the 3rd (a part-day
at the new budget), ₹12,000–₹13,500 from the 4th to the 19th, and ₹4,500–₹7,800
since the 20th. **Do not read any rate across those lines without saying which budget
was in force.** Balance top-ups of ₹70,000 landed on 7, 12, 17 and 25 Oct (₹30,000
on the 4th); a stall in delivery is worth checking against the balance before it is
read as a cheap day.

**Scaling 12x did not cost what it usually does.** The campaign's lifetime cost per
registration is **₹22.22** (₹3,40,990.09 for 15,345 registrations). October's settled
days (1–28 Oct) ran at **₹19.86** on ₹2,49,320.83 for 12,555 registrations — cheaper
than the lifetime average and cheaper than the ₹30.30 benchmark day of 27 Sep that
the last cut was built around. The account's lifetime spend is now ₹4,13,550.74,
₹3,40,990.09 of it on this one campaign.

**The creative roster was replaced.** Ten new `E3-*` creatives and three new
`influencer` cuts ran in October and now carry most of the campaign. The order of
`ANAME` is unchanged and the fourteen new names are **appended** (indexes 8–21), so
every historical `ADAY` row still points at the creative that actually ran.

| Creative | Spend | Registrations | Each |
|---|---|---|---|
| `E3-08a 3 medical apps to 1` | ₹1,23,614.31 | 5,962 | ₹20.73 |
| `E3-09 Asking medicos which AI app they trust` | ₹53,243.60 | 2,446 | ₹21.77 |
| `influencer 20oct – Copy` | ₹35,425.33 | 2,620 | **₹13.52** |
| `E3-10 Rounds with the strictest senior` | ₹29,495.14 | 1,318 | ₹22.38 |
| `v5 - I am a med student` (ARCHIVED) | ₹54,351.01 | 1,734 | ₹31.34 |

`influencer 20oct – Copy` is the standout and is **still ACTIVE** — ₹13.52 a
registration against ₹20–₹22 for the `E3` series. `v5 - I am a med student`, the
previous top creative, is archived and closed at ₹31.34.

**The influencers campaign (`120249585873390482`) is finished.** It last spent on
3 October (₹2.51), booked one late-attributed install and registration on the 4th,
and now reads PAUSED at ₹7,700.08 for 160 registrations (₹48.13 each) — roughly twice
the signups campaign's rate. `influencer - hasaan` and `influencer direct video ad`
never moved past a few rupees inside it.

The page carries `noindex, nofollow` so it stays out of search results.

A live version of the same report — one that queries Meta each time it is opened — is kept
as a private Claude artifact rather than here.

## An unfiltered ad-level read silently drops archived creatives

**This bit the report on 29 October 2026, and it is worth ₹65,551.01.** An
`ads_get_ad_entities` call at `level: "ad"` with no `effective_status` filter returned
16 ads for the signups campaign. They summed to ₹2,75,439.08 against a campaign total
of ₹3,40,990.09 — **₹65,551.01 short**. Nothing in the response said anything was
missing, and the page would have shown a creative table that did not add up to its own
campaign header.

The missing rows were **ARCHIVED**, and archived ads are not returned by an unfiltered
ad listing any more than archived campaigns are by an unfiltered campaign listing:

| Ad | Status | Spend |
|---|---|---|
| `v5 - I am a med student` | ARCHIVED | ₹54,351.01 |
| `influencer direct video ad - Rheumatoid Arthritis` | ARCHIVED | ₹10,460.96 |
| `influencer - 20oct` | ARCHIVED | ₹631.34 |
| `E3-06 My entire AI stack` | ARCHIVED | ₹107.70 |

**Always pass the status filter at ad level, not just at campaign level:**

    filtering: [{"field": "campaign.id",      "operator": "IN", "value": ["<id>"]},
                {"field": "effective_status", "operator": "IN",
                 "value": ["ACTIVE","PAUSED","ARCHIVED","DELETED","CAMPAIGN_PAUSED",
                           "ADSET_PAUSED","PENDING_REVIEW","DISAPPROVED","WITH_ISSUES",
                           "IN_PROCESS"]}]

With the filter the 22 ads sum to ₹3,40,990.09 — the campaign total, to the paisa.
**The reconciliation is what caught this**, which is the whole reason it is not
optional: `CAMP` totals must equal the sums of `ADS`, `DAILY` and `AGE`, and a
shortfall that large can only be missing rows. Note also that `object_ids` at
`level: "ad"` must be *ad* ids — passing a campaign id returns an empty list and a
quiet note in `additional_info`, which is another way to end up with no rows and no
error.

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
