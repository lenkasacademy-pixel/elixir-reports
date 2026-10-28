# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 28 October 2026, 8:20 am IST.
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

## Where the account stands (28 Oct 2026)

**October was a deliberate scale-up, and it worked.** The daily budget on
`India | MBBS Students - Doctors | App Downloads | signups` (`120249183882130482`)
went **₹1,000 → ₹8,000 on 3 Oct at 11:07 am**, **₹8,000 → ₹12,000 on 4 Oct at
12:10 pm**, and back to **₹6,000 on 20 Oct at 10:14 am** (activity log, actor
Abhijeet Lenka). Spend followed: ₹4,617 on the 3rd, ₹14,846 on the 4th,
₹8,600–₹13,500 a day to the 19th, ₹1,500–₹7,800 since.

Registrations got much cheaper as it scaled:

| window | spend | registrations | each |
|---|---|---|---|
| September (1–30) | ₹92,623.77 | 2,750 | ₹33.68 |
| October 1–27 | ₹2,43,714.28 | 12,243 | **₹19.91** |
| …of which 4–19 Oct (peak) | ₹1,93,685.01 | 8,931 | ₹21.69 |
| …of which 20–27 Oct | ₹43,121.96 | 3,085 | **₹13.98** |

Lifetime the account is now **₹4,08,192.11** media (₹4,81,666.69 billed incl. GST),
35,197 installs and 16,088 registrations — ₹25.37 a registration across all nine
campaigns, **₹22.30 on the live one** (down from ₹32.89 at the last snapshot).

**Thirteen new creatives arrived.** Eleven `E3-01` … `E3-10` ads were created on
**3 Oct between 11:56 and 11:58 am** (ads MCP server) and have taken
₹2,08,541.98 between them at **₹21.40 a registration**; `E3-08a 3 medical apps to 1`
alone is ₹1,23,412.33 at ₹20.79 and is the biggest single creative on the account.
Two more were created on **20 Oct**, and `influencer 20oct – Copy` is now the
**cheapest line on the account at ₹13.18 a registration** on ₹31,567.47. The old
workhorse `v5 - I am a med student` is archived at ₹31.34.

**`India | MBBS Students - influencers` (`120249585873390482`) is now PAUSED.** It
last delivered on 2 October and closes at ₹7,700.08 for 386 installs and 160
registrations (₹48.13 each). Only the signups campaign is ACTIVE, so the default
"active only" view shows one campaign.

The late-September regime this file used to describe (both campaigns cut to
₹1,000/day on 27 Sep, the 27th at ₹30.30 a registration) is now history; October
is a different and much larger regime, and the two should not be read against
each other.

### ⚠ Archived ads vanish from an unfiltered ad listing

**This nearly cost ₹65,551 of ad rows on this refresh.** Four of the live
campaign's ads are **ARCHIVED** at Meta — `v5 - I am a med student` (₹54,351.01),
`influencer direct video ad - Rheumatoid Arthritis` (₹10,460.96),
`influencer - 20oct` (₹631.34) and `E3-06 My entire AI stack` (₹107.70) — and an
ordinary `level: "ad"` pull **does not return them**, while the campaign total still
includes their spend. The first pull came back ₹65,551.02 short and the only sign
was that `sum(ADS) != CAMP`.

Always pull ads with an explicit status filter:

    filtering: [{field:"effective_status", operator:"IN",
                 value:["ACTIVE","PAUSED","DELETED","ARCHIVED","CAMPAIGN_PAUSED",
                        "ADSET_PAUSED","DISAPPROVED","PENDING_REVIEW","PREAPPROVED",
                        "WITH_ISSUES"]}]

This is the same trap `halcyon-reports` records for *campaigns*; it bites at ad
level too.

### No renames this period — checked, not assumed

`ads_account_get_activity_logs` with `event_category: "ad"` over 28 Sep – 28 Oct
returns **120 events and not one `Ad name updated`**. The 13 new names are
`Ad created` events, so they were **appended** to `ANAME` (indexes 8–20) and
nothing was renamed in place. `ANAME` is now 21 long and its first 8 entries are
byte-identical to the previous snapshot — asserted at build time.

The two 22 September creative swaps are unchanged and still split by hand:
`v4 - Ecg` → `influencer direct video ad - Rheumatoid Arthritis` and
`v1 - male` → `influencer - hasaan`. Each pair is **one Meta ad id**; the
`REPLACED` row keeps everything to 21 September and the new-creative row is the
Meta ad total minus it. Note **`v5 - I am a med student` is not part of a swap** —
it is its own ad id and takes Meta's figures whole. (Getting that wrong moves
₹53,275 between two creatives without changing any total, so no reconciliation
check would catch it.)

### Revisions Meta made after the last publication

- **29 Sep settled from a part-day**: signups +₹541.09 (₹349.67 → ₹890.76),
  influencers +₹42.68 (₹13.49 → ₹56.17). This is exactly why `SNAP_DAY` is held
  out of the window total.
- **28 Sep** revised +₹2.74 on signups.
- **22 Sep** gained one registration (33 → 34), which a re-pull placed on
  `v5 - I am a med student`. `ADAY` was corrected rather than guessed.

Because of this, **re-pull the whole run of days for any campaign that is still
live**, not just the days since the last snapshot — the first attempt at this
refresh appended only 30 Sep onward and came out ₹524.29 short on the live
campaign and ₹42.68 short on the influencer one.

### A known, pre-existing gap

`India | MBBS Students | ios` (`CID[0]`) reports **184** registrations at campaign
level but its age rows sum to **183**. That gap is in the published file too — it
is Meta's own age breakdown dropping one, not a build error. It is left alone.

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
