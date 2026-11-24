# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 24 November 2026, 8:15 am IST.
  Self-contained: the figures are baked into the file, so it needs no network and no connector.
  Campaign rail, per-ad tables, day-by-day, age bands, and the GST-inclusive billing line.
  Defaults to the campaigns that are active; a switch shows all twelve.

**Lifetime as at this snapshot: ₹5,92,051.81 media spend (₹6,98,621.14 billed with GST),
49,696 installs at ₹11.91 and 22,896 registrations at ₹25.86.** At the 29 September
snapshot it was ₹1,61,870.20, 9,509 installs and 3,741 registrations at ₹43.27. The
29 Sep – 24 Nov window alone took **₹4,30,542.03 for 19,167 registrations, ₹22.46 each** —
the account is both much bigger and much cheaper than the report last showed.

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

## What changed between 29 Sep and 24 Nov 2026

The 24 Sep second campaign and the 27 Sep budget cut described in earlier versions of
this file are now history; what follows replaces them.

**The account scaled, then handed over to a new campaign.** `India | MBBS Students -
Doctors | App Downloads | signups` (`120249183882130482`) went from ₹1,000/day to
**₹8,000 on 3 Oct** (activity log, actor Abhijeet Lenka, application *ads MCP server*)
and to **₹12,000 on 4 Oct**, was cut to ₹6,000 on 20 Oct, to ₹4,500 on 9 Nov and ₹3,500
on 10 Nov, and is now **PAUSED** — its last delivery was 15 November. It closes at
₹4,25,427.74 for 18,968 registrations. `India | MBBS Students - influencers` stopped on
3 October at ₹7,700.08.

**Three new campaigns were added to `CID`, appended at indexes 9, 10 and 11:**

| # | Campaign | Spent | Regs | Each | Status |
|---|---|---|---|---|---|
| 9 | `Qualified Leads \| Campaign` | ₹91,744.67 | 2,871 | ₹31.96 | **ACTIVE** |
| 10 | `Qualified Leads \| AI Videos \| 21 Nov` | ₹1,917.45 | 11 | ₹174.31 | PAUSED |
| 11 | `Qualified Leads \| cost cap 30` | ₹401.30 | 11 | ₹36.48 | **ACTIVE** |

`Qualified Leads | Campaign` started on **6 November** and its daily budget has been
raised five times since — ₹2,000 → ₹3,000 → ₹5,000 → ₹6,000 → ₹7,000 → ₹8,000 — so it
is now effectively the whole account. `influencer3 nov` is the workhorse inside it
(₹65,035.58, 2,105 registrations).

**14 November: the whole account spent ₹0.** ₹45,000 had been added to the prepaid
balance at 10:33 pm the night before and delivery was normal on either side, so the
balance does not explain it. Nobody has. It is kept in `DAILY` as a real zero row
because installs and registrations were still attributed to it.

**Results keep landing on days that look closed.** The signups campaign booked installs
and registrations on 14, 16, 17, 18 and 19 November with ₹0 spend, after it had stopped
serving. Never filter day rows on spend alone — keep any row with a nonzero metric.

**Two settled September days moved after the last publication.** 22 Sep gained one
registration (33 → 34) and 28 Sep gained ₹2.74 of spend and 46 impressions
(₹686.75 → ₹689.49). Both are patched here, in `DAILY` and in the matching `ADAY` rows.
This is why the refresh re-pulls old days rather than appending.

**No ad was renamed and no creative was swapped in this window.** The activity log
(`event_category: "ad"`, 29 Sep – 24 Nov) holds 482 entries: 62 *Ad created*, the rest
status changes. Every new name on the page is a new ad id. Note that re-pulling an old
window at ad level labels every row with the ad's **current** name — which is why the
Aug/Sep `ADAY` rows are kept as published and only the two restated days were patched.

### Archived ads vanish from an ordinary ad listing

`v5 - I am a med student` (₹54,351.01), the swapped `influencer direct video ad -
Rheumatoid Arthritis` ad id (₹10,460.96), `influencer - 20oct` (₹631.34) and
`E3-06 My entire AI stack` (₹107.70) are **ARCHIVED** at Meta. An unfiltered ad-level
pull does not return them, and **₹65,551 of campaign 5's spend disappears** — the ad
rows then sum ₹65,551 short of the campaign header. Always pass
`effective_status IN [..., "ARCHIVED", "DELETED"]`, exactly as `halcyon-reports` has to
for campaigns. The ad-sum-equals-campaign-total check is what caught it.

### The two live campaigns drift between reads

Campaigns 9 and 11 were still spending at 8:15 am, and Meta restates the current day
between one call and the next. Measured on this refresh, `Qualified Leads | cost cap 30`
read ₹393.65, then ₹397.31, ₹401.30, ₹405.38 and ₹418.83 across five calls minutes apart.
The published figures take the campaign-level pull as the snapshot, so:

- `CAMP`, `DAILY` and `AGE` tie **exactly** for all twelve campaigns.
- `ADS` sits ₹0.15 under `CAMP` for campaign 9 and ₹3.99 under for campaign 11.
- `ADAY` sits ₹0.07 over `DAILY` on campaign 9's 23 Nov row.
- Campaign 11's day rows sum to 23 installs against Meta's lifetime 22 — one install
  de-duplicated across the two days.

Everything else, on every settled campaign, reconciles to the paisa. Do not chase these
down by re-pulling: the next read moves them again. `SNAP_DAY` already keeps 24 November
out of every headline.

The page carries `noindex, nofollow` so it stays out of search results.

A live version of the same report — one that queries Meta each time it is opened — is kept
as a private Claude artifact rather than here.

## A rename can hide a creative swap — check the activity log

**This bit the report on 23 Sep 2026** (the check still runs every refresh; it found
nothing on 24 Nov 2026). Two ads were renamed at Meta and the
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
That is how the three Qualified Leads campaigns were added on 24 Nov 2026 — indexes 9,
10 and 11, with indexes 0–8 untouched.

## Reconciliation run on 24 Nov 2026

- `level: "ad_account"` with `time_increment: "1"` for 29 Sep – 24 Nov: campaign sums
  equal the account's own daily **spend, impressions and clicks on all 57 days**.
  Window total ₹4,30,542.03 either way.
- For every campaign, `CAMP` equals the sum of its `DAILY` rows and of its `AGE` rows on
  spend, impressions, installs and registrations.
- `ADS` equals `CAMP` for every campaign except the two still spending (see above).
- `ADAY` equals `DAILY` per campaign per settled day on spend, impressions, installs and
  registrations, except campaign 9's 23 Nov row (₹0.07).
- `ADS` is sorted descending by spend; `ANAME` indexes 0–7 are unchanged and 23 new
  creative names are appended at 8–30.
- The snapshot stamp is updated in **four** places in `index.html` (the HTML comment on
  line 8, the masthead stamp, the footer paragraph, the `Snapshot data` script comment)
  and once here.
