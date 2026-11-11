# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 11 November 2026, 8:10 am IST.
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

## Where the account stands (11 Nov 2026)

**The account roughly tripled in six weeks.** Lifetime media spend was ₹161,870.20 at the
29 Sep snapshot and is **₹500,245.28** now — 43,530 installs at ₹11.49 and **19,957
registrations at ₹25.07**. ₹268,262.50 of the increase was spent in October alone.

**October is the cheapest month the account has had, and not because it went quiet.**

| Month | Spend | Installs | Regs | Cost/reg |
|---|---|---|---|---|
| Aug 2026 | ₹70,797.29 | 3,058 | 1,038 | ₹68.21 |
| Sep 2026 | ₹92,623.77 | 6,600 | 2,750 | ₹33.68 |
| Oct 2026 | ₹268,262.50 | 27,491 | 13,303 | **₹20.17** |
| Nov 2026 (1–11) | ₹68,421.59 | 6,384 | 2,868 | ₹23.86 |

That is 4.8 times September's registrations on 2.9 times the spend. Two things
happened on 3 October: **eleven new creatives** (`E3-01` … `E3-10`, with `E3-08` in an `a`
and a `b` version) were published in the main campaign at 11:56–11:58 am, and the
campaign's daily budget went from **₹1,000 to ₹8,000** at 11:07 am (activity log, actor
Abhijeet Lenka, via `ads MCP server`). It went to **₹12,000** the next day at 12:10 pm.

`E3-08a 3 medical apps to 1` is now the biggest-spending ad the account has ever run:
**₹126,819.57 for 6,053 registrations at ₹20.95**. `E3-09` took ₹53,243.63 at ₹21.72 and
`E3-10` ₹33,034.20 at ₹22.91. The rest of the E3 set never got past a few hundred rupees.

**The spend is being walked back down, and redirected.** Main-campaign budget: ₹12,000/day
→ ₹6,000 on 20 Oct 10:14 am → ₹4,500 on 9 Nov 9:46 am → **₹3,500 on 10 Nov 4:09 pm**.
Meanwhile a third campaign, **`Qualified Leads | Campaign`** (`120250327482630482`),
opened on 6 November and is the one being funded: ₹2,000/day on 6 Nov 4:29 pm → ₹3,000 on
9 Nov → **₹5,000 on 10 Nov**, the same minute the main campaign was cut to ₹3,500. **The
account cap today is ₹8,500/day with more of it on the six-day-old campaign than on the
one that has spent ₹415,383.** That is worth putting to the client: the new campaign is
**dearer** so far — ₹12,301.48 for 317 registrations, **₹38.81 each**, against the main
campaign's ₹21.99 across November. It runs three of the main campaign's own ads
(`new influencer 1nov`, `influencer 20oct – Copy`, `E3-10`) plus fifteen that have never
delivered.

**The main campaign's cost per registration has been climbing all month** — ₹16.89 on
3 Nov to ₹34.34 on 10 Nov. The page's own split card attributes it: over 3–6 Nov against
7–10 Nov, impressions got only 2% dearer while each 1,000 of them produced **28% fewer
registrations**. That is creative fatigue on `new influencer 1nov`, which carries 73–83%
of the spend, not an auction problem.

**`India | MBBS Students - influencers` is paused.** It last spent on 3 October and closes
at ₹7,700.08 for 386 installs and 160 registrations. Its final `effective_status` is
`PAUSED` (it was `ACTIVE` at the last snapshot), so `CAMP[8][2]` changed.

**Both swapped creatives are now off.** `influencer direct video ad - Rheumatoid Arthritis`
is `ARCHIVED` and `influencer - hasaan` is `PAUSED` in the main campaign; neither has spent
since the last snapshot except the Rheumatoid ad's ₹3.97.

### Still true from the September snapshot

