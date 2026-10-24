# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 24 October 2026, 8:10 am IST.
  Self-contained: the figures are baked into the file, so it needs no network and no connector.
  Campaign rail, per-ad tables, day-by-day, age bands, and the GST-inclusive billing line.
  Defaults to the campaigns that are active; a switch shows all nine.

## Snapshot: 24 Oct 2026, 8:10 am IST

Covers **31 Jul – 24 Oct 2026** (86 delivering days). 24 October is a part-day and is
held out of every headline.

| | Active only (1 campaign) | All nine campaigns |
|---|---|---|
| Media spend | ₹3,16,510.90 | ₹3,89,071.55 |
| Billed incl. 18% GST | **₹3,73,482.86** | ₹4,59,104.43 |
| Installs | 29,959 (₹10.56) | 32,936 (₹11.81) |
| Registrations | 13,744 (**₹23.03**) | 14,779 (₹26.33) |

**The account scaled hard since the 29 September snapshot, and got cheaper doing it.**
`India | MBBS Students - Doctors | App Downloads | signups` went from ₹89,452 to
₹3,16,511; the **₹2,27,059 of new spend bought 11,024 registrations at ₹20.60**, against
the ₹32.89 it had averaged to 29 September. Daily spend went from about ₹1,100 on
30 September to ₹14,846 on 4 October, held ₹8,600–13,500 a day for a fortnight, and has
eased to ₹4,500–7,800 over 20–23 October. It is now the **only** ACTIVE campaign on the
account.

**Thirteen new ads opened on 3 October** — the eleven `E3-` creatives plus
`influencer - 20oct` and `influencer 20oct – Copy`. `E3-08a 3 medical apps to 1` alone
carries ₹1,18,942 (38% of the campaign) at ₹20.91 a registration;
`influencer 20oct – Copy` is the cheapest line on the account at **₹12.84**.

**The rename check came back clean this time.** `ads_account_get_activity_logs`
(`event_category: "ad"`) records all thirteen as *Ad created*, with **no `Ad name updated`
and no `Ad updated` creative swap** anywhere in the window. So nothing was split, the
thirteen names were **appended** to `ANAME` at indexes 8–20 and every historical `ADAY`
row still points at the creative that earned it. The two 22 September swaps stay split
exactly as they were.

**The last three days are the cheapest of the whole run** — ₹12.42, ₹10.23 and ₹12.54 a
registration on 21–23 October against ₹17–29 through the first three weeks. The page's
own creative split attributes it rather than guessing: `influencer 20oct – Copy` took
**0% of the spend in the preceding window and 74% in this one**, at ₹12.76 a
registration. Those are also the lowest-spend days of the run and 23 October is still
inside the ~48 hours Meta revises, so the exact figures are provisional — but the
direction is a creative that is working, not an artefact.

**`India | MBBS Students - influencers` is finished.** It stopped spending after
3 October and now reads PAUSED, closing at ₹7,700.08 for 386 installs and 160
registrations. The ₹6,000/day → ₹2,000/day budget cut of 27 Sep (below) no longer binds:
the surviving campaign carries the whole account.

### Reconciliation for this snapshot

Everything closes to the paisa except four known items, all small and all expected:

| Check | Result |
|---|---|
| `ADS` sums vs `CAMP`, all nine campaigns | **exact** on spend, impressions, clicks, installs, registrations |
| `DAILY` sums vs `CAMP` | exact except campaign 5: **registrations 13,743 vs 13,744** (−1) |
| `AGE` sums vs `CAMP` | campaign 5 **+₹10.72 / +317 impressions / +2 clicks**; campaign 0 **−1 registration** |
| `ADAY` sums vs `DAILY`, per campaign per day | exact on installs and registrations on **every** day; spend differs only on 10‑23 (+₹0.05) and the part-day 10‑24 (+₹10.67 / +314 impressions) |

The age-band and part-day drift is Meta's own breakdown rounding and the live day still
filling. A single registration falling between the period total and the day rows is
late attribution at a day boundary. Nothing here needed a correction — but if any of
these grows past a few rupees or a couple of results, stop and find out why before
publishing.

## `date_preset: "maximum"` under-reports — use an explicit `time_range`

**This cost an afternoon on 24 Oct 2026.** Campaign 5's day rows summed to ₹10.26 *more*
than its own lifetime total, which looks like a data error and is not: `date_preset:
"maximum"` returned a smaller lifetime figure than the days it is supposed to contain.
An explicit `time_range` of `2026-07-31 .. 2026-10-24` returns
**₹3,16,510.90 / 7,304,046 impressions / 63,454 clicks**, which equals the sum of the day
rows exactly.

So: **pull `CAMP`, `ADS` and `AGE` with an explicit `time_range` spanning the whole run,
not `date_preset: "maximum"`.** Extend the end date every refresh.

