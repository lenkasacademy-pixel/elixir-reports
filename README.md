# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 17 November 2026, 8:15 am IST.
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

## Where the account is now (17 Nov 2026)

**October was a step change in scale.** `India | MBBS Students - Doctors | App Downloads |
signups` (`120249183882130482`) ran at roughly ₹1,000/day to 2 Oct, then
₹11,000–₹14,000/day from 4 Oct, and has taken **₹4,25,427.74** against ₹89,451.65 at the
previous snapshot — 18,962 registrations at ₹22.44. It is now **PAUSED**; its last spending
day was 15 November. `India | MBBS Students - influencers` (`120249585873390482`) stopped on
3 October at ₹7,700.08 and is paused too.

**The live campaign is `Qualified Leads | Campaign` (`120250327482630482`)**, which opened on
6 November and now carries the account on its own — ₹35,121.34, 1,126 registrations,
₹31.19 each. It was **appended to the end of `CID` as index 9**, so no historical row
re-labels itself. It is the only ACTIVE campaign, so it is the only one with a creative split
under its trend card.

**14 November booked no delivery at all** — zero spend and zero impressions on every campaign —
yet Meta still attributed 13 installs and 31 registrations to it from earlier clicks. It is kept
as a real zero-spend day (the same treatment as `["08-31",3,0.0,...,3,1]`), not dropped as a gap.
16 and 17 November have the same shape on the paused `signups` campaign. Do not read a
zero-spend day's cost per registration — the rollup's "cheapest day" guard already ignores it.

## An archived ad disappears from an ordinary ad listing

**This bit the report on 17 Nov 2026.** Four ads on `signups` had been **archived** at Meta, and
`ads_get_ad_entities` at `level: "ad"` does not return them unless you ask for them:

| Ad | Spend | Status |
|---|---|---|
| `v5 - I am a med student` | ₹54,351.01 | ARCHIVED |
| `influencer direct video ad - Rheumatoid Arthritis` | ₹10,460.96 | ARCHIVED |
| `influencer - 20oct` | ₹631.34 | ARCHIVED |
| `E3-06 My entire AI stack` | ₹107.70 | ARCHIVED |

That is **₹65,551 of real spend**, 15% of the campaign, missing from `ADS` with no error of any
kind — the ad rows simply would not have summed to `CAMP`. Same trap as the archived *campaign*
in `halcyon-reports`. **Always pass**
`effective_status IN ["ACTIVE","PAUSED","ARCHIVED","DELETED","CAMPAIGN_PAUSED","ADSET_PAUSED",...]`
on every ad-level and campaign-level listing. The campaign-vs-`ADS` reconciliation is what
catches it; it is not optional.

Note the sibling trap: when an ad's name changes at Meta, a *historical* ad-level day pull
returns the **new** name over the whole history. `ADAY` therefore maps the two swapped ad ids
(`120249212142170482`, `120249183882150482`) to their retired `ANAME` entries for every day
before 22 Sep 2026 and to the new ones from the 22nd on. Keep that date-aware mapping.

**Meta is still revising September.** This refresh moved 22 Sep's registrations on `signups`
from 33 to 34, and campaign `India | MBBS Students | ios`'s 25–34 age band from 72 to 73 —
both months after the fact. Re-pull the whole series, never append.

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
