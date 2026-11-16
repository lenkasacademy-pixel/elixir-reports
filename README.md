# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 16 November 2026, 8:55 am IST.
  Self-contained: the figures are baked into the file, so it needs no network and no connector.
  Campaign rail, per-ad tables, day-by-day, age bands, and the GST-inclusive billing line.
  Defaults to the campaigns that are active; a switch shows all ten.

## Where the account stands at 16 Nov 2026

**The account has spent ₹5,28,476.98 for 20,921 registrations — ₹25.26 each, against
₹43.27 at the 29 September snapshot.** Lifetime spend is 3.3x what it was seven weeks
ago and the cost per registration has nearly halved. Billed, with 18% GST, that is
**₹6,23,602.84**.

| Campaign | Spend | Regs | Each | Status |
|---|---|---|---|---|
| MBBS Students - Doctors \| signups | ₹4,25,427.48 | 18,956 | **₹22.44** | PAUSED |
| **Qualified Leads \| Campaign** (new) | ₹30,488.85 | 930 | ₹32.78 | **ACTIVE** |
| MBBS Students \| ios | ₹23,042.48 | 184 | ₹125.23 | PAUSED |
| MBBS Students \| Playstore | ₹15,335.76 | 314 | ₹48.84 | PAUSED |
| Doctors \| Playstore – Copy | ₹10,648.88 | 151 | ₹70.52 | PAUSED |
| MBBS Students - influencers | ₹7,700.08 | 160 | ₹48.13 | PAUSED |
| MBBS Students \| Playstore | ₹7,089.11 | 146 | ₹48.56 | PAUSED |
| Doctors \| App store | ₹7,014.39 | 79 | ₹88.79 | PAUSED |
| MBBS Students \| ios | ₹1,331.18 | — | — | PAUSED |
| App Downloads \| Playstore \| v2 | ₹398.77 | 1 | ₹398.77 | PAUSED |

**One campaign is the whole account.** `India | MBBS Students - Doctors | App Downloads |
signups` went from ₹89,451.65 to **₹4,25,427.48** — 4.8x — and took its cost per
registration from ₹32.89 to **₹22.44**. It ran hardest from 4 to 19 October, above
₹10,000 on most of those days, peaking at ₹14,846.11 on 4 Oct. It tapered through
November and stopped after the 15th. **It is now PAUSED**, which means the account's
cheapest line is switched off.

**The only live campaign is new and dearer.** `Qualified Leads | Campaign`
(`120250327482630482`) opened 6 November, has spent ₹30,488.85 for 930 registrations at
**₹32.78** — 46% above the campaign it followed. It is the sole ACTIVE line, so it alone
sets what the account pays from here. Worth a human look.

**The creative that is carrying it:** `influencer3 nov` and `new influencer 1nov` are the
two ads with real delivery in it; six of its twenty ads have ever spent, and twelve are at
zero.

**Where the money actually went, account-wide:**

| Creative | Campaign | Spend | Regs | Each |
|---|---|---|---|---|
| E3-08a 3 medical apps to 1 | signups | ₹1,26,819.57 | 6,053 | ₹20.95 |
| influencer 20oct – Copy | signups | ₹59,320.80 | 3,566 | **₹16.64** |
| v5 - I am a med student | signups | ₹54,351.01 | 1,734 | ₹31.34 |
| E3-09 Asking medicos which AI app they trust | signups | ₹53,243.63 | 2,451 | ₹21.72 |
| new influencer 1nov | signups | ₹49,266.47 | 2,286 | ₹21.55 |
| E3-10 Rounds with the strictest senior | signups | ₹33,605.52 | 1,466 | ₹22.92 |

`influencer 20oct – Copy` is the best creative the account has ever run at scale —
**₹16.64 a registration across ₹59,320.80** — and it is paused with its campaign.
`v5 - I am a med student`, the workhorse of the September report, is now ARCHIVED and
finished at ₹31.34, nearly twice the cost of the October influencer cuts.

**14 November took no money at all** — the whole account spent ₹0 and still booked 31
registrations, because a registration is credited to the day of the click that earned it.
The page shows that day with a zero spend and a real result count; it is not an error.

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

