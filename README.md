# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 12 October 2026, 8:05 am IST.
  Self-contained: the figures are baked into the file, so it needs no network and no connector.
  Campaign rail, per-ad tables, day-by-day, age bands, and the GST-inclusive billing line.
  Defaults to the campaigns that are active; a switch shows all nine.

  **With only one campaign still active, the page now opens on that campaign's tab,
  not the all-campaigns sheet.** `autoTab()` has always done this
  (`if (!state.picked && state.activeOnly && list.length === 1)`), it just never
  fired before, because two campaigns were live. So "The last 10 days" card is one
  click away on load - press **All campaigns** in the rail to see it. This is designed
  behaviour, not a regression; it was A/B-checked against `main` before publishing. If
  it is the wrong default for a one-campaign account, that is a change to `autoTab()`,
  not to the data.

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
not a budget change on the first. It ran the same three influencer creatives and only
`Influencer - 11sep` ever got real delivery in it. **It is now PAUSED** and closed at
₹7,700.08 for 160 registrations — ₹48.13 each, against ₹24.55 on the campaign that is
still running. It is the account's dearest line and stopping it was the right call.

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

## What changed at this refresh (12 Oct 2026)

Previous snapshot: 29 Sep 2026, 11:00 am IST. Thirteen days of new delivery, and the
account is a different size than it was.

**Budgets went up six-fold, then half again.** The 27 Sep cut to ₹1,000/day held for
five days. Then at **11:07 am on 3 Oct** the live campaign went ₹1,000 → **₹8,000/day**
(activity log, api source `ads MCP server`), and at **12:10 pm on 4 Oct** ₹8,000 →
**₹12,000/day** (`Ads Manager App for iOS`). It has billed around ₹13,000/day since,
funded by **₹1,70,000 of October top-ups** — ₹30,000 on the 4th, ₹70,000 on the 7th,
₹70,000 on the 12th. Nothing before 3 Oct is comparable to anything after it; the
27 Sep "different regime" note below now applies twice over.

**Eleven new creatives, and one of them is already the biggest ad on the account.**
Eleven `E3-*` ads were created on **3 Oct between 11:56 and 11:58 am** and were live by
12:08 pm; `Influencer - 11sep` was switched off six minutes later at 12:14 pm. They are
appended to `ANAME` at indexes 8-18 and the first eight entries are untouched.

| New creative | spend | registrations | each |
|---|---|---|---|
| `E3-08a 3 medical apps to 1` | ₹60,788.86 | 3,133 | ₹19.40 |
| `E3-09` | ₹24,755.27 | 1,232 | ₹20.09 |
| `E3-10` | ₹15,983.37 | 770 | ₹20.76 |
| `E3-08b 5 apps on rounds to 1` | ₹841.45 | 34 | ₹24.75 |
| the other seven | ₹743.65 between them | 7 | - |

`E3-08a` has passed `v5 - I am a med student` (₹54,343.79 lifetime) to become the
largest single creative in the account's history, in nine days.

**Cost per registration is rising as the spend scales.** ₹19.95 across the nine settled
days in the window, but it was ₹17.47 on 4 Oct and ₹20.52 by the 11th. Splitting the run
in half: **₹18.24 (4-7 Oct) against ₹20.94 (8-11 Oct), +15%**. The creative split labels
**all four spending creatives *response fading*** rather than *impressions costlier* —
impressions got 2% cheaper over the same span while registrations per 1,000 impressions
fell 14%. That is the pattern a new creative fixes, not a bidding one, and it is showing
up nine days into creatives that are themselves brand new.

**No ad was renamed or re-created** between 29 September and this snapshot, other than
the eleven new ones. The activity log was checked for exactly the creative-swap-behind-a-
rename case described below; there is none. The two 22 Sep creative splits are retained
unchanged.

**One campaign is still spending.** `India | MBBS Students - influencers` is PAUSED (see
above), so eight of the nine campaigns in `CAMP` are now closed and settled.

Account lifetime across all nine campaigns: **₹2,70,431.22 of media, 21,989 installs,
9,094 registrations**. The active campaign alone: ₹1,97,870.57 media / **₹2,33,487.27
billed** including 18% GST, 19,012 installs at ₹10.41, **8,059 registrations at
₹24.55**.

## Reconciliation on this refresh

**Eight of the nine campaigns reconcile exactly** — their `ADS`, `DAILY` and `AGE` rows
each sum to the campaign's lifetime `CAMP` figures on spend, impressions, installs and
registrations, to the rupee.

**The live campaign cannot, and the reason is measurable.** Its lifetime read came back
₹1,97,842.51 on one call and **₹1,97,921.77 a few minutes later** — **+₹79.26**, about
₹9 a minute of live burn. A lifetime total pulled at one instant and a set of day rows
pulled at another will never agree while a campaign is spending, so:

- **`CAMP[5]` is derived from the `DAILY` sums**, not from the campaign lifetime read:
  ₹1,97,870.57 / 44,32,903 impressions / 40,615 clicks / 19,012 installs / 8,059
  registrations. Reach (967,385) is kept from the campaign read because it cannot be
  summed, and CTR 0.92 / CPM 44.64 are recomputed from the derived figures. The campaign
  rail therefore reconciles exactly with the day-by-day table underneath it, which is
  what a reader can actually check.
- Residuals against that basis, left visible rather than plugged: `AGE` is exact on spend
  and impressions, **+2 installs / +3 registrations**. `ADS` is **-₹20.86 / -458
  impressions / +1 install / +2 registrations**.

**`sum(ADAY) == DAILY` holds for every settled day from 30 Aug to 11 Oct** on spend,
installs and registrations. 11 Oct carries **₹0.33** of per-ad rounding drift across
twelve ads; the assertion tolerance is ₹1 for that reason. The 12 Oct part-day differs
as expected and is excluded everywhere (`ADAY` ₹2,325.59 vs `DAILY` ₹2,302.27).

**A long-paused campaign gained a registration after the fact.** Campaign index 0 moved
from 183 to 184 lifetime registrations, in the 25-34 band (72 → 73), having not spent in
weeks. The first run of this refresh only rebuilt `AGE` for the two campaigns thought to
be moving and the assertion caught it. **Rebuild `AGE` for all nine campaigns every
refresh** — late attribution reaches campaigns that stopped long ago.

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
