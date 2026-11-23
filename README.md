# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 23 November 2026, 8:10 am IST.
  Self-contained: the figures are baked into the file, so it needs no network and no connector.
  Campaign rail, per-ad tables, day-by-day, age bands, and the GST-inclusive billing line.
  Defaults to the campaigns that are active; a switch shows all eleven.
  With only one campaign active, the page auto-focuses that campaign's tab — click **All** in
  the campaign rail to get the account-wide view and the ten-day rollup.

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

**State at the 23 Nov 2026 snapshot.** One campaign is live: **Qualified Leads | Campaign**
(`120250327482630482`), opened 6 Nov on Rs 8,000/day, at Rs 80,587.61 and 2,648 registrations
(Rs 30.43 each). It is `CID` index 9.

**India | MBBS Students - Doctors | App Downloads | signups** (`120249183882130482`, index 5) is
the campaign that carried the account from late August and is now PAUSED. It closes at
Rs 4,25,427.74 and 18,968 registrations (Rs 22.43 each) and last spent on 15 Nov. It is also
what the October step-up was: account spend went Rs 92,623.77 (Sep) -> Rs 2,68,262.50 (Oct),
peaking at Rs 14,846.11 on 4 Oct. **India | MBBS Students - influencers** (index 8) stopped on
3 Oct and is PAUSED.

**Qualified Leads | AI Videos | 21 Nov** (`120250608611250482`, index 10) ran only 21-22 Nov:
Rs 1,917.30, 11 registrations, Rs 174.30 each, already PAUSED. Appended to `CID` as index 10.

**14 Nov 2026 is an empty day, not a cheap one.** The whole account spent Rs 0.00 and served no
impressions, while late-attributed installs and registrations still landed against it
(campaign 5: 4 installs, 14 registrations; campaign 9: 9 and 17). Nothing is logged in the
activity log against that date — no status, budget or balance change. The same shape recurs on
16-19 Nov for campaign 5 after it stopped spending. Keep those rows: `DAILY` carries them with
zero spend so the day reads as empty rather than missing, and the page's "cheapest day" guard
already refuses to let them set a rate.

## Archived ads are hidden from an unfiltered ad listing

**This cost Rs 65,551.01 on the 23 Nov refresh.** An ad-level listing without an
`effective_status` filter silently omits ARCHIVED (and DELETED) ads, exactly as it does for
campaigns in `halcyon-reports`. On campaign 5 the ad rows came to Rs 3,59,876.73 against a
campaign total of Rs 4,25,427.74 — the missing Rs 65,551.01 was four archived ads:

| Ad | Spend |
|---|---|
| `v5 - I am a med student` | Rs 54,351.01 |
| `influencer direct video ad - Rheumatoid Arthritis` (the renamed `v4 - Ecg` id) | Rs 10,460.96 |
| `influencer - 20oct` | Rs 631.34 |
| `E3-06 My entire AI stack` | Rs 107.70 |

Always pass `effective_status IN ["ACTIVE","PAUSED","ARCHIVED","DELETED","CAMPAIGN_PAUSED",
"ADSET_PAUSED",...]` at **ad** level as well as campaign level, and reconcile ad rows against the
campaign total before writing the file. That reconciliation is what caught it.

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

On the 23 Nov 2026 refresh the check came back clean: `ads_account_get_activity_logs`
(`event_category: "ad"`, from 28 Sep) returned 282 entries and **not one `Ad name updated` or
`Ad updated`** — only status changes, review outcomes and 41 new ads. Every pre-existing name
still resolves to the same ad id, so no history moved.

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
account, PAUSED, and has **still never delivered** as at 23 Nov 2026: Meta returns no spend, impression or
result fields for it at all. So it is deliberately left out of `CID` and `CAMP`. Add it
the day it spends, **appended to the end of `CID`** so no historical row re-labels itself.
