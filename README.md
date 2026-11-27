# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 27 November 2026, 8:10 am IST.
  Self-contained: the figures are baked into the file, so it needs no network and no connector.
  Campaign rail, per-ad tables, day-by-day, age bands, and the GST-inclusive billing line.
  Defaults to the campaigns that are active; a switch shows all twelve.

## Where the account stands (27 Nov 2026)

**Lifetime spend is ₹6,08,361.70, up from ₹1,61,870.20 at the September snapshot** — nearly
four times as much across 51,058 installs and 23,504 registrations (₹11.92 and ₹25.88 each).

Almost all of that growth is one campaign. `India | MBBS Students - Doctors | App Downloads |
signups` (`120249183882130482`, index 5) went from ₹89,451.65 to **₹4,25,427.74** — 70% of
everything the account has ever spent — and **paused on 19 November**. It booked 40,640
installs and 18,968 registrations at **₹22.43 a registration**, comfortably the best
large-scale result on the account.

**Three new `Qualified Leads` campaigns now carry everything that is still running**, appended
to `CID` as indexes 9, 10 and 11:

| # | Campaign | Spend | Installs | Regs | Cost/reg | Status |
|---|---|---|---|---|---|---|
| 9 | `Qualified Leads \| Campaign` | ₹93,913.67 | 6,248 | 2,997 | ₹31.34 | ACTIVE |
| 11 | `Qualified Leads \| cost cap 30` | ₹13,527.14 | 1,153 | 487 | ₹27.78 | ACTIVE |
| 10 | `Qualified Leads \|instagram reel` | ₹2,932.50 | 40 | 17 | ₹172.50 | ACTIVE |

Active-only billing is **₹1,30,240.51** (₹1,10,373.31 media + ₹19,867.20 GST), 7,441 installs
at ₹14.83 and 3,501 registrations at ₹31.53.

**The instagram reel campaign is the outlier and is worth a look.** ₹2,932.50 for 17
registrations is ₹172.50 each — five and a half times the account's blended rate — on a 0.34%
CTR against 0.89% and 1.34% on the other two. It is small, but it is the only line on the
account priced like that.

**`cost cap 30` is doing what it was named for.** ₹27.78 a registration against ₹31.34 on the
main campaign, on a tenth of the spend, and it has only been delivering since 23 November.

`India | MBBS Students - influencers` (index 8) closed at ₹7,700.08 and last spent on
4 October. The nine campaigns that were in the report in September are all PAUSED; only the
three new ones deliver.

The last day in the data is a **part-day** and is held out of every window total and every
rate beside it. It was still moving while this snapshot was taken — see the note on drift
under *Refreshing*.

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

The September regime notes that used to sit here (the 24 Sep influencer campaign, the
27 Sep budget cut from ₹3,000 to ₹1,000 a day, the 28 Sep delivery stall and the prepaid
balance top-up) have been removed: every campaign they described is paused and the account
has since run a different set of campaigns at several times the budget. The day rows still
carry those days, so the charts show them; there is no point narrating them twice.

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

## An ad listing leaves out archived ads — and that hid ₹65,551.01

**This bit the report on 27 Nov 2026.** Campaign 5's creative rows summed to ₹3,59,876.73
against a campaign total of ₹4,25,427.74 — **₹65,551.01 missing, 15% of the campaign.** The
gap was four ads that are now `ARCHIVED` at Meta, and **an ad-level listing does not return
them unless you ask**:

| Archived ad | Spend | Regs |
|---|---|---|
| `v5 - I am a med student` | ₹54,351.01 | 1,734 |
| `influencer direct video ad - Rheumatoid Arthritis` | ₹10,460.96 | 313 |
| `influencer - 20oct` | ₹631.34 | 19 |
| `E3-06 My entire AI stack` | ₹107.70 | — |

`v5 - I am a med student` is **the single biggest creative on the account**. Dropping it
silently would have left the per-ad table understating the campaign by a seventh while every
figure in it still looked plausible.

**Always pull ad level with `effective_status IN ["ACTIVE","PAUSED","ARCHIVED","DELETED",
"CAMPAIGN_PAUSED","ADSET_PAUSED",...]`.** This is the same trap `halcyon-reports` hit with an
archived *campaign* in September; it applies at ad level too. The check that catches it is the
one already in this README: assert each campaign's `CAMP` total equals the sum of its `ADS`
rows. It is not optional.

## Campaigns not in the report

`India | Influencer Videos | App Registrations` (`120249585723440482`) is on the
account, PAUSED, and has **never delivered**: Meta returns no spend, impression or
result fields for it at all. So it is deliberately left out of `CID` and `CAMP`. Add it
the day it spends, **appended to the end of `CID`** so no historical row re-labels itself.

## Drift on the live days, and what is asserted

Three campaigns were delivering while this snapshot was taken, so independent reads of the
same day disagree. Measured on this refresh:

- `level: "campaign"` lifetime, pulled first, read ₹13,492.79 for `cost cap 30`; the day rows
  and the ad rows, pulled minutes later, both read ₹13,527.14. The whole ₹34.35 is 27 November.
- Ad-level day rows vs campaign day rows: **26 of 26 settled campaign-days agree exactly** on
  spend, installs and registrations. The only disagreements are 27 Nov (part-day) and 26 Nov,
  which is still inside Meta's ~48h revision window — ₹0.25 on `cost cap 30`.
- All nine paused campaigns reconcile to **₹0.00** across `CAMP`, `ADS` and `AGE`.

So `CAMP` is built from the day rows rather than from the campaign-level pull: that makes
`CAMP`, `ADS` and `DAILY` agree exactly for every campaign, and confines the drift to the
part-day the page already excludes. Reach is the exception — it is de-duplicated by Meta and
cannot be summed, so it is carried from the lifetime pull.

`ADAY` only needs to cover the recent window: the creative split uses
`Math.min(8, settled)` days, so it is refreshed for the last 8-18 days of each campaign that
was delivering, and the published history is kept beneath it. `ANAME` went from 8 names to 28
on this refresh — all 20 appended, the original 8 untouched and in place, so every historical
`ADAY` row still points at the creative that actually ran.
