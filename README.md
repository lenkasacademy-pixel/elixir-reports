# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 2 November 2026, 8:20 am IST.
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

**October rebuilt the account, and it is now a one-campaign report.**
`India | MBBS Students - Doctors | App Downloads | signups` (`120249183882130482`) is the
only campaign still spending. October took **Rs 2,68,262.50 for 13,301 registrations,
Rs 20.17 each** - the account's biggest month and its cheapest, against Rs 92,623.77 at
Rs 33.68 in September and Rs 68.21 in August. Spend stepped up on 4 Oct (Rs 14,846.11 against
Rs 1,143.27 on the 1st) and held roughly Rs 4,500-13,500 a day thereafter.

**Sixteen ads were created in October** - the ten-strong `E3-*` series plus dated influencer
cuts (`influencer 20oct - Copy`, `influencer 27oct`, `influencer31st oct`, `new influencer 1nov`,
`influencer - 20oct`). They are **appended to `ANAME` at indexes 8-23**; indexes 0-7 are
untouched so every historical `ADAY` row still points at the creative that actually ran.
`E3-08a 3 medical apps to 1` is now the largest creative on the account at Rs 1,26,819.49 for
6,050 registrations. **No ad was renamed and no creative was swapped in this period** - the
activity log (`event_category: "ad"`, 29 Sep - 2 Nov) carries only `Ad created` and
`Ad status updated` events, no `Ad name updated` and no `Ad updated`.

**`v5 - I am a med student` and the Rheumatoid ad are now ARCHIVED**, so they vanish from an
unfiltered ad listing - see the archived-ads trap below.

**`India | MBBS Students - influencers` (`120249585873390482`) is PAUSED.** It last delivered
in volume on 30 Sep, trickled to a stop on 3 Oct, and closes at Rs 7,700.08 / 160 registrations.
Its three ad rows still sum to the campaign total to the paisa.

*History, kept because it explains the shape of September:* the influencer campaign opened on
24 Sep 2026 on its own Rs 3,000/day alongside the original Rs 3,000/day campaign, which is why
account spend roughly doubled that day. **Budgets were cut on 27 Sep 2026 at 7:36 am** - both
campaigns from Rs 3,000/day to Rs 1,000/day (activity log, Power Editor, actor Abhijeet Lenka).
28 Sep spent only Rs 712.22 because delivery thinned all day and all but stopped from 3 pm to
9 pm, resuming after Rs 30,000 was added to the prepaid balance at 9:32 pm. At 7:34 am the
influencer ad set `120249585873460482` also moved to "Optimize bid for actions" with a
**Rs 19.00 bid cap**. `influencer - hasaan` was switched off in the main campaign at 9:25 pm on
26 Sep and has not delivered since; it is PAUSED in `ADS`.

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

## Two pull traps that will silently corrupt a refresh

**1. An ad-level daily pull with too many `object_ids` returns zeros, it does not drop rows.**
On this refresh, `level: "ad"` + `time_increment: "1"` + 25 ad ids over 33 days returned all
825 rows and every id was present - but **most ads came back with `amount_spent` 0 and
`impressions` 0**. `E3-09` (Rs 53,243.63), `E3-10` (Rs 30,288.40) and `influencer 20oct - Copy`
(Rs 49,169.54) all read zero. This is nastier than the row cap in `halcyon-reports`, which drops
ids you can check for. **Keep each ad-level daily call to ~6 ids (under ~200 ad-days) and
cross-check every ad's window total against an un-incremented pull of the same window.** That
cross-check is what caught it.

**2. ARCHIVED ads are omitted from an unfiltered ad listing.** Campaign 5's ad rows came to
Rs 65,551.01 less than its campaign total until `effective_status IN ["ARCHIVED","DELETED"]`
was added - `v5 - I am a med student` (Rs 54,351.01), `influencer direct video ad - Rheumatoid
Arthritis` (Rs 10,460.96), `influencer - 20oct` (Rs 631.34) and `E3-06` (Rs 107.70) were all
archived. Same trap as `halcyon-reports` has for campaigns. **Always run the archived pull and
reconcile ad sums against `CAMP` before writing the file.**

**Pull the headline and the daily series close together.** `CAMP`/`ADS`/`AGE` (lifetime) and
`DAILY`/`ADAY` (per day) are separate reads, and while campaign 5 is spending the part-day moves
between them - it moved Rs 25.12 in the minutes between two reads on this refresh. Pull the last
two days of `DAILY` and `ADAY` in the *same* batch: done that way they reconciled exactly on
spend, impressions, installs and registrations for both 1 and 2 Nov. Residual drift of
`DAILY`/`AGE` against the `CAMP` header (here +Rs 17.23 and +Rs 7.83) is the live part-day and is
what the page's own footnote describes.

## Campaigns not in the report

`India | Influencer Videos | App Registrations` (`120249585723440482`) is on the
account, PAUSED, and has **never delivered**: Meta returns no spend, impression or
result fields for it at all. So it is deliberately left out of `CID` and `CAMP`. Add it
the day it spends, **appended to the end of `CID`** so no historical row re-labels itself.
