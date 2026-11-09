# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 9 November 2026, 8:15 am IST.
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

## The account changed scale in October

**This is the first refresh since 29 September, and almost everything moved.**
Account lifetime spend went from ₹1,61,870.20 to **₹4,83,087.51**; installs from
9,509 to **42,237**; registrations from 3,741 to **19,431**.

- **`India | MBBS Students - Doctors | App Downloads | signups` (index 5) is now the
  account.** ₹4,06,534.72 of the ₹4,83,087.51, against ₹89,451.65 at the last
  snapshot, on a ₹6,000/day budget. Daily account spend ran ₹1,000–2,000 at the end
  of September, **peaked at ₹14,846.11 on 4 October**, held ₹10,000–13,500 for most
  of October and has settled to ₹4,400–8,800 a day in November.
- **`India | MBBS Students - influencers` (index 8) was paused** and closes at
  ₹7,700.08 / 386 installs / 160 registrations. It is no longer in `LIVE`.
- **A new campaign, `Qualified Leads | Campaign` (`120250327482630482`), opened on
  6 November 2026** on its own ₹2,000/day and has taken ₹3,992.14 for 238 installs and
  97 registrations. It was **appended to the end of `CID` as index 9** so no historical
  row re-labels itself. It runs three of the same creatives as campaign 5, which is why
  the same names appear under two campaign ids in `ADS` and `ANAME`.
- The ₹3,000/day → ₹1,000/day cut of 27 September is long superseded; do not read
  October or November against it.

### Creative turnover, and the archived-ad trap

The creative set turned over almost completely. `v5 - I am a med student` was the
account's workhorse at the last snapshot; it closed at ₹54,351.01 and was
**archived**. A scripted **E3** series (E3-01 to E3-10) plus four dated influencer
cuts (`influencer - 20oct`, `influencer 20oct – Copy`, `influencer 27oct`,
`influencer31st oct`, `new influencer 1nov`) now carry delivery, led by
**`E3-08a 3 medical apps to 1` at ₹1,26,819.57** — the largest single creative the
account has run. Sixteen names were **appended** to `ANAME`; nothing was reordered.

**Archived ads are not returned by an unfiltered ad-level listing.** Four of campaign
5's ads are archived and between them hold **₹65,551.01** — `v5 - I am a med student`,
the Rheumatoid ad, `influencer - 20oct` and `E3-06 My entire AI stack`. Without them
the ad rows came ₹65,551.01 short of the campaign total, and the first reading of
that gap was "ads were renamed underneath us". They were not. Pull ad level with
`filtering` on `effective_status IN [..., "ARCHIVED", "DELETED"]`, the same lesson
`halcyon-reports` learned for campaigns. With them included, **every campaign's
`CAMP` row equals the sum of its `ADS`, `DAILY` and `AGE` rows to the paisa**.

**No ad was renamed and no creative was swapped this period.** `ads_account_get_activity_logs`
(`event_category: "ad"`, since 29 Sep) returns only *Ad created*, *Ad status updated*
and *Updated status of ad after it finishes Ad Review* — no *Ad name updated* and no
*Ad updated*. The two ads split at the 22 September swap keep their split: `v4 - Ecg`
and `v1 - male` carry everything to 21 September, `influencer direct video ad -
Rheumatoid Arthritis` and `influencer - hasaan` only what they earned after. Neither
retired half changed, and `hasaan` has not spent since.

**`ADAY` reconciles against `DAILY` on spend, installs and registrations for every
campaign on every settled day.** The only difference is 9 November, the part-day,
where one install sits in `DAILY` and not yet in `ADAY` — which is exactly what the
part-day rule exists to tolerate.

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
account, PAUSED, and has **still never delivered** (checked again 9 Nov 2026): Meta returns no spend, impression or
result fields for it at all. So it is deliberately left out of `CID` and `CAMP`. Add it
the day it spends, **appended to the end of `CID`** so no historical row re-labels itself.
