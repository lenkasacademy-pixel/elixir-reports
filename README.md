# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 18 November 2026, 11:00 am IST.
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

## Where the account is now (18 Nov 2026)

**The account scaled roughly tenfold through October.** Lifetime spend is
**Rs 5,39,456.05** against Rs 1,54,870 at the September snapshot — 46,568 installs and
21,370 registrations. Daily account spend went from about Rs 1,100 at the start of
October to a peak of **Rs 14,846.11 on 4 October**, then settled into the
Rs 5,000–13,000 range.

`India | MBBS Students - Doctors | App Downloads | signups` (`120249183882130482`,
index 5) carried almost all of it: **Rs 4,25,428 lifetime**, 40,635 installs, 18,963
registrations. It ran until **15 November** and is now PAUSED.

**A new campaign is the only one delivering.** `Qualified Leads | Campaign`
(`120250327482630482`) opened **6 November** and is **appended to `CID` as index 9**, so
no historical row re-labels itself. It has taken **Rs 41,468 for 2,956 installs and
1,372 registrations** (Rs 30.22 per registration). It overlapped campaign 5 from the
6th to the 15th.

**14 November delivered nothing at all** — Rs 0.00 and zero impressions across the whole
account — while still booking installs and registrations attributed from earlier clicks.
That is why the day shows results against no cost. It is the same shape as 31 Aug in the
existing data; worth a glance at the prepaid balance, since the 18 Sep and 28 Sep stalls
both turned out to be balance-related.

**The creative roster turned over almost completely.** The `E3-` series (`E3-01` …
`E3-10`) and the dated `influencer` cuts (`influencer - 20oct`, `influencer 20oct – Copy`,
`influencer 27oct`, `influencer31st oct`, `new influencer 1nov`, `influencer3 nov`,
`sadaf`) all started in October or November — 18 names appended to `ANAME`. The biggest
single creative on the account is now `E3-08a 3 medical apps to 1` at Rs 1,26,820.
`influencer3 nov` is what moved campaign 9's cost per registration: it took 0% of spend
in the 9–12 Nov window and 90% in 13–17 Nov, and a registration went from Rs 38.08 to
Rs 24.71 across those windows.

### Historical: the September regime (kept for the day rows before 30 Sep)

A second campaign, **India | MBBS Students - influencers** (`120249585873390482`),
opened on 24 Sep 2026 on its own Rs 3,000/day budget alongside the original Rs 3,000/day
campaign. It stopped spending on **3 Oct** and is now PAUSED at Rs 7,700.08.

**Budgets were cut on 27 Sep 2026 at 7:36 am** — both campaigns went from Rs 3,000/day
to Rs 1,000/day, so the account cap fell from Rs 6,000/day to Rs 2,000/day. 27 Sep
settled at Rs 1,939.44 for 64 registrations, Rs 30.30 each, against Rs 41.74 on the 26th
at three times the spend. 28 Sep spent only Rs 709.48 because delivery thinned all day
and all but stopped from 3 pm to 9 pm, resuming right after Rs 30,000 was added to the
prepaid balance at 9:32 pm. A stall like that looks like a cheap day in the tables.
`influencer - hasaan` was switched off in the main campaign at 9:25 pm on 26 Sep. That
whole regime was superseded by the October scale-up; do not read October against it.

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

## An ARCHIVED ad disappears from an ordinary ad listing

**This bit the report on 18 Nov 2026.** Four ads in campaign 5 — including
`v5 - I am a med student` at **Rs 54,351.01**, the account's second-biggest creative at
the time — had been **archived**, and a plain `level: "ad"` listing simply did not
return them. Nothing errored. `v5`, `influencer direct video ad - Rheumatoid Arthritis`,
`influencer - 20oct` and `E3-06 My entire AI stack` were all missing, about **Rs 65,500**
of ad-level spend, and the first guess looked like the 23 Sep rename trap: a familiar
name gone from the ad rows.

It was not a rename. **Always pass an `effective_status` filter that includes
`ARCHIVED` and `DELETED`** at ad level, exactly as `halcyon-reports` already does at
campaign level:

    filtering: [{"field":"effective_status","operator":"IN",
                 "value":["ACTIVE","PAUSED","ARCHIVED","DELETED","CAMPAIGN_PAUSED",
                          "ADSET_PAUSED","AD_PAUSED","DISAPPROVED","WITH_ISSUES",
                          "IN_PROCESS","PENDING_REVIEW","PREAPPROVED",
                          "PENDING_BILLING_INFO"]}]

The tell that it is archiving and not a rename: with archived ads included, every
campaign's ad rows sum back to its `CAMP` total exactly, and no new name is carrying a
disappeared ad's history. Check that before concluding anything about a rename.

**How to tell the two apart, in order:**

1. Pull ad level with the archived/deleted statuses included. If the missing name comes
   back, it was archived — keep its row, nothing is renamed.
2. If it does not come back, pull ad level with a `time_range` ending at the previous
   snapshot date. A renamed ad returns its **old figures under its new name**, which
   identifies the pair unambiguously.
3. Only then go to `ads_account_get_activity_logs` to see whether the creative changed
   too.

## Campaigns not in the report

`India | Influencer Videos | App Registrations` (`120249585723440482`) is still on the
account, PAUSED, and has **never delivered**: Meta returns no spend, impression or
result fields for it at all. So it is deliberately left out of `CID` and `CAMP`. Add it
the day it spends, **appended to the end of `CID`** so no historical row re-labels itself.
