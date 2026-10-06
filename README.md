# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 6 October 2026, 8:30 am IST.
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

## The account was rebuilt on 3 October 2026 — read nothing across that line

Everything before 3 Oct was a ₹1,000-₹2,000/day account running creatives from August and
September. Everything after is a ₹12,000/day account running a creative batch that did not
exist before. The activity log has all of it, same day:

| 3 Oct | event |
|---|---|
| 11:07 am | daily budget on `India \| MBBS Students - Doctors \| App Downloads \| signups` **₹1,000 → ₹8,000** |
| 11:07 am | ad set `120249183882120482` to "Optimize bid for actions", **₹30.00 bid cap** |
| 11:56–11:58 am | **eleven new ads created** — `E3-01` … `E3-10`, with two variants of E3-08 |
| 12:01–12:08 pm | all eleven cleared review and went Active |
| 12:14 pm | `Influencer - 11sep` switched off inside the signups campaign |
| 12:15 pm | the whole second campaign, `India \| MBBS Students - influencers`, **paused** |
| 1:11–3:14 pm | the new ads started delivering |
| 8:01 pm | bid cap removed, back to "Automatically bid for actions" |

| 4 Oct | event |
|---|---|
| 12:10 pm | daily budget **₹8,000 → ₹12,000** |
| 6:48 pm | ₹30,000 added to the prepaid balance (UPI) |

All of it by actor Abhijeet Lenka; the budget change and the eleven ads were made through
the **ads MCP server**, the campaign pause through Power Editor.

**It worked, and by a wide margin.** 4 October settled at ₹14,846.11 for 848 registrations
— **₹17.51 each** — and the 5th at ₹12,306.39 for 666, ₹18.48. The previous benchmark was
₹30.30 (27 Sep) and before that ₹41.74 (26 Sep). Cost per install went ₹15.67 → **₹6.42**
(4 Oct) and ₹7.47 (5 Oct). CTR went from 0.6–0.8% to 1.40% on the 4th.

**Delivery concentrated on four of the eleven.** `E3-08a 3 medical apps to 1` (₹17,898.75,
2,623 installs, 996 registrations, **₹17.97 per registration**), `E3-09 Asking medicos
which AI app they trust` (₹8,244.37, ₹18.61), `E3-10 Rounds with the strictest senior`
(₹4,599.25, ₹19.82) and `E3-08b 5 apps on rounds to 1` (₹488.27, ₹30.52) took ₹31,230 of
the ₹31,856 the batch has spent. **`E3-01` through `E3-07` took ₹625 between them and
returned three registrations** — Meta starved them within hours. If the client asks which
new creative to make more of, it is the "apps to 1" idea, twice.

For comparison, the long-running `v5 - I am a med student` sits at ₹54,303.92 lifetime for
1,727 registrations, **₹31.44 each**.

**Only one campaign is live now.** `India | MBBS Students - influencers`
(`120249585873390482`) opened on 24 Sep on its own ₹3,000/day, ran to 4 Oct and is PAUSED;
it closes at ₹7,700.08 for 160 registrations (₹48.13). Its three ads read
`CAMPAIGN_PAUSED`. `influencer - hasaan` and `Influencer - 11sep` are PAUSED in the main
campaign too.

### Earlier history, kept because the tables still show it

**Budgets were cut on 27 Sep 2026 at 7:36 am** — both campaigns went from ₹3,000/day to
₹1,000/day (activity log, Power Editor, actor Abhijeet Lenka), so the account cap fell
from ₹6,000/day to ₹2,000/day. **The first day under the cap was the cheapest normal day
in a fortnight at the time**: 27 Sep settled at ₹1,939.44 for 64 registrations, ₹30.30
each, against ₹41.74 on the 26th at three times the spend. **28 Sep is not a fair
comparison**: it spent only ₹712.22 (31 registrations) because delivery thinned all day
and all but stopped from 3 pm to 9 pm. It resumed in the 9 pm hour, right after ₹30,000
was added to the prepaid balance at 9:32 pm (activity log, "Money added to balance").
Watch the balance: a stall like that looks like a cheap day in the tables. At 7:34 am the
influencer ad set `120249585873460482` also moved to "Optimize bid for actions" with a
**₹19.00 bid cap**. 29 and 30 September settled at ₹946.93 and ₹1,145.17, and 1–3 October
ran flat at about ₹1,143 a day — the last three days of the old regime.

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

**Both splits were re-verified on the 6 Oct 2026 refresh and neither moved.** Meta now
returns ad `120249212142170482` only under the new name, carrying ₹10,457.31 of lifetime
spend; a ranged pull from the 22 Sep swap date gives the new creative ₹95.99, and
₹10,457.31 − ₹95.99 = ₹10,361.32, exactly the frozen `v4 - Ecg` row. The same subtraction
on ad `120249183882150482` returns ₹1,076.22 for `v1 - male`, also unchanged, and
`influencer - hasaan` is identical to the paisa at ₹413.03. **Do that subtraction every
refresh** — it is the only way to tell a Meta revision of the retired creative's days from
new delivery on the replacement.

`ANAME` grew from 8 names to 19 on that refresh: the eleven `E3-*` creatives were
**appended at indexes 8–18** and the first eight were left exactly where they were. Note
that index 1 (`v4 - Ecg`) and index 6, and index 5 (`v1 - male`) and index 7, are the same
ad object either side of 22 September — `ADAY` rows dated before the swap use the retired
index and rows from 22 Sep onward use the replacement. Keep that rule when adding days.

## Campaigns not in the report

`India | Influencer Videos | App Registrations` (`120249585723440482`) is on the
account, PAUSED, and has **never delivered**: Meta returns no spend, impression or
result fields for it at all. So it is deliberately left out of `CID` and `CAMP`. Add it
the day it spends, **appended to the end of `CID`** so no historical row re-labels itself.
