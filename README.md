# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 4 November 2026, 8:55 am IST.
  Self-contained: the figures are baked into the file, so it needs no network and no connector.
  Campaign rail, per-ad tables, day-by-day, age bands, and the GST-inclusive billing line.
  Defaults to the campaigns that are active; a switch shows all nine.

## Snapshot: 4 Nov 2026 — the account more than doubled in five weeks

The previous published cut was 29 September. Since then the account has gone from
₹1,61,870.20 of media to **₹4,49,538.29** (+ 18% GST = **₹5,30,455.18 billed**), from
9,509 installs to **39,307** and from 3,741 registrations to **18,121**. Cost per
registration came down from **₹43.27 to ₹24.81** lifetime.

| | published 29 Sep | now 4 Nov |
|---|---|---|
| Media spend | ₹1,61,870.20 | **₹4,49,538.29** |
| Billed (incl. GST) | ₹1,91,006.84 | **₹5,30,455.18** |
| Installs | 9,509 | **39,307** |
| Registrations | 3,741 | **18,121** |
| Cost per install | ₹17.02 | **₹11.44** |
| Cost per registration | ₹43.27 | **₹24.81** |
| Active campaigns | 2 | **1** |

**The ₹1,000-a-day regime the last snapshot described ended on 3 October.** The main
campaign's daily budget was raised ₹1,000 → ₹8,000 at 11:07 am on 3 Oct, ₹8,000 →
₹12,000 at 12:10 pm on 4 Oct, and cut back to ₹6,000 at 10:14 am on 20 Oct — all three
by Abhijeet Lenka (activity log, `event_category: "budget"`; the first came through the
ads MCP server, the other two through the Ads Manager iOS app). Account spend went from
₹1,144.37 on 2 October to **₹14,846.11 on the 4th** and peaked at ₹13,450.65 on 12 Oct.
**Nothing before 3 October is comparable with anything after it.**

**The second campaign is finished.** `India | MBBS Students - influencers` was switched
off on 3 Oct at 12:15 pm (Power Editor) and closed at **₹7,700.08 for 386 installs and
160 registrations**; its last spending day was 4 Oct. `India | MBBS Students - Doctors |
App Downloads | signups` is now the only ACTIVE campaign and carries ₹3,76,977.64 —
84% of the account.

**Sixteen new creatives were appended to `ANAME`** (8 → 24): ten in-house `E3-` cuts
plus `influencer - 20oct`, `influencer 20oct – Copy`, `influencer 27oct`,
`influencer31st oct` and `new influencer 1nov`. **`E3-08a 3 medical apps to 1` is now the
single biggest ad on the account** — ₹1,26,819.57, 13,025 installs, 6,053 registrations —
ahead of the retired `v5 - I am a med student` (₹54,351.01).

**Cost per registration improved as spend came down.** ₹29.40 on 18 Oct at ₹12,379 a day
against **₹16.86 on 3 Nov at ₹4,383**. The page's settled 10-day window (26 Oct – 3 Nov,
9 settled days) reads ₹53,692.31 for 2,824 registrations, **₹19.01 each**, CPM ₹33.31. The
cheapest-day footnote names 27 Oct at ₹15.34 — a day carrying a normal share of the spend,
which is the guard described below.

**The creative split on the live campaign has turned over completely.** `new influencer
1nov` went from 0% to 50% of spend between the two comparison windows and is the cheapest
line in it (₹16.05 a registration). `influencer 20oct – Copy` is labelled *impressions
costlier* (CPM ₹32.94 → ₹40.07 at a flat response), and both `E3-08a` and `E3-10` are
*costlier + fading*.

### Reconciliation for this refresh

- **Campaign sums equal the `level: "ad_account"` daily totals exactly** — to the paise and
  to the single impression — on every one of the 37 days from 29 Sep to 3 Nov.
- The only day that differs is **4 Nov, the part-day**: ₹11.79 and 251 impressions, because
  the account-level pull was taken a couple of minutes after the campaign-level one and the
  day was still accruing. It is excluded from every headline by design.
- `CAMP`, `ADS` and `DAILY` agree **exactly** for all nine campaigns. `AGE` sits ₹11.86
  above `CAMP` on the live campaign — the same part-day drift, from the same cause — while
  its installs and registrations match to the unit.
- `ADAY` sums equal `DAILY` per campaign per day on spend, installs **and** registrations
  for every settled day from 30 Aug to 3 Nov.
