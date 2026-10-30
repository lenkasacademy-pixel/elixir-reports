# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 30 October 2026, 8:15 am IST.
  Self-contained: the figures are baked into the file, so it needs no network and no connector.
  Campaign rail, per-ad tables, day-by-day, age bands, and the GST-inclusive billing line.
  Defaults to the campaigns that are active; a switch shows all nine.
  One campaign is active as of this snapshot.

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

**October was a deliberate ten-fold scale-up of one campaign**, and it got cheaper
rather than dearer. `India | MBBS Students - Doctors | App Downloads | signups`
(`120249183882130482`) is the only campaign still running. Its daily budget went
**₹1,000 → ₹8,000 at 11:07 am on 3 Oct**, **→ ₹12,000 on 4 Oct at 12:10 pm**, and
**→ ₹6,000 at 10:14 am on 20 Oct** (activity log, `event_category: "budget"`, actor
Abhijeet Lenka — the first change through the ads MCP server, the other two through
Ads Manager for iOS). The prepaid balance was topped up ₹30,000 on 4 Oct and ₹70,000
on each of the 7th, 12th, 17th and 25th.

| | spend | registrations | cost per registration |
|---|---|---|---|
| everything to 30 Sep | ₹91,082.08 | 2,761 | ₹32.99 |
| **1–29 Oct (settled)** | **₹2,54,564.14** | **12,774** | **₹19.93** |

Cost per install fell with it, to ₹9.64 over 1–29 Oct. The biggest single day was
**4 Oct: ₹14,846.11 for 850 registrations**.

**The cause is a new creative batch.** Ten ads named `E3-01` … `E3-10` were created on
**3 Oct at ~11:58 am** and went live that afternoon (activity log, `Ad created`).
`E3-08a 3 medical apps to 1` is now the **largest single ad the account has ever run**
— ₹1,23,614.33 at ₹20.72 a registration. `influencer 20oct – Copy`, created 20 Oct,
is the cheapest line of any size at **₹14.15**, and is the one still carrying most of
the spend. `influencer 27oct` was added on 28 Oct and has ₹2,311.03 so far.

**Two ads were deleted on 15 Oct at 7:39 am** — `v5 - I am a med student`, the previous
mainstay (₹54,351.01 lifetime, ₹31.34 a registration), and `E3-06 My entire AI stack`.
Meta reports deleted ads as `ARCHIVED` and still returns their figures, so they are kept
in `ADS` with their final numbers. **They are only returned when the ad-level query
filters `effective_status` to include `ARCHIVED`/`DELETED`** — without that filter
campaign 5's ad rows came up ₹65,551.01 short of its own total. Same trap as the
archived *campaign* in `halcyon-reports`; it applies at ad level too.

**The influencer campaign is finished.** `India | MBBS Students - influencers`
(`120249585873390482`) is PAUSED and closed at **₹7,700.08 for 160 registrations,
₹48.13 each** — more than twice the October rate of the campaign that replaced its
budget. Its last delivery was 3 Oct, with one late-attributed install and registration
landing on 4 Oct.

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

**Checked again on the 30 Oct refresh: no renames and no creative swaps.** The ad
activity log for 28 Sep – 30 Oct carries no `Ad name updated` and no `Ad updated`
event at all — only `Ad created`, `Ad status updated` and ad-review events. The
arithmetic agrees: each of the six pre-October ad objects on campaign 5 still answers
to the name the report already had for it, and both 22 Sep splits still reconcile to
the paisa against their ad object's lifetime (₹10,361.32 + ₹99.64 = ₹10,460.96;
₹1,076.22 + ₹413.03 = ₹1,489.25). Every `E3-*` and `influencer *oct` name is a
genuinely new ad object and was **appended** to `ANAME`, which now has 22 entries.

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
