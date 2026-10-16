# Elixir ads reports

Client-facing performance reports for Elixir Social's Meta ads (account 1358051173168970).

- `index.html` — **Creative Ledger**, a lifetime creative-wise report snapshotted 16 October 2026, 8:05 AM IST.
  Self-contained: the figures are baked into the file, so it needs no network and no connector.
  Campaign rail, per-ad tables, day-by-day, age bands, and the GST-inclusive billing line.
  Defaults to the campaigns that are active; a switch shows all nine.

## Snapshot: 16 Oct 2026, 8:05 AM IST

Data window **31 Jul – 16 Oct 2026**; 16 Oct is the live part-day and is excluded
from every headline. Active-only (campaign 5 is the one still running):

Media spend ₹2,46,475.39 + 18% GST ₹44,365.57 = **₹2,90,840.96 billed**.
23,153 installs at **₹10.65** each, 10,184 registrations at **₹24.20** each,
44% of installs going on to register. Lifetime across all nine campaigns in the
report: ₹3,19,036.04, 26,130 installs, 11,219 registrations.

**The account was scaled roughly twelvefold in October, and got cheaper doing
it.** Campaign 5's daily budget went ₹1,000 → ₹8,000 on 3 Oct at 11:07 am and
₹8,000 → ₹12,000 on 4 Oct at 12:10 pm (activity log, actor Abhijeet Lenka; the
first change through the ads MCP server, the second through the Ads Manager iOS
app). Daily spend went from ~₹1,100 on 1–2 Oct to ₹8,600–₹14,800 a day from the 4th
onward. Over the fifteen settled October days the campaign took **₹1,53,862.03
for 16,331 installs (₹9.42) and 7,346 registrations (₹20.95)** — against
September's ₹84,964.54 for 6,225 installs (₹13.65) and 2,599 registrations
(**₹32.69**). Cost per registration fell by a third while spend rose twelvefold,
which is the opposite of what scaling usually does; say so to the client, and
keep watching it, because that is the number a further budget rise would spend.

**A new creative family, "Elixir 3.0", carries nearly all of it.** Ten ads
`E3-01` … `E3-10` were created and approved on **3 Oct between 11:58 am and
12:08 pm** and took **₹1,51,743.26 of October's ₹1,55,477.51 of ad-level spend
(97.6%)**. Three of them do the work:

| Creative | Spend | Installs | Each | Registrations | Each |
|---|---|---|---|---|---|
| E3-08a 3 medical apps to 1 | ₹88,095.42 | 9,762 | ₹9.02 | 4,367 | **₹20.17** |
| E3-09 Asking medicos which AI app they trust | ₹38,444.85 | 3,794 | ₹10.13 | 1,818 | ₹21.15 |
| E3-10 Rounds with the strictest senior | ₹23,387.98 | 2,511 | ₹9.31 | 1,069 | ₹21.88 |
| *(for contrast)* v5 - I am a med student | ₹54,351.01 | 3,962 | ₹13.72 | 1,734 | ₹31.34 |

The other seven E3 ads have ₹78–₹956 each: this is a 3-of-10 result, not a
ten-creative success. `E3-04` and `E3-08b` were paused on 15 Oct at 7:54 am and
`E3-06` deleted at 7:39 am, so the family is already being pruned.

**Campaign 8 (`India | MBBS Students - influencers`) is PAUSED** — switched off
on **3 Oct at 12:15 pm**, twelve minutes after the budget rise on campaign 5. It
closes at ₹7,700.08 for 386 installs and 160 registrations over 11 days
(24 Sep – 4 Oct), with a ₹0 / 1 install / 1 registration tail on 4 Oct from
delayed attribution. `Influencer - 11sep` was also switched off inside campaign
5 on 3 Oct at 12:14 pm; the influencer creatives are finished for now, and they
were the dearest line on the account (₹36.59 a registration).

**Revisions to days that were already published.** Meta moved four day rows
since the 29 Sep snapshot: 29 Sep was a ₹349.67 part-day and settled at ₹890.76
(+56 installs, +18 registrations) for campaign 5 and ₹13.49 → ₹56.17 for
campaign 8; 28 Sep gained ₹2.74; 22 Sep gained one registration on unchanged
spend. Nothing before 22 Sep moved. This is the usual ~48h settling, and it is
exactly why the part-day is quarantined.

**All five age bands register at nearly the same price** — ₹23.76 (25–34) to
₹26.07 (55–64) per registration, on spend running from ₹1,34,789 down to
₹8,010. There is no age band to cut and none to lean on; the spread is in the
creative, not the audience.

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

**India | MBBS Students - influencers** (`120249585873390482`) opened on 24 Sep 2026
on its own ₹3,000/day budget alongside the original ₹3,000/day campaign — account spend
roughly doubled that day, and that was a deliberate second campaign, not a budget change
on the first. It ran the same three influencer creatives and only `Influencer - 11sep`
ever got real delivery in it. **It is PAUSED since 3 Oct** (see the snapshot above), so
from 4 Oct onward campaign 5 is the whole account again.

