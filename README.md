# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 7 October 2026, 8:05 am IST.
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

**India | MBBS Students - influencers** (`120249585873390482`) opened on 24 Sep 2026 on its
own budget alongside the original campaign and was **switched off at 12:15 pm on 3 Oct 2026**
(activity log, Active -> Inactive, actor Abhijeet Lenka). Its last spend was 3 Oct; it is now
PAUSED and closes at Rs 7,700.08 for 160 registrations. Only
`India | MBBS Students - Doctors | App Downloads | signups` (`120249183882130482`) is still live.

**October is a different regime — do not read it against September.** Budgets were cut to
Rs 1,000/day on 27 Sep, then raised again twice on the live campaign: **Rs 1,000 -> Rs 8,000/day
at 11:07 am on 3 Oct**, and **Rs 8,000 -> Rs 12,000/day at 12:10 pm on 4 Oct** (activity log,
Power Editor, actor Abhijeet Lenka). Rs 30,000 was added to the prepaid balance at 6:48 pm on
4 Oct. The ad set bid moved twice on the 3rd as well: to "Optimize bid for actions" with a
**Rs 30.00 cap** at 11:07 am, then back to automatic bidding with the cap removed at 8:01 pm.

**Eleven new creatives were created between 11:56 and 11:58 am on 3 Oct** — the `E3` set,
`E3-01` through `E3-10` (with `E3-08a` and `E3-08b`) — and they now carry nearly all delivery.
They are appended to `ANAME` at indexes 8-18 and to `ADS`. `E3-08a 3 medical apps to 1` alone
has taken Rs 23,566. **They are much cheaper per registration**: the page's own window
comparison reads Rs 32.26 across 29 Sep-2 Oct against **Rs 19.17 across 3 Oct-6 Oct**, 41% less,
and attributes it to 85% more registrations per 1,000 impressions rather than to price.
4 Oct is the account's largest day ever: Rs 14,846.11 for 849 registrations at **Rs 17.49** each.

`influencer - hasaan` was switched off in the main campaign at 9:25 pm on 26 Sep and is
PAUSED in `ADS`.

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