A second campaign, **India | MBBS Students - influencers** (`120249585873390482`),
opened on 24 Sep 2026 on its own ₹3,000/day budget alongside the original ₹3,000/day
campaign. Account spend roughly doubled that day — that is a deliberate second campaign,
not a budget change on the first. It runs the same three influencer creatives, and
only `Influencer - 11sep` ever had real delivery in it. **It last delivered on 3 Oct 2026**
and closed at ₹7,700.08 for 160 registrations.

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
**₹19.00 bid cap**. Anything from 27 Sep onward is a different regime; do not read it
against the ₹6,000/day days. `influencer - hasaan` was switched off in the main campaign
at 9:25 pm on 26 Sep and is now PAUSED in `ADS`.

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

## Ad-level reads silently drop ARCHIVED ads

**This bit the 16 Nov 2026 refresh.** An `ads_get_ad_entities` call at `level: "ad"`
with no `effective_status` filter returns only the ads Meta considers current. Four
archived ads in the signups campaign — including `v5 - I am a med student` at
**₹54,351.01** — were missing, and the ad rows came out **₹65,551.01 short** of the
campaign's own lifetime total. The campaign-level number was right; the ad table was
not, and nothing in the response said anything was missing.

It is the same shape of bug as the rename above: every figure returned is real, so a
sloppy reconciliation passes. **Always pass the full status list on ad-level and
campaign-level reads:**

    filtering: [{ field: "effective_status", operator: "IN", value: [
      "ACTIVE","PAUSED","CAMPAIGN_PAUSED","ADSET_PAUSED","ARCHIVED","DELETED",
      "PENDING_REVIEW","DISAPPROVED","PREAPPROVED","PENDING_BILLING_INFO",
      "IN_PROCESS","WITH_ISSUES"] }]

and then assert `sum(ADS) == CAMP` per campaign before writing the file. That assertion
is the only thing that catches it.

## Re-pull history, do not append to it

`DAILY` and `ADAY` are **not** append-only. Meta keeps attributing results to old days
long after they settle: between the two snapshots it credited **one more registration to
22 September**, seven weeks after the fact. Appending new days alone left `sum(DAILY)`
one registration short of `CAMP`, and the ad-day rows short by the same one.

So on each refresh, re-pull the whole series for any campaign whose totals moved rather
than only the new days. The 16 Nov refresh re-pulled the signups campaign's `DAILY` from
27 Aug and its `ADAY` from 18 Sep; campaigns whose lifetime totals were byte-identical to
the published ones were left alone.

**The 200-row cap bites here.** `time_increment: "1"` at ad level returns one row per ad
per day including empty ones, so a 25-ad campaign caps out at 8 days per call. Split by
date range and check that every day came back with the full ad count before trusting it.

## The part-day is the only thing allowed to disagree

At 16 Nov 2026 the account reconciled **exactly** — `CAMP`, `ADS`, `DAILY` and `AGE`
agree to the paisa on all ten campaigns, and campaign-day sums match the
`level: "ad_account"` daily totals on spend and impressions for all 108 settled days.

The one exception is `SNAP_DAY` itself. The live campaign was spending about ₹15 a
minute while the pulls ran, so a lifetime pull taken two minutes after an ad-level pull
disagrees with it. The fix is to compose the live campaign's lifetime arrays out of two
pieces — a settled pull to the day before `SNAP_DAY`, plus a single `SNAP_DAY` slice
pulled once and reused for `CAMP`, `ADS`, `AGE`, `DAILY` and `ADAY` alike. Do not pull
each view's lifetime separately and hope they land together; they will not.

Reach is the exception to the exception: Meta de-duplicates it, so it is carried
per row from Meta and never summed or asserted.

## Campaigns not in the report

`India | Influencer Videos | App Registrations` (`120249585723440482`) is on the
account, PAUSED, and has **never delivered**: Meta returns no spend, impression or
result fields for it at all. So it is deliberately left out of `CID` and `CAMP`. Add it
the day it spends, **appended to the end of `CID`** so no historical row re-labels itself.

Still true at 16 Nov 2026 — it has never spent a rupee.

`Qualified Leads | Campaign` (`120250327482630482`) *did* spend, so on 16 Nov 2026 it was
appended as **`CID[9]`**. The first nine ids were left exactly where they were; every
`CAMP`, `ADS`, `DAILY`, `AGE` and `ADAY` row that pointed at an index still points at the
same campaign.
