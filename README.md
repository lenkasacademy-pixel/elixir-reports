# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 1 November 2026, 11:00 am IST.
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

## October: the account quadrupled

**Lifetime spend is now ₹4,33,709.84**, against ₹1,62,310 at the 29 September snapshot.
Almost all of the increase is one campaign: **India | MBBS Students - Doctors | App
Downloads | signups** went from ₹89,451.65 to **₹3,61,149.19** — 34,280 installs at
₹10.54 and 16,102 registrations at ₹22.43 — and it is the only campaign still spending.

The account came into October on about ₹1,100 a day, went to ₹4,619 on the 3rd and
**₹14,846 on the 4th**, then held between ₹4,400 and ₹13,500 a day for the rest of the
month. That is well above the ₹2,000/day cap set on 27 September, so the cap was raised
again at some point in early October; the budget history is not in the figures this report
pulls.

**India | MBBS Students - influencers** (`120249585873390482`) stopped on 3 October after
₹7,700.08 and is now PAUSED. It is no longer a live campaign, so `ADAY` carries it only up
to its last spending day.

**Fifteen new creatives** were appended to `ANAME` at indexes 8–22: eleven in a new `E3-xx`
series and four dated influencer ads (`influencer - 20oct`, `influencer 20oct – Copy`,
`influencer 27oct`, `influencer31st oct`). **`E3-08a 3 medical apps to 1` is now the largest
single creative the account has ever run** — ₹1,26,171.10 for 12,971 installs and 6,029
registrations. `influencer 20oct – Copy` is second at ₹48,707.91 and currently carries about
70% of daily spend.

**`v5 - I am a med student` was archived** at ₹54,351.01 (it had been ₹50,622.10 and the
account's biggest creative at the last snapshot).

## Archived ads vanish from an ad-level listing — filter for them

**This nearly cost ₹65,551 of creative detail on this refresh.** An ad-level pull with no
`effective_status` filter returned 21 of the live campaign's ads but silently omitted four
archived ones, and the ad rows then summed **₹65,551.01 short** of the campaign total. The
missing ads were `v5 - I am a med student`, `influencer direct video ad - Rheumatoid
Arthritis`, `influencer - 20oct` and `E3-06 My entire AI stack`, all `ARCHIVED`.

Filter `effective_status IN ["ACTIVE","PAUSED","ARCHIVED","DELETED","CAMPAIGN_PAUSED",
"ADSET_PAUSED",…]` on **every** ad-level pull, exactly as `halcyon-reports` must do at
campaign level. With them included the ad rows sum to **₹3,61,149.19**, equal to the
campaign total to the paisa.

Note also that the ad-level listing **paginates** — the first page stopped at 30 rows with a
`next_cursor`. Follow it, or the tail of the creative list goes missing the same way.

## The two creative swaps still hold

The 22 September swaps are unchanged and were re-read at this refresh:

| Ad id | Retired creative (to 21 Sep) | Current creative (from 22 Sep) |
|---|---|---|
| `120249212142170482` | `v4 - Ecg` — ₹10,361.32 | `influencer direct video ad - Rheumatoid Arthritis` — ₹99.64 |
| `120249183882150482` | `v1 - male ( asking medicos how they study)` — ₹1,076.22 | `influencer - hasaan` — ₹413.03 |

**Both retired halves came back identical to the paisa on every field** — spend,
impressions, reach, clicks, CTR, CPM, link clicks, installs and registrations. A ranged pull
(`since` the swap date) is still how the current half is read, because reach, CTR and CPM
cannot be obtained by subtraction. The ranged halves add up to Meta's single-ad lifetime
figure exactly, which is what proves the split is still sound.

The page carries `noindex, nofollow` so it stays out of search results.

A live version of the same report — one that queries Meta each time it is opened — is kept
as a private Claude artifact rather than here.

## What this refresh reconciled

- **`ADAY` against `DAILY`, per campaign per settled day, on spend, installs *and*
  registrations: 74 campaign-days checked, 0 mismatches.** The 1 November part-day is left
  out of the assertion, as it must be.
- **`ADS` sums equal `CAMP` totals exactly** for all nine campaigns.
- `ANAME`'s first eight entries are byte-identical and in their original order; the fifteen
  new names are appended after them, so every historical `ADAY` row still points at the
  creative that actually ran.
- `ADS` re-sorted descending by spend and the sort asserted. `ANAME` deliberately not sorted.

**Two drifts, both on the one live campaign, both explained:**

1. **`DAILY` and `AGE` sum ₹39.59 (and 844 impressions) above `CAMP`** for campaign 5. The
   `CAMP` lifetime figure was pulled a few minutes before the day and age pulls, and
   **1 November moved by ₹367 during this refresh alone** (₹1,517.96 → ₹1,885.05 between two
   calls). This is the part-day race the report already guards against in its window totals;
   it cannot be eliminated without an atomic pull, so it is reported rather than hidden.
   0.011% of the campaign.
2. `AGE` for campaign 5 also runs +5 installs and +2 registrations above `CAMP`. That is
   Meta's own age-breakdown over-attribution, not a timing effect.

**Pre-existing, not introduced here:** campaign 0's `AGE` registrations sum to one less than
its `CAMP` total. That was true in the published file and campaign 0 has not spent since
August, so it was left alone.

**Days that moved after publication:** 28 Sep campaign 5 +₹2.74; 29 Sep — the old part-day —
campaign 5 ₹349.67 → ₹890.76 and campaign 8 ₹13.49 → ₹56.17. Every other published day came
back identical.

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
account, PAUSED, and has **still never delivered** (re-checked 1 Nov 2026): Meta returns no spend, impression or
result fields for it at all. So it is deliberately left out of `CID` and `CAMP`. Add it
the day it spends, **appended to the end of `CID`** so no historical row re-labels itself.
