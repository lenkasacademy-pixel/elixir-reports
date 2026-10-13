# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 13 October 2026, 8:10 am IST.
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

**13 Oct 2026: a new creative batch has roughly halved cost per registration.**
Eleven new ads, the **`E3-` series**, were created on 3 October (activity log, `ads MCP
server`, actor Abhijeet Lenka) and `India | MBBS Students - Doctors | App Downloads |
signups` was scaled hard behind them. The ten settled days 3-12 October took
**Rs 1,15,686.35 for 5,733 registrations - Rs 20.18 each** - against **Rs 33.68** across
the whole of September. Daily spend has run Rs 8,600-Rs 14,800 since the 4th, against
the Rs 1,000/day cap the account was under on 27 September; that cut is now ancient
history and nothing after 3 October should be read against it.

**`E3-08a 3 medical apps to 1` is the standout of the account's whole life**:
Rs 68,450 for **3,496 registrations at Rs 19.58**, more registrations than any earlier
creative has produced in total. `E3-09 Asking medicos which AI app they trust`
(Rs 28,362 / 1,388 / Rs 20.43) and `E3-10 Rounds with the strictest senior`
(Rs 17,924 / 849 / Rs 21.11) follow. Against them `v5 - I am a med student`, the
previous best, sits at Rs 31.36 and `Influencer - 11sep` at Rs 36.59. The other eight
E3 ads have spent under Rs 160 each and have no signal yet.

**The second campaign is finished.** `India | MBBS Students - influencers`
(`120249585873390482`), opened 24 September on its own Rs 3,000/day, stopped on
4 October and now reads **PAUSED**. It closes at **Rs 7,700.08 for 160 registrations**
(Rs 48.13 each), all but Rs 13 of it from `Influencer - 11sep`. Its `ADS` rows move
from ACTIVE to CAMPAIGN_PAUSED.

**No renames this refresh.** `ads_account_get_activity_logs` (`event_category: "ad"`)
from 28 Sep onward contains **no `Ad name updated` event at all** - only the eleven
`Ad created` events of 3 October and ordinary status changes. The two creative swaps of
22 September are unchanged and still split; their `REPLACED` rows are frozen and
reproduce to the paisa (`v4 - Ecg` Rs 10,361.32 + `influencer direct video ad -
Rheumatoid Arthritis` Rs 96.34 = Rs 10,457.66, Meta's lifetime figure for that one ad
id; `v1 - male` Rs 1,076.22 + `influencer - hasaan` Rs 413.03 = Rs 1,489.25, exactly).

**Drift on the live campaign, and why `DAILY` is built two ways.** The signups campaign
is spending right now, and the three views of it were read seconds apart:

- its lifetime total moved **Rs 2,11,140.64 -> Rs 2,11,225.80 (+Rs 85.16)** between two
  reads minutes apart;
- 12 October moved **Rs 13,412.65 -> Rs 13,413.69** in the same interval.

So `CAMP` cannot agree exactly with `DAILY` and `AGE` today. The choice made here is to
keep the strict rule strict: **for 12 and 13 October the `DAILY` row is built from the
same ad-level read `ADAY` is built from**, so `sum(ADAY) == DAILY` holds on *every* day
08-27 -> 10-13 on spend, impressions, installs and registrations, not just the settled
ones. Reach on those two rows stays Meta's campaign figure, because reach is
de-duplicated and cannot be summed. Every day from 27 August to 11 October agrees
exactly between the campaign-level and ad-level reads with no adjustment, which is what
validates the rest. The residue lands where this README already says to expect it:
`DAILY` and `AGE` each run **Rs 31.49** ahead of `CAMP` on campaign 5
(installs +3 / +8, registrations +4 / +6, impressions +675). `ADS` still equals `CAMP`
to the paisa. **Do not chase this to zero** - re-pulling only moves it again.

`AGE` on campaign 0 has carried a **1-registration** shortfall against `CAMP` since
before this refresh (183 banded + Unknown against 184). Campaign 0 has been paused for
weeks, so it is historical, not new.

`ADAY` now starts at **27 August**, not 30 August. The three earlier days were always
available, and including them makes the `sum(ADAY) == DAILY` assertion hold across the
whole of the live campaign's life rather than most of it.

## What to re-pull, and what not to

Only the campaigns that are still moving need their day rows re-pulled. On this refresh
a lifetime `level: "campaign"` read showed seven of the nine unchanged **to the paisa**
(indexes 0, 1, 2, 3, 4, 6, 7), so their `DAILY`, `ADS` and `AGE` rows were carried
forward untouched and only campaigns 5 and 8 were re-fetched. Do that check first: it
turns a 675-row pull into a 200-row one, and it is also the cheapest way to notice a
campaign that has quietly started spending again.

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