## Archived and deleted ads are missing from the default ad listing

The ad-level pull returned four fewer ads than the account has, and `ADS` summed
**₹65,551.01 short** of `CAMP` — on paused campaigns, so no live number was wrong, but
the leaderboard was quietly incomplete. The default listing omits ARCHIVED and DELETED
objects. Re-pull ad level with an explicit
`filtering` on `effective_status IN [ACTIVE, PAUSED, ADSET_PAUSED, CAMPAIGN_PAUSED, ARCHIVED, DELETED, …]`
and the sums close to the paisa. `ADS` currently carries statuses ACTIVE, PAUSED,
ADSET_PAUSED, CAMPAIGN_PAUSED, ARCHIVED and the report's own `REPLACED`.

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
  registration moved between one rebuild and the next. At this snapshot it reads
  *9 settled days — 24 Oct excluded*, ₹83,409.95 for 4,074 registrations (₹20.47).
- **The "cheapest day" footnote ignores days that barely spent.** A throttled or
  near-dead day still books registrations earned by the days before it, so its cost
  per registration is both meaningless and unbeatable. Only days carrying at least
  half the window's average settled spend are eligible. Before this guard the
  footnote named 21 Sep at ₹17.88 — a suppressed-delivery day that took ₹733 on
  13,898 impressions against the ₹3,000-4,000 either side of it — which is not a
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

## History worth keeping

A second live campaign, **India | MBBS Students - influencers** (`120249585873390482`),
opened on 24 Sep 2026 on its own ₹3,000/day budget alongside the original ₹3,000/day
campaign. Account spend roughly doubled that day — that was a deliberate second campaign,
not a budget change on the first. It last spent on 3 Oct 2026 and is now PAUSED.

**Budgets were cut on 27 Sep 2026 at 7:36 am** — both campaigns went from ₹3,000/day to
₹1,000/day (activity log, Power Editor, actor Abhijeet Lenka), so the account cap fell
from ₹6,000/day to ₹2,000/day. **The first day under the cap was the cheapest normal day
in a fortnight**: 27 Sep settled at ₹1,939.44 for 64 registrations, ₹30.30 each, against
₹41.74 on the 26th at three times the spend. **28 Sep is not a fair comparison**: it
spent only ₹709.48 (31 registrations, ₹22.89 each) because delivery thinned all day and
all but stopped from 3 pm to 9 pm (₹2.74 across those six hours, hourly breakdown). It
resumed in the 9 pm hour, right after ₹30,000 was added to the prepaid balance at 9:32 pm
(activity log, "Money added to balance"). Watch the balance: a stall like that looks
like a cheap day in the tables. At 7:34 am the influencer ad set `120249585873460482`
also moved from "Automatically bid for actions" to "Optimize bid for actions" with a
**₹19.00 bid cap**. The cap no longer binds — spend ran to ₹14,846 on 4 October — so do
not read the ₹6,000/day-era days, the ₹2,000/day week, and the October scale-up as one
regime. `influencer - hasaan` was switched off in the main campaign at 9:25 pm on
26 Sep and is still PAUSED in `ADS`.

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

**A pure rename is handled differently from a new creative.** If a name changed but the
creative did not, rename `ANAME` **in place** so past `ADAY` rows keep their creative —
never reorder `ANAME`, and only a genuinely new creative gets appended to the end.

## Refreshing it

- The snapshot timestamp appears **four times in `index.html`** — the `<!-- Standalone
  snapshot -->` comment in `<head>`, the masthead `.stamp`, the account line in the
  masthead prose, and the `Snapshot data` comment above the arrays — and **once here**.
  Move all five.
- Set `SNAP_DAY` to the still-running day. The page gives it its own row, labels it
  *part day so far*, and keeps it out of every headline and every window total.
- **Never reshuffle `CID`.** `CAMP`, `ADS`, `DAILY` and `AGE` all carry an index into it.
  New campaigns are appended to the end.
- Everything except `DAILY` and `ADAY` is lifetime (pulled with an explicit `time_range`,
  see above). `DAILY` is per campaign per day with dates as `"MM-DD"` and `YEAR`
  prefixed; `ADAY` is per ad per campaign per day, same date form.
- Ad-level insights do **not** return `campaign_id` unless it is requested by name.
- Re-sort `ADS` descending by spend after patching, and assert the sort. Do **not**
  sort `ANAME` — `ADAY` indexes into it positionally.
- Metrics are `mobile_app_install` and `omni_complete_registration` at campaign and day
  level; ads also carry `link_click`.

## Campaigns not in the report

`India | Influencer Videos | App Registrations` (`120249585723440482`) is on the
account, PAUSED, and has **never delivered**: Meta returns no spend, impression or
result fields for it at all. So it is deliberately left out of `CID` and `CAMP`. Add it
the day it spends, **appended to the end of `CID`** so no historical row re-labels itself.