The 24 Sep second-campaign launch, the 27 Sep cut from ₹3,000/day to ₹1,000/day, the
near-dead 28 Sep that only looked cheap, and the ₹19.00 bid cap on ad set
`120249585873460482` are all still inside the data this page shows. The relevant lesson
survives: **a day that barely spends still books registrations earned by the days before
it, so its cost per registration flatters** — which is why the "cheapest day" footnote
ignores days below half the window's average settled spend.

**29 Sep is the proof that the part-day rule earns its keep.** It was published as
`SNAP_DAY` at ₹946.93 account-wide; it settled at ₹1,530.70 — **₹583.77 and 20
registrations more**, a 62% revision on a day that would otherwise have set a headline.

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

## Reconciliation at the 11 Nov snapshot

**Campaign day rows against the account's own daily totals: 102 of 104 days agree
exactly** on spend, impressions, clicks, installs and registrations. The two that do not
are both inside Meta's 48-hour revision window, and in both the account read (taken last)
is the higher one:

| Day | campaign sum − account | note |
|---|---|---|
| 10 Nov | −₹0.82, −17 impressions | 0.009% of a ₹9,478 day |
| 11 Nov | −₹83.59, −2,060 impressions, −2 installs, −1 reg | `SNAP_DAY`, excluded from every headline |

**`CAMP` against the sums of `ADS`, `DAILY` and `AGE`:** exact for all eight settled
campaigns. The two live ones drift, because Meta's lifetime campaign read was taken a few
minutes before the ad, day and age reads and the part-day grew in between:

| Campaign | vs `ADS` | vs `DAILY` | vs `AGE` |
|---|---|---|---|
| 5 — main | +₹27.67 | +₹12.58 | +₹27.67 |
| 9 — Qualified Leads | +₹54.47 | +₹28.11 | +₹54.47 |

All of it sits inside 11 Nov. `CAMP` carries Meta's own lifetime figure, because that is
what the client sees in Ads Manager; the drift is reported rather than smoothed away.
`sum(ADAY) == DAILY` exactly on every settled day except 10 Nov, which is ₹0.12 apart.

**The age breakdown paginates, and a short `limit` silently drops bands.** Pulling
`breakdowns: ["age"]` with `limit: 50` returned 51 rows and quietly lost the `Unknown`
band for two campaigns and the whole `65+` row for a third — ₹135.46 of real spend. The
`Unknown` band carries zero spend and impressions but real installs and registrations
(Meta could not place them in a band), so `AGE` will not reconcile against `CAMP` on
results without it. **Pull age with `limit: 200` and check the row count per campaign.**

**ARCHIVED ads are omitted from an unfiltered ad-level listing** — the same trap the
campaign level has. Without `effective_status IN [ACTIVE, PAUSED, ARCHIVED, DELETED,
CAMPAIGN_PAUSED, ADSET_PAUSED, PENDING_REVIEW, DISAPPROVED, WITH_ISSUES, IN_PROCESS]` the
main campaign came back **₹65,551.01 short**: `v5 - I am a med student` (₹54,351.01),
`influencer direct video ad - Rheumatoid Arthritis` (₹10,460.96), `influencer - 20oct`
(₹631.34) and `E3-06 My entire AI stack` (₹107.70) are all archived now.

**`Qualified Leads | Campaign` was appended as `CID[9]`,** never inserted, so no
historical `CAMP`, `ADS`, `DAILY`, `AGE` or `ADAY` row re-labelled itself. Its fifteen
never-delivered ads are carried as ₹0.00 rows in `ADS`, which is the same convention the
old iOS campaign's empty ads follow — it makes that campaign's creative table long.

## Campaigns not in the report

`India | Influencer Videos | App Registrations` (`120249585723440482`) is on the
account, PAUSED, and has **never delivered**: Meta returns no spend, impression or
result fields for it at all. So it is deliberately left out of `CID` and `CAMP`. Add it
the day it spends, **appended to the end of `CID`** so no historical row re-labels itself.
