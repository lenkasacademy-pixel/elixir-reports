# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 27 October 2026, 8:11 am IST.
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

## Where the account is now (27 Oct 2026)

**One campaign is live and it is the whole account.** `India | MBBS Students - Doctors |
App Downloads | signups` (`120249183882130482`) is the only ACTIVE campaign and carries
₹3,29,653.64 of the account's ₹4,02,214.29 lifetime spend, 31,549 installs and 14,658
registrations. Everything else is PAUSED.

**October was a deliberate scale-up, and it worked.** Budget changes on campaign 5, all
from the activity log (`event_category: "budget"`, actor Abhijeet Lenka):

| when | daily budget |
|---|---|
| 3 Oct 11:07 am (ads MCP server) | ₹1,000 → **₹8,000** |
| 4 Oct 12:10 pm (Ads Manager iOS) | ₹8,000 → **₹12,000** |
| 20 Oct 10:14 am (Ads Manager iOS) | ₹12,000 → **₹6,000** |

Cost per registration fell as the spend rose, which is the opposite of the usual shape:
**₹32.86** across everything up to 3 Oct, **₹19.86** over 4–26 Oct, **₹13.77** across
23–26 Oct. Do not read September days against October days; the budget, the creatives and
the ad set bid are all different.

**India | MBBS Students - influencers** (`120249585873390482`) was switched off at
12:15 pm on 3 Oct and closes at ₹7,700.08 for 386 installs and 160 registrations
(₹48.13 each). It kept booking late-attributed spend through 4 Oct, which is why its
last `DAILY` rows sit after the pause. Its three ads now read `CAMPAIGN_PAUSED`.

**Eleven new creatives, the `E3` set, went live at noon on 3 Oct and the field was cut
down hard.** By 15 Oct only `E3-08a 3 medical apps to 1`, `E3-09 Asking medicos which AI
app they trust` and `E3-10 Rounds with the strictest senior` were still carrying spend.
`influencer - 20oct` went live on 20 Oct and was taken down the same afternoon;
`influencer 20oct – Copy` took over at 2:15 pm. **Those are two distinct ad ids, not a
rename** — check the activity log before assuming either way (see the rename section
below). The Copy is the cheapest line the account has run: ₹27,898.41 for 2,126
registrations, **₹13.12 each**.

`v5 - I am a med student`, the workhorse until then, was taken down at 7:39 am on 15 Oct
and closes at ₹54,351.01 / 1,734 registrations (₹31.34). `E3-06 My entire AI stack` went
the same morning; the post-swap `influencer direct video ad - Rheumatoid Arthritis`
creative went on 20 Oct at 7:27 am.

**Watch the prepaid balance.** Top-ups of ₹70,000 landed on 7, 12, 17 and 25 Oct and
₹30,000 on 4 Oct. A day that stalls on a thin balance still books registrations earned by
the days before it, so it reads as a suspiciously cheap day in the tables — the same trap
the "cheapest day" footnote guards against. 24 Oct (₹1,560.94, 194 registrations) is the
clearest example in this cut.

## An unfiltered ad listing silently drops archived ads

**This cost ₹65,551 on this refresh and was caught by the reconciliation, not by eye.**
The first ad-level pull for campaign 5 came back ₹65,551 short of the campaign's
₹3,29,653.64 — because ARCHIVED and DELETED ads are simply absent from an unfiltered
listing. Four ads were missing: `v5 - I am a med student` (₹54,351.01),
`influencer direct video ad - Rheumatoid Arthritis` (₹10,460.96), `influencer - 20oct`
(₹631.34) and `E3-06 My entire AI stack` (₹107.70) — between them a fifth of the
campaign's lifetime spend and its single biggest creative.

Always pass the full status filter on ad **and** campaign listings:

    filtering: [{ field: "effective_status", operator: "IN", value: [
      "ACTIVE","PAUSED","ARCHIVED","DELETED","IN_PROCESS","WITH_ISSUES",
      "CAMPAIGN_PAUSED","ADSET_PAUSED","DISAPPROVED","PENDING_REVIEW",
      "PREAPPROVED","PENDING_BILLING_INFO"] }]

An ad an advertiser deletes in Power Editor keeps its whole spend history and must stay in
`ADS` — deleting it in Ads Manager does not delete what it cost.

## Reconciliation: the breakdowns lead the campaign rollup

The checks the builder runs before writing the file, all of which must pass:

- `sum(ADAY) == DAILY` per campaign per settled day, on **spend, installs and
  registrations**. Only `SNAP_DAY` may differ.
- each campaign's `DAILY`, `AGE` and `ADS` totals agree with each other on spend,
  impressions, installs and registrations.
- `ADS` is sorted descending by spend, and `ANAME`/`CID` order is never touched.

**On 27 Oct the live campaign's own rollup lagged its breakdowns** — `CAMP` read
₹3,29,653.64 and 7,732,169 impressions where `DAILY`, `AGE` and `ADS` all read
₹3,29,668.1x and 7,732,627. That is ₹14.50 (0.004%) and 458 impressions (0.006%) on a
campaign spending ~₹6,000 a day, and **installs and registrations match exactly on all
four**, so it is the rollup settling behind, not a build error. The breakdowns agreeing
with *each other* is the check that matters; drift against the rollup gets reported, not
silently reconciled.

Two single-value corrections were needed to make the older rows reconcile, both late
attribution rather than mistakes: campaign 5's `ADAY` row for 22 Sep (`v5`, 26 → 27
registrations) and campaign 0's `AGE` row for 25-34 (72 → 73). Campaign 0 last spent
weeks ago, so re-pull the lifetime breakdowns of dormant campaigns too — "it cannot have
moved" is not true of registrations.

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
