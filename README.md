# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 19 October 2026, 8:15 am IST.
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

**The account scaled roughly tenfold on 3-4 October.** The surviving campaign,
`India | MBBS Students - Doctors | App Downloads | signups` (`120249183882130482`,
`CID` index 5), went from a 1,000/day budget to **12,000/day** and has run at
Rs 10,000-14,000 a day since. It took **Rs 1,88,835 for 8,707 registrations between
30 September and 18 October - Rs 21.69 each, against Rs 32.86 for everything up to
29 September.** Scaling up made registrations cheaper, not dearer. It is the only
campaign still spending; account lifetime spend is now **Rs 3,53,706.66**.

**Eleven new creatives launched (the `E3-` series)** and are appended to the end of
`ANAME`, after the original eight, so no historical `ADAY` row re-labels itself.
`E3-08a 3 medical apps to 1` is now the biggest creative on the account at
Rs 1,08,196 and 5,188 registrations (Rs 20.86), ahead of `E3-09` (Rs 22.25) and
`E3-10` (Rs 22.80).

## An archived ad vanishes from the ad listing - filter for it

**This bit the 19 Oct refresh.** `v5 - I am a med student`, the previous top
creative, was **ARCHIVED**. An unfiltered `level: "ad"` read **silently omits
archived and deleted ads**, so the ad rows came back Rs 54,458.71 short of the
campaign total while every ad that *was* returned looked correct. The campaign-level
figure is what caught it.

Pull ad rows with an explicit status filter, every refresh:

    filtering: [{"field":"effective_status","operator":"IN",
                 "value":["ACTIVE","PAUSED","ARCHIVED","DELETED","CAMPAIGN_PAUSED",
                          "ADSET_PAUSED","WITH_ISSUES","PENDING_REVIEW",
                          "DISAPPROVED","IN_PROCESS"]}]

With it, `v5` comes back at Rs 54,351.01 (1,734 registrations) and `E3-06 My entire
AI stack` at Rs 107.70, and the ad rows sum to the campaign exactly. This is the
same trap `halcyon-reports` documents for archived *campaigns*; it applies at ad
level too. **Keep archived ads in `ADS`** - they spent real money and the client
needs to see it.

## Zero-delivery rows can still carry conversions

A day on which an ad served nothing can still be credited with installs and
registrations attributed late from earlier clicks. **Do not drop a row just because
spend and impressions are zero** - keep it if installs, registrations or clicks are
non-zero, or `sum(ADAY)` silently falls short of `DAILY`. On this refresh that was
worth 9 installs and 7 registrations on 22 September alone.

**The second campaign, `India | MBBS Students - influencers`
(`120249585873390482`, `CID` index 8), is now PAUSED**, closing at Rs 7,700.08 for
386 installs and 160 registrations (Rs 48.13 a registration) - roughly twice the cost
of the main campaign. It opened 24 Sep 2026 on its own Rs 3,000/day budget.

**Budgets were cut on 27 Sep 2026 at 7:36 am** - both campaigns went from Rs 3,000/day
to Rs 1,000/day (activity log, Power Editor, actor Abhijeet Lenka). That regime is
over: the surviving campaign is now on Rs 12,000/day. The old reading of 27-28
September still holds for those two days - the 27th settled at Rs 1,939.44 for 64
registrations (Rs 30.30 each) and the 28th spent only Rs 712.22 because delivery
thinned all day, so its cheap-looking Rs 22.97 a registration is an artefact of a
day that barely spent. Meta revised the 28th up by Rs 2.74 after publication.
`influencer - hasaan` was switched off in the main campaign at 9:25 pm on 26 Sep and
is PAUSED in `ADS`.

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
