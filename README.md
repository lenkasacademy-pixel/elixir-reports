# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 21 October 2026, 8:20 am IST.
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

**One campaign is live.** `India | MBBS Students - Doctors | App Downloads | signups`
(`120249183882130482`) carries the account: ₹3,01,387.83 of the ₹3,73,948.48 lifetime,
27,517 installs and 12,387 registrations at ₹24.33 each. The second campaign,
**India | MBBS Students - influencers** (`120249585873390482`), opened on 24 Sep 2026 on
its own ₹3,000/day budget, stopped delivering on 3 Oct and is now **PAUSED**, closed at
₹7,700.08 / 386 installs / 160 registrations.

**The October scale-up is three budget edits, not a change in performance.** All on the
live campaign (activity log, `event_category: "budget"`, actor Abhijeet Lenka):

| When | Daily budget |
|---|---|
| 3 Oct 2026, 11:07 am (ads MCP server) | ₹1,000 → **₹8,000** |
| 4 Oct 2026, 12:10 pm (Ads Manager iOS) | ₹8,000 → **₹12,000** |
| 20 Oct 2026, 10:14 am (Ads Manager iOS) | ₹12,000 → **₹6,000** |

So 1–2 Oct ran at ~₹1,140/day, the 3rd at ₹4,619.67 (edited mid-morning), 4–19 Oct at
₹8,600–₹14,850/day, and **the 20th took ₹7,166.14 against ₹12,779.43 on the 19th purely
because the cap was halved at 10:14 am**. Do not read the 20th as a collapse, and do not
read 1–3 Oct against the rest of the month. Prepaid top-ups: ₹30,000 on 4 Oct,
then ₹70,000 each on 7, 12 and 17 Oct — delivery has not stalled for balance since
28 Sep.

**October is much cheaper than September**: 1–20 Oct bought 9,496 registrations on
₹2,07,757.90 — **₹21.88 each** — against September's ₹33.69 (2,749 on ₹92,623.77). Within
October the daily rate drifted up from ₹20.48 (11th) to ₹29.40 (18th) and back to ₹20.83
(20th); it is noisy, not a trend.

**Eleven new creatives landed on 3 Oct 2026 at 11:56–11:58 am** — the `E3-01` … `E3-10`
set (`E3-08` exists as both `08a` and `08b`) — and two more on 20 Oct, `influencer - 20oct`
(7:33 am) and `influencer 20oct – Copy` (2:07 pm). `E3-08a 3 medical apps to 1` is now the
account's largest ad at ₹1,18,167.42. `v5 - I am a med student`, the biggest earner of the
old set, is **ARCHIVED** at ₹54,351.01, and `influencer - 20oct` and `E3-06` are archived too.

**Archived ads are not returned by an unfiltered ad listing.** This refresh lost
₹65,523.58 of campaign-5 ad spend that way — the ad rows came to ₹2,35,864.25 against a
₹3,01,387.83 campaign header — until the pull was repeated with
`filtering: effective_status IN ["ARCHIVED","DELETED"]`. Same trap as archived campaigns
in `halcyon-reports`. **Always pull ads twice, or filter for every status**, and let the
campaign total be the check.

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
activity log before carrying history forward.** (Checked again on the 21 Oct 2026
refresh: `event_category: "ad"` since 28 Sep holds 13 `Ad created`, 74 `Ad status
updated` and 33 post-review status events and **no** `Ad name updated` or `Ad updated`,
so nothing was renamed or swapped — the 13 new names are genuinely new creatives.) If the creative changed too,
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
