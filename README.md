# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a creative-wise report snapshotted 25 November 2026, 8:15 am IST.
  Self-contained: the figures are baked into the file, so it needs no network and no connector.
  Campaign rail, per-ad tables, day-by-day, age bands, and the GST-inclusive billing line.
  Defaults to the campaigns that are active; a switch shows all twelve.

## Status as of 25 Nov 2026: the campaign that worked is off

**Settled window 31 July – 24 November 2026**, 117 days with delivery: **₹594,157.50** for
49,853 installs and **22,995 registrations**, ₹25.84 each. 25 November is a part day
(₹269.69 so far) and is held out of every total and every rate.

`India | MBBS Students - Doctors | App Downloads | signups` ran 27 Aug – **15 Nov** and took
**₹425,427.74** — about five rupees in every seven this account has ever spent — for 18,968
registrations at **₹22.43**, the cheapest sustained rate anything here has managed. It is now
PAUSED and nothing has replaced it at that scale.

What is live instead are three new `Qualified Leads` campaigns, appended to `CID` as indexes 9–11:

| # | Campaign | Ran | Settled spend | Regs | Each |
|---|---|---|---|---|---|
| 9 | Qualified Leads \| Campaign | 6–24 Nov | ₹92,368.53 | 2,919 | ₹31.64 |
| 10 | Qualified Leads \|instagram reel | 21–22 Nov | ₹1,917.45 | 11 | **₹174.31** |
| 11 | Qualified Leads \| cost cap 30 | 23–24 Nov | ₹1,883.13 | 62 | ₹30.37 |

**The instagram-reel campaign is the outlier**: ₹1,917.45 for 11 registrations is five to six
times the rate of everything beside it, and it has barely delivered since the 22nd. The cost-cap
campaign is landing at ₹30.37, which is its ₹30 cap behaving as asked.

**Read the months together and the direction is plain.** October took ₹268,262.50 for 13,304
registrations, **₹20.16 each**; 1–24 November took ₹162,293.12 for 5,903, **₹27.49 each** — a
little over a third dearer, on a smaller budget, while the account moved off the campaign that
was working. That is the one thing to put in front of the client.

## Changed 25 Nov 2026: the page now describes settled days only

`CAMP`, `ADS` and `AGE` used to be lifetime pulls (`date_preset: "maximum"`), which included the
still-running day. **They are now ranged pulls over the settled window, 2026-07-29 → 2026-11-24.**
`DAILY` still runs to `SNAP_DAY` and carries the part day in its own row.

This was not cosmetic. With three campaigns live, every separate API call returned a different
instant for 25 November — the same campaign read ₹92,465.27, then ₹92,467.21, then ₹92,467.22,
₹92,471.56 across four calls minutes apart, and the lifetime, daily, ad-level and age-breakdown
pulls could not be made to agree while any of them included today. Over a closed window they all
agree exactly and **the refresh is reproducible**: run it twice and the settled figures are
identical. Keep it this way. The part day belongs in `DAILY` and nowhere else.

The one place a part-day figure is still needed is `DAILY`'s last row. **Pull it as a burst** —
ad level, campaign level and `ad_account` level for that single day, in one go — and check the
three agree before using it. On this refresh they all read **₹269.69 / 7,141 impressions**, which
is why that row can be trusted.

## A rename can hide a creative swap — check the activity log

**This bit the report on 23 Sep 2026.** Two ads were renamed at Meta and the refresh carried their
whole history forward under the new names. It was not a rename: the *creative was replaced on the
same ad id*, so Meta reported `v4 — Ecg`'s installs and registrations under
`influencer direct video ad — Rheumatoid Arthritis`, a video that had produced **nothing**. The
client spotted it, not the reconciliation — every total still balanced, because the numbers were
real, just attached to the wrong creative.

The tell is in `ads_account_get_activity_logs` (`event_category: "ad"`), which records all three
events together:

    Ad updated        old_value ["2154354885490353"] -> new_value ["956543453540710"]
    Ad name updated   "v4 - Ecg" -> "influencer direct video ad - Rheumatoid Arthritis"
    Ad status updated Inactive -> Pending Process -> Pending Review -> Active

A pure rename never triggers Pending Review. **If a name changed, pull the activity log before
carrying history forward.** If the creative changed too, split the ad into two rows at the swap
date:

- the retired creative keeps everything up to the day before, status `REPLACED`
- the new creative starts from the swap day, with its own ranged pull for reach

