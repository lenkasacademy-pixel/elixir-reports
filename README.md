# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 13 November 2026, 8:15 am IST.
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

## Where the account is now (13 Nov 2026)

**The account is several times the size the September snapshot described.** The budget on
`India | MBBS Students - Doctors | App Downloads | signups` (`120249183882130482`) went from
₹1,000/day to **₹8,000 on 3 Oct 11:07 am** (activity log, actor Abhijeet Lenka, via the ads MCP
server), **₹12,000 on 4 Oct 12:10 pm**, back to **₹6,000 on 20 Oct 10:14 am**, then **₹4,500 on
9 Nov** and **₹3,500 on 10 Nov**. Daily account spend ran ₹1,100–1,150 on 30 Sep–2 Oct, hit
₹14,846.11 on 4 Oct, and has sat between ₹4,400 and ₹13,500 since. Nothing here is comparable to
the ₹2,000/day regime the previous snapshot was written under.

**A second live campaign, `Qualified Leads | Campaign` (`120250327482630482`), opened on 6 Nov.**
It was created at ₹6,000/day, cut to ₹2,000 the same afternoon (6 Nov 4:29 pm), then raised to
₹3,000 on 9 Nov and ₹5,000 on 10 Nov. It is **appended to the end of `CID` as index 9**, so no
historical row re-labels itself. The account now carries **₹8,500/day** across the two live
campaigns. It is at ₹22,324.93 for 578 registrations (₹38.62 each) in its first eight days —
dearer than the main campaign, but it is still in learning.

**`India | MBBS Students - influencers` (`120249585873390482`) last spent on 3 Oct and is PAUSED**,
closing at ₹7,700.08 for 160 registrations (₹48.13 each). Its three influencer creatives are all
retired.

**A new creative set carried the October scale-up.** Ten `E3-xx` videos went live on 3 Oct and the
dated influencer ads followed (`influencer - 20oct`, `influencer 20oct – Copy`, `influencer 27oct`,
`influencer31st oct`, `new influencer 1nov`). The winners, by cost per registration on real volume:

| Ad | Spend | Regs | Cost/reg | Days |
|---|---|---|---|---|
| `influencer 20oct – Copy` | ₹58,087.12 | 3,528 | **₹16.46** | 20 Oct – 13 Nov |
| `E3-08a 3 medical apps to 1` | ₹1,26,819.57 | 6,045 | **₹20.98** | 3 Oct – 1 Nov |
| `new influencer 1nov` | ₹47,437.58 | 2,211 | ₹21.46 | 1 Nov – 13 Nov |
| `E3-09 Asking medicos which AI app they trust` | ₹53,243.63 | 2,446 | ₹21.77 | 3 Oct – 28 Oct |
| `E3-10 Rounds with the strictest senior` | ₹33,465.04 | 1,459 | ₹22.94 | 3 Oct – 13 Nov |
| `v5 - I am a med student` (ARCHIVED) | ₹54,351.01 | 1,733 | ₹31.36 | 31 Aug – 14 Oct |

`v5 - I am a med student` was the account's biggest ad in September; it last spent on 14 Oct and is
now ARCHIVED. The E3 set replaced it at roughly two thirds of its cost per registration. The seven
low-volume E3 videos (`E3-01` to `E3-07`, `E3-08b`) each took under ₹1,000 and are all paused —
read them as a creative test that picked `E3-08a`, not as results.

## Archived ads are hidden from an unfiltered ad listing

**This bit the report on 13 Nov 2026.** An ordinary `level: "ad"` read returned ad rows that summed
to **₹65,551.01 short** of `India | MBBS Students - Doctors | App Downloads | signups`. The missing
spend was in four ads that are now **ARCHIVED** — `v5 - I am a med student` (₹54,351.01), the
`v4 — Ecg` / `influencer direct video ad` ad id (₹10,460.96), `influencer - 20oct` (₹631.34) and
`E3-06 My entire AI stack` (₹107.70).

Worse, archived ads are missing from **ranged** ad pulls too, so the historical read used to tell a
rename from a new ad came back without them and made it look as though `v5` had been renamed to
`E3-08a`. It had not. **Always filter `effective_status IN ["ACTIVE","PAUSED","ARCHIVED","DELETED",
...]` on every ad-level read, lifetime and ranged.** Same trap as `halcyon-reports` has at campaign
level.

Also note `object_ids` cannot be used to scope an ad-level read to a campaign — it is matched
against the requested `level` and campaign ids come back in `omitted_object_ids`. Filter on
`campaign.id` with `IN` instead.

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

On the 13 Nov 2026 refresh this check came back clean: `ads_account_get_activity_logs`
(`event_category: "ad"`, since 25 Sep) holds 34 `Ad created` events and **no**
`Ad name updated` or `Ad updated` events at all, so every unfamiliar name is a genuinely
new ad and `ANAME[0..7]` keeps its original order.

## Campaigns not in the report

`India | Influencer Videos | App Registrations` (`120249585723440482`) is on the
account, PAUSED, and has **never delivered**: Meta returns no spend, impression or
result fields for it at all. So it is deliberately left out of `CID` and `CAMP`. Add it
the day it spends, **appended to the end of `CID`** so no historical row re-labels itself.
