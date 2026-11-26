# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 26 November 2026, 8:15 am IST.
  Lifetime arrays (`CAMP`, `ADS`, `AGE`) count **settled days only, to 25 November**; `DAILY` carries 26 November
  as a part-day and `SNAP_DAY` keeps it out of every total.
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

## Where the account stands (26 Nov 2026)

**The account scaled about 3.7x since the September cut.** Lifetime settled spend is
**₹5,96,737.17** (₹7,04,149.86 billed with 18% GST) against ₹1,61,870.20 at the
29 September snapshot, on **50,031 installs** and **23,082 registrations**. Cost per
registration fell from ₹43.27 to **₹25.85** — the account got much bigger and
materially cheaper at the same time.

**`India | MBBS Students - Doctors | App Downloads | signups` carried almost all of it**
and is now PAUSED. It took ₹4,25,428 of the lifetime total — 71% of everything the
account has ever spent — for 18,968 registrations at **₹22.43**. Its last delivery was
15 November; late-attributed conversions trickle in to 19 November.

**Three `Qualified Leads` campaigns opened in November** and are the only ACTIVE ones:

| Campaign | from | spend | regs | per reg |
|---|---|---|---|---|
| `Qualified Leads \| Campaign` | 6 Nov | ₹93,223.44 | 2,959 | **₹31.51** |
| `Qualified Leads \| cost cap 30` | 23 Nov | ₹3,225.75 | 105 | ₹30.72 |
| `Qualified Leads \|instagram reel` | 21 Nov | ₹2,299.59 | 15 | **₹153.31** |

The `instagram reel` test is five times the cost per registration of everything else on
the account. Four days and ₹2,300 is far too little to call, but it is the one to watch.
`cost cap 30` is holding at ₹30.72 on four days.

**14 November served nothing** — zero spend and zero impressions account-wide — though
installs and registrations were still attributed to that date from earlier clicks.

**The part-day moves while you read it.** Building this refresh, the account's
26 November total went from ₹388.77 to ₹425.52 between two calls minutes apart. That is
why every lifetime array here stops at 25 November and `SNAP_DAY` exists.

## Archived ads are missing from an unfiltered ad listing

**This is the same trap as archived campaigns, one level down, and it cost ₹65,551 of
campaign 5's spend on this refresh.** An ad-level pull without an `effective_status`
filter returned 18 ads for `India | MBBS Students - Doctors | App Downloads | signups`
summing to ₹3,59,877 against a campaign total of ₹4,25,428. The missing ₹65,551 was in
five ARCHIVED ads — including **`v5 - I am a med student` at ₹54,351**, the top creative
at the September cut.

Filter `effective_status IN ["ACTIVE","PAUSED","ARCHIVED","DELETED","CAMPAIGN_PAUSED",
"ADSET_PAUSED", ...]` on **ad-level** pulls as well as campaign-level ones. The
reconciliation (ADS sums == CAMP totals) is what catches it; without that check the
creative tables would simply have been missing the account's best-known ad.

The page carries `noindex, nofollow` so it stays out of search results.

A live version of the same report — one that queries Meta each time it is opened — is kept
as a private Claude artifact rather than here.

## A rename can hide a creative swap — check the activity log

**On the 26 Nov 2026 refresh no new swap was found.** Both historical splits were
re-verified and still reconcile to the paisa against their ad's own lifetime:
`v4 - Ecg` ₹10,361.32 + `influencer direct video ad - Rheumatoid Arthritis` ₹99.64 =
₹10,460.96, and `v1 - male` ₹1,076.22 + `influencer - hasaan` ₹413.03 = ₹1,489.25.
The retired-creative rows have not moved since they were first published. The method
below is what produced that check — a dated ad-level pull as at the previous snapshot,
matched against the published `ADS` rows.

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
