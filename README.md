# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 12 November 2026, 8:20 am IST.
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

## Where the account stands (12 Nov 2026)

**Two campaigns are live**, and the account is a different size from the one the
September notes below describe.

- `India | MBBS Students - Doctors | App Downloads | signups` (`120249183882130482`,
  `CID` index 5) has run every day since 27 Aug and is now ₹4,18,743.41 lifetime —
  it was ₹89,451.65 at the September snapshot.
- **`Qualified Leads | Campaign` (`120250327482630482`) is new**, first spent on
  6 Nov 2026, and is scaling fast: ₹826.94 → ₹1,718.55 → ₹1,446.65 → ₹2,536.53 →
  ₹4,266.54 → ₹6,225.68 across its first six settled days. It was **appended to
  `CID` as index 9**, per the rule above — never inserted.
- `India | MBBS Students - influencers` (index 8) is **PAUSED** and last spent on
  3 Oct 2026. It closes at ₹7,700.08.

**Account spend stepped up sharply at the start of October**: about ₹1,100/day on
1–2 Oct, ₹4,619.67 on the 3rd, then ₹14,846.11 on the 4th — the highest day on
record and more than seven times the ₹2,000/day cap that applied through late
September. Every day since the 4th has landed between ₹1,560.94 (24 Oct) and
₹13,450.65 (12 Oct). **Cost per registration roughly halved** over the period:
₹43.27 lifetime at the September cut, ₹25.18 now, and ₹25.61 across the last ten
settled days.

**The creative roster turned over almost completely.** A batch of ten `E3-xx` ads
and five dated `influencer …` ads arrived from early October; `E3-08a 3 medical
apps to 1` alone is now the account's biggest spender at ₹1,26,819.57. The
September creatives (`v5 - I am a med student`, `v4 — Ecg`, `v2 - Female`) are
paused or archived.

### September history, kept for reading the old days

**Budgets were cut on 27 Sep 2026 at 7:36 am** — both campaigns then live went from
₹3,000/day to ₹1,000/day (activity log, Power Editor, actor Abhijeet Lenka), so the
account cap fell from ₹6,000/day to ₹2,000/day. 27 Sep settled at ₹1,939.44 for 64
registrations, ₹30.30 each, against ₹41.74 on the 26th at three times the spend.
**28 Sep is not a fair comparison**: it spent only ₹712.22 because delivery thinned
all day and all but stopped from 3 pm to 9 pm, resuming in the 9 pm hour right after
₹30,000 was added to the prepaid balance at 9:32 pm. Watch the balance: a stall like
that looks like a cheap day in the tables. At 7:34 am the influencer ad set
`120249585873460482` also moved from "Automatically bid for actions" to "Optimize bid
for actions" with a **₹19.00 bid cap**. `influencer - hasaan` was switched off in the
main campaign at 9:25 pm on 26 Sep. **That ₹2,000/day regime ended on 3 Oct** — see
above; do not read October and November against those days either.

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

## Archived ads are missing from an unfiltered ad listing

**This bit the 12 Nov 2026 refresh.** An `ads_get_ad_entities` call at `level: "ad"`
without a status filter silently omitted four archived ads on campaign 5, and the ad
rows came out **₹65,551.01 short** of the campaign's own total — including
`v5 - I am a med student`, the third-biggest creative on the account at ₹54,351.01.
Same trap as the archived campaign in `halcyon-reports`.

Always filter `effective_status IN ["ACTIVE","PAUSED","ARCHIVED","DELETED",
"CAMPAIGN_PAUSED","ADSET_PAUSED",...]` on ad-level pulls, and **keep archived ads in
`ADS`** — dropping them is what breaks the campaign-equals-its-ads assertion. The
reconciliation is what catches it; it is not optional.

`limit` is honoured when you pass `object_ids`, so there is no 200-row cap to work
around: 30 ad ids × 79 days came back as all 2,370 rows in one call. Build `ADS`,
`DAILY` and `ADAY` from that one pull and they reconcile by construction.

## Campaigns not in the report

`India | Influencer Videos | App Registrations` (`120249585723440482`) is on the
account, PAUSED, and has **never delivered**: Meta returns no spend, impression or
result fields for it at all. So it is deliberately left out of `CID` and `CAMP`. Add it
the day it spends, **appended to the end of `CID`** so no historical row re-labels itself.