and **append** the new names to `ANAME` while restoring the old names at their original indexes —
every historical `ADAY` row still points at the creative that actually ran.

**Both 22 September splits were re-verified on this refresh and they hold to the paisa.** Summing
`ADAY` either side of the swap date reproduces the published figures exactly: ad `120249183882150482`
gives `v1 - male` ₹1,076.22 before and `influencer - hasaan` ₹413.03 after; ad `120249212142170482`
gives `v4 - Ecg` ₹10,361.32 before and `Rheumatoid Arthritis` ₹99.64 after. Meta's own lifetime for
each ad is the sum of its two halves. Do not touch these rows; just re-check them the same way.

### A swap with no rename — `AI-04 Reel 4`, and the next refresh must split it

The activity log for 29 Sep – 25 Nov contains exactly **one** `Ad updated` event and **no**
`Ad name updated` events at all:

    11/25/2026 at 8:12 AM   Ad updated   120250608613900482  AI-04 Reel 4
    {"old_value":["1655525292780496"],"new_value":["1459320393060762"]}

So the trap ran in reverse this time: the creative on `AI-04 Reel 4` (campaign 10) was replaced
three minutes before this snapshot, and the ad was **not** renamed. Nothing is wrong with the page
— every settled rupee under that name is the creative that ran before 8:12 am, and the new creative
has only part-day delivery, which is excluded anyway. But from the next refresh onward that name
will blend two creatives.

**Split it at 2026-11-25 on the next refresh**: the old creative keeps everything to 24 Nov as
`REPLACED`, the new one starts from the 25th. It needs a name, and Meta will not supply one because
the ad was never renamed — so a human has to say what the new creative is called. Do not guess it.

## "The last 10 days"

Sits at the top of the all-campaigns view. It rolls `DAILY` up across whichever campaigns are in
scope and shows spend, impressions, CPM, CTR, clicks, installs, cost per install, registrations and
cost per registration, newest first, with a window total. `DAY_WINDOW` at the top of the rollup
controls the length.

Two things it does deliberately:

- **CPM and CTR are re-derived** from each day's own spend, impressions and clicks rather than
  averaged from the per-campaign values. Averaging two campaigns' CPMs gives a number that is not
  any real cost.
- **Reach is not shown at all.** Meta de-duplicates people, so a day's reach is not the sum of its
  campaigns' reach. Per-campaign reach stays on each campaign's tab. This is the same rule the
  campaign and age tables already follow for their totals.

Two more rules, both about not letting a bad day set a number:

- **The window total covers the settled days only.** `SNAP_DAY` keeps its own row, labelled
  *part day so far*, but it is out of the total and every rate beside it, and the total row says how
  many settled days it covers and which day it dropped. Meta revises the last two days all day;
  without this the window's cost per registration moved between one rebuild and the next.
- **The "cheapest day" footnote ignores days that barely spent.** A throttled or near-dead day still
  books registrations earned by the days before it, so its cost per registration is both meaningless
  and unbeatable. Only days carrying at least half the window's average settled spend are eligible.
  Before this guard the footnote named 21 Sep at ₹17.88 — a suppressed-delivery day that took ₹733 on
  13,898 impressions against the ₹3,000-4,000 either side of it — which is not a bar any normal day
  can clear.

## "Why the cost per registration moved"

Sits on each campaign. Cost per registration is exactly CPM divided by registrations per 1,000
impressions, so the card plots all three on one day axis: a rise is either dearer impressions or a
weaker response, and the shapes say which. Under it, a creative-by-creative split compares two equal
windows of settled days (the part-day is excluded from both) and labels each ad — *impressions
costlier* is a price problem, *response fading* is the one a new creative fixes.

That split reads `ADAY`, ad-level day rows indexed by `ANAME`. **`ADAY` has to be refreshed alongside
`DAILY`** or the split silently goes stale while the charts above it move. It now covers campaigns
5, 8, 9, 10 and 11 — the ones with delivery since the last snapshot — 676 rows over 27 Aug – 25 Nov.
The card needs three settled days to draw and two comparable windows to label, so campaigns 10 and 11
show charts but no creative labels yet; that is the guard working, not a bug.

**Re-pull `ADAY` for the whole life of a campaign, not just the new days.** Meta revised campaign 5's
pre-October history by **₹543.83** between the 29 Sep and 25 Nov snapshots. Appending would have left
`ADAY` disagreeing with `DAILY` on every day before October.

