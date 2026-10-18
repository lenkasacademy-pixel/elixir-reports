# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 18 October 2026, 8:30 am IST.
  Self-contained: the figures are baked into the file, so it needs no network and no connector.
  Campaign rail, per-ad tables, day-by-day, age bands, and the GST-inclusive billing line.
  Defaults to the campaigns that are active; a switch shows all nine.
  Only one campaign is live now.

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

**The account re-opened on a much bigger budget on 3-4 October 2026.** It ran about
Rs 1,100/day from 27 Sep to 2 Oct, took Rs 4,619.67 on 3 Oct, and has run
Rs 8,612-Rs 13,451 a day since 4 Oct (average Rs 12,038/day across 4-17 Oct). That
fortnight bought **8,042 registrations for Rs 1,68,525.78 - Rs 20.96 each**, against
Rs 38.27 over 20-28 Sep and Rs 29.30 over the thin 27 Sep - 2 Oct stretch. CPM barely
moved (Rs 48.07 -> Rs 41.65), so the gain is response, not price. Anything before
3 October is a different regime; do not read the two against each other.

A new creative set, **E3-01 through E3-10**, started on 3 October and now carries almost
all delivery. `E3-08a 3 medical apps to 1` alone is at Rs 1,00,251.52 for 4,906
registrations (**Rs 20.43 each**), then `E3-09` (Rs 22.03) and `E3-10` (Rs 22.35).

**`India | MBBS Students - influencers` (`120249585873390482`) is now PAUSED**, last spend
3 Oct, closing at Rs 7,700.08 for 160 registrations (Rs 48.13 each). It stays in `CID` and
`CAMP` at index 8 - never drop a campaign that has delivered.

## Archived ads vanish from the default ad listing

**This nearly broke the 18 Oct 2026 refresh.** `v5 - I am a med student` disappeared from
an unfiltered ad-level read and a much larger `E3-08a 3 medical apps to 1` appeared in its
place - exactly the shape of the 23 Sep rename-and-swap trap below. It was neither: `v5`
had simply been **ARCHIVED**, and archived ads are **not returned by an unfiltered ad
listing**. `E3-06 My entire AI stack` was hidden the same way.

The tell is arithmetic: with the archived ads excluded, the campaign-5 ad rows came to
Rs 2,14,231.62 against a campaign total of Rs 2,68,690.33 - a Rs 54,458.71 hole roughly the
size of the missing ad. **Always filter `effective_status IN ["ACTIVE","PAUSED","ARCHIVED",
"DELETED","CAMPAIGN_PAUSED","ADSET_PAUSED",...]` on ad-level reads**, then assert the ad rows
sum to `CAMP`. Archived ads that carry spend belong in `ADS`; archived ads with no spend at
all are left out, as they always have been.

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

## Reconciling while a campaign is still spending

The live campaign revises the part-day *between calls*. On the 18 Oct refresh the 18 Oct
row moved Rs 2,243.70 -> Rs 2,338.46 and the age breakdown moved Rs 27.69 then Rs 86.91 in
the space of a few minutes, so `CAMP`, `ADS`, `DAILY` and `AGE` pulled minutes apart cannot
tie. **Pull all four in one parallel batch**; done that way they tie to the paisa on spend
and exactly on impressions, and the campaign-level daily sums equal the
`level: "ad_account"` daily totals on spend *and* impressions for every one of the 80 days.

What still does not tie is **result counts on the live day**: the breakdowns lead the
campaign aggregate by a few units (18 Oct: `ADS` +5 installs / +3 registrations, `AGE`
+10 / +3, on 24,892 and 11,086). That is Meta attributing results to ads and age bands
slightly ahead of the campaign roll-up. Spend and impressions must tie exactly - treat any
gap there as a real error - but a handful of results on the one still-spending campaign is
expected. Flag the exact numbers rather than smoothing them.
