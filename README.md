# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 21 November 2026, 8:20 am IST.
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

**Where the account stands at 21 Nov 2026.** Lifetime spend is **₹5,61,855.20** for
**48,265 installs and 22,185 registrations** — ₹25.33 a registration over the account's
whole life. Ten campaigns are tracked now; only one is still running.

**`India | MBBS Students - Doctors | App Downloads | signups` (`120249183882130482`,
index 5) was the whole story and is now paused.** It went from ₹89,451.65 at the 29 Sep
snapshot to **₹4,25,427.74** — four and three-quarter times larger — taking 40,640 installs
and 18,967 registrations at **₹22.43 each**, and stopped after 15 November. It is 76% of
everything the account has ever spent. The ₹6,000/day cap of late September is long gone:
October ran at ₹10,000–₹14,000 a day at its peak (4 Oct, ₹14,846.11).

**`Qualified Leads | Campaign` (`120250327482630482`) is the only ACTIVE campaign.** It
opened 6 November, has taken **₹63,866.81 for 4,648 installs and 2,183 registrations at
₹29.26**, and was **appended to `CID` as index 9** so no historical row re-labels itself.
It is running ₹6.83 a registration dearer than the campaign it replaced — worth watching,
but it is two weeks old against that campaign's three months.

**`influencer3 nov` is carrying it almost single-handed**: ₹37,546.84 of the campaign's
₹63,866.81, and every day from 15 November onward is essentially that one ad.

**14 November is a zero-impression day that still booked results.** The whole account spent
**₹0.00 on no impressions** and still recorded 13 installs and 31 registrations from the
clicks of the days before it. Nothing was paused; delivery resumed on the 15th. This is the
clearest example yet of why a near-dead day flatters its own cost per registration, and why
the "cheapest day" footnote ignores days carrying less than half the window's average
settled spend.

**The `Elixir 3.0` creative batch now carries the account.** The `E3-` ads are the Elixir 3.0
reels (their cover frames are in `ad-thumbs/`). `E3-08a 3 medical apps to 1` alone is at
**₹1,26,819.57**, more than the whole account had spent at the last snapshot.
`v5 - I am a med student`, the old workhorse, is **ARCHIVED** at ₹54,351.01.

**No ad was renamed this refresh.** The two 22 September creative swaps are still split
exactly as before — see the section below — and their post-swap rows have barely moved
(`influencer direct video ad` ₹95.67 → ₹99.64; `influencer - hasaan` unchanged at ₹413.03).
`influencer - hasaan` remains PAUSED in `ADS`.

## `ADAY` follows the live campaign, so it changed hands

`ADAY` is ad-level day rows **for the campaign(s) still running**. At the 29 Sep snapshot that
meant campaigns 5 and 8; both are now paused, so `ADAY` covers **campaign 9 only**, its whole
life (6–21 Nov). The old campaign-5 and campaign-8 rows were dropped with them: keeping them
would have broken the `sum(ADAY) == DAILY` assertion on every October and November day of
campaign 5, because `ADAY` stopped at 29 September while `DAILY` did not.

**`ANAME` was not reordered.** The eight existing names keep indexes 0–7 even though no `ADAY`
row points at them any more, and the seven creatives in the live campaign were **appended** as
indexes 8–14. If campaign 5 ever restarts, its historical rows would still resolve correctly.

What the assertion gave this refresh: installs and registrations match `DAILY` **exactly on
every one of the sixteen days**. Spend drifts ₹+0.04 on 20 Nov and ₹+24.75 on the 21st,
because the ad-level read happened minutes after the campaign-level one and Meta was still
settling both days. Only the part-day is allowed to differ by the rule; the ₹0.04 on the 20th
is inside Meta's ~48h revision window and is noise, not a dropped row. Re-pull both levels
close together if you want them to agree to the paisa.

## Archived ads vanish from an unfiltered ad listing

**This nearly cost ₹65,551.01 this refresh.** An ad-level listing without a status filter
silently omits ARCHIVED (and DELETED) ads. Campaign 5's ad rows summed to ₹3,59,876.73
against its own ₹4,25,427.74 — a ₹65,551.01 hole — because four archived ads were missing,
`v5 - I am a med student` (₹54,351.01) among them. With

    filtering: [{field: "effective_status", operator: "IN",
                 value: ["ACTIVE","PAUSED","ARCHIVED","DELETED","CAMPAIGN_PAUSED",
                         "ADSET_PAUSED","IN_PROCESS","WITH_ISSUES",
                         "PENDING_REVIEW","DISAPPROVED"]}]

the ad rows sum to ₹4,25,427.74 exactly. **Always pass that filter at ad level**, the same way
`halcyon-reports` has to pass it at campaign level. The campaign-vs-ads reconciliation is what
catches it; without that check the page would simply have under-reported four creatives.


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

That is exactly how `Qualified Leads | Campaign` was added on 21 Nov 2026 — appended as
index 9, leaving indexes 0–8 untouched.

**One pre-existing gap, not introduced here:** campaign 0's age rows sum to 183
registrations against the 184 its `CAMP` row carries. The missing one sits in no age band
and the same gap is in the 29 Sep figures as published. Campaign 0 has been paused since
August and its totals have not moved, so the gap is Meta's, not the refresh's.
