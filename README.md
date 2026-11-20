# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 20 November 2026, 8:10 am IST.
  Self-contained: the figures are baked into the file, so it needs no network and no connector.
  Campaign rail, per-ad tables, day-by-day, age bands, and the GST-inclusive billing line.
  Defaults to the campaigns that are active; a switch shows all ten.

## Status at 20 Nov 2026

Lifetime media spend is **₹5,56,436.29** (₹6,56,594.82 with GST) for **47,869 installs** and
**21,996 registrations** — **₹25.30 a registration** across every campaign that has ever spent.
Settled days only (20 November held out) are ₹5,55,336.86 and 21,964 registrations.

**October was the account's real scale-up.** `India | MBBS Students - Doctors | App Downloads |
signups` took **₹2,68,221.65 in October alone**, against ₹84,964.54 in September, and now carries
₹4,25,427.74 of the account's lifetime at **₹22.43 a registration** — still the cheapest line of
any size on the account.

**That campaign is now PAUSED.** Its last day with any delivery was **15 November**; 16–19 November
are real zero-spend rows carrying only registrations attributed back to earlier clicks. Its daily
budget reads ₹3,500, up from the ₹1,000 it was cut to on 27 September.

**A new campaign has taken over.** `Qualified Leads | Campaign` (`120250327482630482`) opened
**6 November** on **₹8,000/day** and is the only ACTIVE campaign: **₹58,447.90, 4,252 installs,
1,994 registrations, ₹29.31 each**. It was appended to the end of `CID` as index **9** so no
historical row re-labels itself. Its ad set is a copy of the signups campaign's, so five creative
names appear in both campaigns; `ADS`/`ADAY` key on campaign index, so that is not a collision.

**14 November was an account-wide stop.** Nothing was billed and nothing served on any campaign —
₹0, 0 impressions at `level: "ad_account"` — yet **13 installs and 31 registrations** were booked
against the day from earlier clicks. The day-by-day table shows it as what it is. A day like that
is not a cheap day, and the existing "cheapest day" guard (at least half the window's average
settled spend) already keeps it out of that footnote. Worth asking the client about: a one-day
total stop with delivery normal either side usually means the prepaid balance ran out.

`India | MBBS Students - influencers` ran **24 Sep – 4 Oct** for ₹7,700.08 (160 registrations,
₹48.13 each) and is paused. `India | Influencer Videos | App Registrations`
(`120249585723440482`) still has **never delivered** and is still deliberately out of `CID`.

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
while the charts above it move. It now covers campaigns 5, 8 and 9 gap-free across their whole
lives — 606 rows.

## Age bands cannot account for every result (added 20 Nov 2026)

Meta's `age` breakdown reconciles to the campaign **exactly on spend, impressions and clicks**,
but it cannot put an age on every conversion. On this refresh the signups campaign's bands total
**46 registrations short** of the campaign's own 18,967, and six other campaigns are short by one
to four installs.

**Neither side is adjusted.** The age card's footnote now *computes* the gap from `CAMP` and `AGE`
and names it, so the total row can never disagree with the campaign header in silence — and the
sentence disappears on its own when there is no gap. Do not hand-write or hand-remove it, and do
not pad a band to make the totals tie.

Meta also returned an `Unknown` age bucket for `India | MBBS Students - influencers` (0 spend,
0 impressions, 1 registration). Buckets named `Unknown` or `null` are dropped when building `AGE`
— they carry no spend and would render as a band that nobody is in. The registration they carry is
part of the gap the footnote reports.

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

**Both splits were re-verified on 20 Nov** with ranged pulls on the two ad ids, and both halves
still reconcile to the ad's lifetime to the paisa:

| Ad id | Window | Row | Spend |
|---|---|---|---|
| `120249212142170482` | to 15 Sep | `v4 - Ecg` (REPLACED) | ₹10,361.32 |
| `120249212142170482` | from 16 Sep | `influencer direct video ad - Rheumatoid Arthritis` | ₹99.64 |
| `120249183882150482` | to 21 Sep | `v1 - male ( asking medicos how they study)` (REPLACED) | ₹1,076.22 |
| `120249183882150482` | from 22 Sep | `influencer - hasaan` | ₹413.03 |

Lifetime on the first ad is ₹10,460.96 and on the second ₹1,489.25 — each the exact sum of its two
rows. **No ad was renamed or re-creatived between 29 September and 20 November**: the ad-category
activity log for that window holds 267 events, all of them status changes, review results and
ad creations.

## ARCHIVED ads vanish from an unfiltered pull (added 20 Nov 2026)

Four ads appeared to have disappeared from the account on this refresh, including
`v5 - I am a med student`, the single biggest creative in the report. They had not. **An
unfiltered `level: "ad"` listing omits ARCHIVED ads**, exactly as it does at campaign level.
The activity log showed no rename and no creative swap, which is what ruled out the trap above.

Always pass an explicit status filter at ad level:

    filtering: [{field:"effective_status", operator:"IN",
                 value:["ACTIVE","PAUSED","ARCHIVED","DELETED","CAMPAIGN_PAUSED","ADSET_PAUSED",
                        "IN_PROCESS","WITH_ISSUES","PENDING_REVIEW","DISAPPROVED",
                        "PREAPPROVED","PENDING_BILLING_INFO"]}]

The same applies to `filtering` on `campaign.id` for **ad-level day rows** — that also drops
ARCHIVED ads, so the four archived ads in campaign 5 need their own `object_ids` pull or `ADAY`
silently under-sums `DAILY`.

## The live day moves while you build (verified again 20 Nov 2026)

`Qualified Leads | Campaign` read **₹58,352.59**, then **₹58,424.44**, then **₹58,447.90**, then
**₹58,472.66** across four pulls in the same session — roughly ₹25 a minute of part-day accrual.
Chasing it is pointless.

**The rule: day rows are the source of truth.** `CAMP[9]` is the sum of its own `DAILY` rows, not
the campaign-level lifetime read, and its `AGE` rows and ad-level day rows were taken in the same
moment, so all four agree to the paisa. Only two fields on the live campaign come from a slightly
later read because they cannot be summed or were not requested per day: per-ad **reach** and per-ad
**link clicks**. Neither is reconciled anywhere and neither appears in a total.

### Checks that passed on this refresh

- `CAMP` equals the sum of its `ADS` rows and of its `DAILY` rows on spend, impressions, clicks,
  installs **and** registrations, for all ten campaigns.
- `CAMP` equals the sum of its `AGE` rows on spend, impressions and clicks for all ten
  (registrations and installs: see the age section above).
- `ADAY` equals `DAILY` per campaign per settled day on spend, installs and registrations, for
  every day of campaigns 5, 8 and 9.
- `ADS` is sorted descending by spend, asserted.
- **`DAILY` summed by day equals the account's own daily spend and impressions on 111 of 113 days.**
  The two exceptions: 19 Nov is ₹0.02 / 2 impressions apart (campaign-rollup rounding) and 20 Nov,
  the part-day, is ₹52.76 / 1,391 impressions apart because the account read was taken last.

## Campaigns not in the report

`India | Influencer Videos | App Registrations` (`120249585723440482`) is on the
account, PAUSED, and has **never delivered**: Meta returns no spend, impression or
result fields for it at all. So it is deliberately left out of `CID` and `CAMP`. Add it
the day it spends, **appended to the end of `CID`** so no historical row re-labels itself.

## Refreshing

Patch the arrays and the four snapshot stamps, nothing else.

| Array | Row shape |
|---|---|
| `CID` | campaign ids; **never reshuffle, only append** |
| `CAMP` | `[cidIdx, name, status, spend, imp, reach, clicks, ctr, cpm, installs, regs]` |
| `ADS` | `[name, cidIdx, status, spend, imp, reach, clicks, ctr, cpm, link_click, installs, regs]`, spend desc |
| `DAILY` | `["MM-DD", cidIdx, spend, imp, reach, clicks, ctr, cpm, installs, regs]` |
| `AGE` | `[band, cidIdx, spend, imp, reach, clicks, ctr, cpm, installs, regs]` |
| `ADAY` | `["MM-DD", cidIdx, anameIdx, spend, imp, clicks, installs, regs]` |
| `ANAME` | ad names; rename **in place**, append only genuinely new creatives |

The snapshot stamp appears **four times** in `index.html` — the title comment, the masthead stamp,
the data-block comment and the footer line — and once here. `SNAP_DAY` is the live part-day.

The footer is the one place in this report that carries hand-written prose. Everything above it is
rendered from the arrays, so **the footer is the only thing that can go stale** — reread it on every
refresh.

The page carries `noindex, nofollow` so it stays out of search results.

A live version of the same report — one that queries Meta each time it is opened — is kept
as a private Claude artifact rather than here.