## Refreshing

Everything is keyed on `CID`, which maps an index to a campaign id. `CAMP`, `ADS`, `DAILY`, `AGE` and
`ADAY` rows all carry that index. **Never reshuffle `CID`** — append new campaigns to the end. Indexes
9, 10 and 11 were appended on 25 Nov for exactly this reason, in ascending campaign-id order, which is
also the order they launched.

| Array | Window | Row shape |
|---|---|---|
| `CAMP` | settled | `[campIdx, name, status, spend, impressions, reach, clicks, ctr, cpm, installs, regs]` |
| `ADS` | settled | `[name, campIdx, status, spend, impressions, reach, clicks, ctr, cpm, linkClicks, installs, regs]` |
| `DAILY` | settled **+ part day** | `["MM-DD", campIdx, spend, impressions, reach, clicks, ctr, cpm, installs, regs]` |
| `AGE` | settled | `[band, campIdx, spend, impressions, reach, clicks, ctr, cpm, installs, regs]` |
| `ADAY` | settled **+ part day** | `["MM-DD", campIdx, anameIdx, spend, impressions, clicks, installs, regs]` |

`YEAR` is prefixed to the `"MM-DD"` dates. `SNAP_DAY` is the part day. Re-sort `ADS` descending by
spend after patching and assert the sort; do **not** sort `ANAME`.

- **Ad-level insights do not return `campaign_id` unless you request that field by name.** At campaign
  level the id comes back as `id`, not `campaign_id` — mixing the two silently collapses every row
  onto one campaign.
- **Filter on `effective_status`, never on `amount_spent`.** Archived and deleted objects are omitted
  from unfiltered listings. `influencer direct video ad - Rheumatoid Arthritis` is ARCHIVED and
  vanishes from a ranged ad pull without
  `{"field":"ad.effective_status","operator":"IN","value":["ACTIVE","PAUSED","ADSET_PAUSED","CAMPAIGN_PAUSED","ARCHIVED","DELETED",…]}`.
- `ads_account_get_activity_logs` caps at the `limit` you pass and returns the **most recent** entries,
  so a 400-row pull over two months silently starts in late October. Page it in date windows and merge.
- Update the snapshot stamp in `index.html` — **four places**: the line-8 title comment, the masthead
  stamp, the `Snapshot data` comment above the arrays, and the hand-written intro paragraph — and here.

### Checks that must pass before publishing

All of these passed on this refresh except the one noted:

- `sum(ADS) == CAMP` per campaign on spend, impressions, clicks, installs and registrations — **12/12 exact**.
- `sum(DAILY excluding SNAP_DAY) == CAMP` per campaign — **12/12 exact**.
- `sum(AGE) == CAMP` per campaign — **12/12 exact**.
- `sum(ADAY) == DAILY` per campaign per day, for campaigns with `ADAY` coverage — **121/122**. The one
  exception is 25 Nov, campaign 11: Meta reports 12 installs at campaign level and only 11 across that
  campaign's ads. It is a gap inside Meta's own data, on the part day, and it is excluded from every
  headline. Do not patch it by hand.
- **Per-day campaign sums equal the `level: "ad_account"` daily totals on spend AND impressions** —
  **118/118 days exact**. This is the check that catches a missing archived campaign; run it last.
- Reach is excluded from every one of these checks on purpose: Meta de-duplicates it, so it can never
  be obtained by adding rows up.

## Campaigns not in the report

`India | Influencer Videos | App Registrations` (`120249585723440482`) is on the account, PAUSED, and
has **never delivered**: Meta returns no spend, impression or result fields for it at all. So it is
deliberately left out of `CID` and `CAMP`. Add it the day it spends, **appended to the end of `CID`** so
no historical row re-labels itself.

## Other notes

The page carries `noindex, nofollow` so it stays out of search results.

A live version of the same report — one that queries Meta each time it is opened — is kept as a private
Claude artifact rather than here.

Budget history worth keeping: both of the original campaigns went from ₹3,000/day to ₹1,000/day at
7:36 am on 27 Sep 2026 (activity log, Power Editor, actor Abhijeet Lenka), and the influencer ad set
`120249585873460482` moved to a ₹19.00 bid cap at 7:34 am the same morning. Anything from 27 Sep onward
is a different regime from the ₹6,000/day days.