- Both creative-swap splits still add back to Meta's own ad totals (₹10,361.32 + ₹99.64 =
  ₹10,460.96; ₹1,076.22 + ₹413.03 = ₹1,489.25).
- **Pre-existing, not introduced:** campaign 0's `AGE` registrations sum to 183 against
  `CAMP[0]`'s 184. That one-registration gap is in the published file too — it is Meta's
  own breakdown, and it has been left alone rather than silently nudged.
- **No ad was renamed** between 29 Sep and this snapshot (activity log,
  `event_category: "ad"`), so no history was carried forward under a new name.

Row counts: `CAMP` 9, `ADS` 63, `DAILY` 148, `AGE` 53, `ANAME` 24, `ADAY` 502.
`DAILY` spans 31 Jul – 4 Nov.

## Pulling the ads: the archived-ad trap

**A spend-sorted `level: "ad"` listing silently omits ARCHIVED and DELETED ads.** On this
refresh that hid ₹65,000 of real spend — `v5 - I am a med student` (₹54,351.01) was missing
from a listing that happily included an ₹89.73 ad. Nothing errors; the campaign total simply
stops matching the ads under it.

Always pass the status filter:

    effective_status IN ["ACTIVE","PAUSED","DELETED","ARCHIVED","CAMPAIGN_PAUSED",
                         "ADSET_PAUSED","DISAPPROVED","PENDING_REVIEW","WITH_ISSUES","IN_PROCESS"]

or pull by explicit `object_ids`, which skips entity search altogether and is what this
refresh used for the lifetime ad figures. (This is the same trap the `halcyon-reports`
README records at campaign level — it bites at ad level too.)

## "The last 10 days"

Sits at the top of the all-campaigns view. It rolls `DAILY` up across whichever campaigns
are in scope and shows spend, impressions, CPM, CTR, clicks, installs, cost per install,
registrations and cost per registration, newest first, with a window total. `DAY_WINDOW` at
the top of the rollup controls the length.

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
  registration moved between one rebuild and the next. Verified again on this refresh:
  4 Nov read ₹640.83, then ₹661.08, then ₹672.63 across three pulls minutes apart, and
  even 3 Nov — two days back — moved ₹4,382.59 → ₹4,382.66 → ₹4,382.72.
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

The page carries `noindex, nofollow` so it stays out of search results.

A live version of the same report — one that queries Meta each time it is opened — is kept
as a private Claude artifact rather than here.

## Refreshing it

- Everything except `DAILY` and `ADAY` is lifetime (`date_preset: "maximum"`).
- `DAILY` is per campaign per day, dates as `"MM-DD"` with `YEAR` prefixed.
- **Never reshuffle `CID`** — `DAILY[1]`, `ADS[1]`, `AGE[1]` and `ADAY[1]` all index into it,
  so reordering it re-labels every historical row. Append only.
- **Never reorder `ANAME`** either. Rename an entry in place if Meta renames the ad; append
  genuinely new creatives to the end. `ADAY[2]` indexes into it.
- Ad-level insights do **not** return `campaign_id` unless you ask for it by name.
- Re-sort `ADS` descending by spend and assert the sort.
- Move `SNAP_DAY` and the snapshot stamp together. **The stamp appears four times in
  `index.html`** (the HTML comment at the top, the masthead stamp, the footer paragraph and
  the data-block comment) and once here.

### Checks that must pass before publishing

- For every campaign, `CAMP` totals equal the sums of its `ADS`, `DAILY` and `AGE` rows.
  A few rupees of drift on the one live campaign is the part-day moving between pulls.
- `sum(ADAY) == DAILY` per campaign per settled day on spend, installs **and**
  registrations. Only the part-day may differ.
- Campaign sums equal the `level: "ad_account"` daily totals on both spend and impressions,
  per day. Anything beyond a few rupees on a settled day is a real problem, not settling.

**Watch the prepaid balance.** Delivery stalls when it runs out and resumes when it is
topped up, and a stalled day reads as a cheap day in the tables — it books registrations
earned by the days before it on almost no spend. Six top-ups show in the log for this
period: ₹30,000 on 4 Oct, ₹70,000 on each of 7, 12, 17 and 25 Oct, and ₹40,000 on 3 Nov. `event_category: "budget"`
lists them as *Money added to balance* alongside the daily *Account billed* entries.

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