**History — the ₹1,000/day regime (27 Sep to 2 Oct).** Budgets were cut on 27 Sep 2026
at 7:36 am: both campaigns went from ₹3,000/day to ₹1,000/day (activity log, Power Editor, actor Abhijeet Lenka), so the account cap fell
from ₹6,000/day to ₹2,000/day. **The first day under the cap was the cheapest normal day
in a fortnight**: 27 Sep settled at ₹1,939.44 for 64 registrations, ₹30.30 each, against
₹41.74 on the 26th at three times the spend. **28 Sep is not a fair comparison**: it
spent only ₹709.48 (31 registrations, ₹22.89 each) because delivery thinned all day and
all but stopped from 3 pm to 9 pm (₹2.74 across those six hours, hourly breakdown). It
resumed in the 9 pm hour, right after ₹30,000 was added to the prepaid balance at 9:32 pm
(activity log, "Money added to balance"). Watch the balance: a stall like that looks
like a cheap day in the tables. At 7:34 am the influencer ad set `120249585873460482`
also moved from "Automatically bid for actions" to "Optimize bid for actions" with a
**₹19.00 bid cap**. Anything from 27 Sep onward is a different regime; do not read it
against the ₹6,000/day days. `influencer - hasaan` was switched off in the main campaign
at 9:25 pm on 26 Sep and is still PAUSED in `ADS`. **That regime ended on 3 Oct** — read
27 Sep – 2 Oct against neither the ₹6,000/day days before it nor the ₹12,000/day days
after it. There are three regimes in this data now, and the page's own day table is the
only place they can be told apart.

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

`ANAME` grew from 8 to 19 entries at the 16 Oct refresh: indexes 0–7 are
untouched and `E3-01` … `E3-10` were appended at 8–18 (`E3-08` ships as two ads,
`08a` and `08b`). **`ANAME` is a flat list of strings** — if you write it with a
generic row-writer that iterates its argument, each name comes out as an array
of single characters and the page renders "E,3,-,0,9, ,A,s,k,i,n,g…" in the
creative-split sentence. Write it as
`"[" + ",".join(json.dumps(x) for x in ANAME) + "]"`, and read
the rendered page once before committing: that sentence is the one that shows
the damage.

## A deleted ad disappears from the listing — ask for ARCHIVED and DELETED

**This cost ₹54,458.71 at the 16 Oct refresh.** An unfiltered ad-level pull
returned ad rows for campaign 5 that summed **₹54,458.71 short of the campaign
total**. The reason is in the activity log: on **15 Oct at 7:39 am**
`v5 - I am a med student` and `E3-06 My entire AI stack` were both set to
*Deleted*, and Meta silently omits ARCHIVED and DELETED entities from a listing
that does not ask for them. `v5` is the second-largest creative on the account
(₹54,351.01), so the gap was the whole of it plus E3-06's ₹107.70.

Always pull ads **and** campaigns with the status filter spelled out:

    filtering: [{field: "effective_status",
                 operator: "IN",
                 value: ["ACTIVE","PAUSED","ARCHIVED","DELETED",
                         "PENDING_REVIEW","DISAPPROVED","CAMPAIGN_PAUSED",
                         "ADSET_PAUSED","IN_PROCESS","WITH_ISSUES"]}]

With it, `ADS` sums to ₹2,46,475.39 — the campaign total, to the paisa. A
deleted ad keeps its spend and its results forever, and a client who paid for it
is entitled to see it, so these rows stay in the report: `v5` is carried as
`ARCHIVED` and still shows its ₹31.34 a registration. Note also that
`filtering` on `amount_spent > 0` re-introduces the same bug by a different
route — filter locally instead.

## CAMP, DAILY, AGE and ADS can disagree on the live campaign — publish all four

At this refresh campaign 5 reads:

| Source | Spend | vs lifetime `CAMP` |
|---|---|---|
| `CAMP` (lifetime, `date_preset: "maximum"`) | ₹2,46,475.39 | — |
| `ADS` (lifetime, ad level) | ₹2,46,475.39 | **exact** |
| `DAILY` (per day, `time_increment: "1"`) | ₹2,46,498.33 | **+₹22.94** |
| `AGE` (lifetime, age breakdown) | ₹2,46,498.33 | **+₹22.94** |

The two per-slice pulls agree with each other and run ₹22.94, 526 impressions,
5 installs and 2 registrations above the lifetime aggregate. It is **not** the
part-day: re-pulling the age breakdown to 15 Oct gives the same +₹22.94. The
other eight campaigns reconcile exactly on all four sources.

**Do not fudge an array to close it.** Each number is what Meta returns for that
granularity, and the client can reproduce any of them in Ads Manager by opening
the matching view. Record the gap, keep the sources independent, and re-check it
when the campaign stops. Roughly twenty-three rupees on ₹2.46 lakh is 0.009%.

`ADAY` matches `DAILY` exactly on spend, installs and registrations for **49 of
campaign 5's 51 days**. The two that differ are 15 and 16 Oct, both inside
Meta's ~48h revision window. Campaign 8 is exact on all 11 of its days. Assert
this every refresh — it is the check that catches a mis-split creative.

## Campaigns not in the report

`India | Influencer Videos | App Registrations` (`120249585723440482`) is on the
account, PAUSED, and has **never delivered**: Meta returns no spend, impression or
result fields for it at all. So it is deliberately left out of `CID` and `CAMP`. Add it
the day it spends, **appended to the end of `CID`** so no historical row re-labels itself.
